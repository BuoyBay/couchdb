# CouchDB for Buoy

A Buoy source client for CouchDB's HTTP API. Requests are synchronous;
use a worker thread for calls from a graphical application. The current release
covers databases, documents, design-document views, one-shot and longpoll changes
feeds, UUIDs, raw single-attachment download/upload/delete, HEAD revision checks,
multipart attachment uploads, Mango queries and indexes, and bulk writes.
Replication, continuous/eventsource changes streams, streaming file transfers,
and cookie authentication are outside this release's scope.

## Installation

Install the Git source package with BPM, or build `couchdb-0.3.0.buipkg` using
the native package builder described in the repository README. Put the sealed
archive in your application's `packages/` directory and use `Import couchdb`.
Keep the package archive available when compiling source consumers; it supplies
the system-library link stubs used by the binary transport.

JSON requests use Buoy's `URLConnection`. Binary attachment, HEAD, and multipart
requests use system libcurl on macOS/Linux and WinHTTP on Windows. Linux requires `libcurl.so.4`,
which Buoy's standard HTTP transport also requires. The package contains source
and link stubs, with no bundled native runtime binaries.

Construct `CouchDBClient(baseURL, username, password)`. All constructor arguments
are optional: the URL defaults to `http://127.0.0.1:5984`, with anonymous access.
The URL may contain a reverse proxy path prefix. Supply credentials as constructor
arguments rather than embedding them in the URL. Basic authentication sends
credentials on every request; use HTTPS for remote servers. TLS certificate
verification stays enabled. Redirects are returned as HTTP errors.

`TimeoutSeconds` defaults to 30 and must be positive. Each request uses a separate
connection; the client holds no persistent response state. Avoid changing timeout
settings concurrently with requests.

## API

All methods return the parsed `JSONItem` response. Empty successful responses
return JSON null. Database names, document IDs, and revisions are accepted as raw
strings and encoded by the client; do not pre-encode them. `_design/` and `_local/`
document prefixes retain their CouchDB routing meaning.

| Method | Arguments | Purpose |
| --- | --- | --- |
| `ServerInfo` | none | Server version and metadata |
| `ListDatabases` | none | List available databases |
| `DatabaseInfo` | database | Database metadata |
| `CreateDatabase` | database | Create a database; existing database errors propagate |
| `DeleteDatabase` | database | Permanently delete a database |
| `GetDocument` | database, documentID, revision = empty, includeAttachments = False | Fetch the latest or a specific revision, optionally including base64 attachment data |
| `CreateDocument` | database, document object | POST a document, optionally including `_id` |
| `PutDocument` | database, documentID, document object | Create or replace a document by ID |
| `PutDocumentWithAttachments` | database, documentID, document object, attachment array, revision = empty | Create/update the document and upload all attachment bytes in one multipart PUT |
| `DeleteDocument` | database, documentID, revision | Delete using the current revision |
| `AllDocuments` | database, options object | POST `_all_docs`; pass an empty object for defaults |
| `Find` | database, query object | Mango `_find`; requires an object-valued `selector` |
| `CreateIndex` | database, definition object | POST a full `_index` request |
| `BulkDocuments` | database, array of document objects | POST `_bulk_docs`, returning every individual result |
| `QueryView` | database, design document, view name, options object | POST a design-document view query |
| `Changes` | database, options object | Read `_changes` once (`normal` or `longpoll`) |
| `UUIDs` | count = 1 | GET `_uuids`; IDs are in the `uuids` array |
| `GetDocumentRevision` | database, documentID | HEAD request; returns the current revision `String` from `ETag` |
| `GetAttachment` | database, documentID, attachmentName, revision = empty | Download exact attachment bytes as `Bytes` |
| `GetAttachmentWithType` | database, documentID, attachmentName, revision = empty | Download a `CouchDBAttachment` with name, bytes, and server `Content-Type` |
| `PutAttachment` | database, documentID, `CouchDBAttachment`, revision = empty | PUT one raw attachment without resending the document |
| `DeleteAttachment` | database, documentID, attachmentName, revision | Delete one attachment using the current document revision |

Responses retain all CouchDB fields, including Mango `bookmark`, `warning`, and
execution statistics. Pass a previous bookmark in the next `Find` query to paginate;
the library doesn't automatically fetch all pages. `EncodeComponent` is also
exported for encoding individual UTF-8 URL components.

## Views

`QueryView(database, "calls", "by_date", options)` sends the options object as the
JSON body of `POST /{db}/_design/{ddoc}/_view/{view}`. CouchDB accepts every view
query parameter there, so keys of any JSON type need no URL encoding: set
`key`, `keys`, `start_key`/`end_key` (or the older `startkey`/`endkey`),
`startkey_docid`, `include_docs`, `descending`, `limit`, `skip`, `reduce`, `group`,
`group_level`, `inclusive_end`, and so on as ordinary JSON values. The design
document may be given with or without its `_design/` prefix. With `descending`,
CouchDB reverses the range: `start_key` is the newest key. To page, pass the
last row's key and ID as `startkey`/`startkey_docid` with `skip: 1`. Responses are
returned unchanged (`total_rows`, `offset`, `rows` with `id`, `key`, `value`, and
`doc` when requested). The options object is never modified.

## Changes feed

`Changes(database, options)` performs one request and returns the response with
`results`, `last_seq`, and (when limited) `pending`. Store `last_seq` and pass it
as `since` on the next call; `"now"` starts from the current sequence. Option
values become query parameters: strings are sent as-is (sequence IDs are opaque
strings), booleans as `true`/`false`, and numbers or other JSON as compact JSON.
A `doc_ids` array or `selector` object is instead sent in a POST body with the
matching built-in `_doc_ids`/`_selector` filter; use only one of these and don't
combine them with an explicit `filter`. Other filters (for example
`filter: "_view"` with `view: "calls/by_date"`, or a design-document filter) are
passed through.

`feed: "longpoll"` waits on the server until a change arrives. It requires a
numeric `timeout` in milliseconds shorter than `TimeoutSeconds`, so the client
does not time out first; an empty `results` array means the wait expired. Use a
worker thread and loop with the returned `last_seq`. `continuous` and
`eventsource` feeds are rejected because responses are not streamed.

## Single attachments

`GetAttachment` returns the attachment's exact bytes, including NUL and invalid
UTF-8, and `GetAttachmentWithType` also returns the server's `Content-Type`
(`application/octet-stream` if absent). Supply a revision to read an attachment
from an earlier, uncompacted revision. Downloads are held fully in memory; write
the `Bytes` to a file yourself if you cache it. A missing attachment throws
`CouchDBException` (404, `not_found`).

`PutAttachment` sends one `CouchDBAttachment` as the raw request body with its
content type, without fetching or resending the document. Supply the current
revision for an existing document; omitting it creates a new document containing
only that attachment. `DeleteAttachment` requires the current revision. Both return
a write acknowledgment with the new `rev`. Attachment names may contain `/` and
non-ASCII text; the client encodes them as a single path component.

`GetDocumentRevision` sends HEAD and returns the unquoted `ETag` revision without
downloading the body. A missing document throws `CouchDBException` with status
404 and an empty body (HEAD responses have none), so `ErrorName` is empty.
A successful HEAD response with a missing or empty `ETag` also throws
`CouchDBException`, retaining the 2xx status. In this case `ResponseBody` contains
the client-generated text `Response did not include an ETag revision`, and
`ErrorName` and `Reason` are empty. Check the exception as well as its status
before treating a revision lookup as successful.

## Revisions and write results

`PutDocument` sends the supplied object without modifying it. For an update, fetch
the document, change its fields, and pass the object (including `_rev`) back with
the same ID. An `_id` in the body must match the method's `documentID`.
A successful write returns an acknowledgment with `id` and `rev`; it does not
update your in-memory document. Keep the returned `rev` or fetch again before the
next update. Deletes require a nonempty revision.

Conflicting revisions produce HTTP 409. The library leaves resolution to the
caller and performs no automatic retries. Bulk writes are not atomic: even an
HTTP success can contain a conflict or another error for an individual document.
Inspect every array element for `error`. Include `_id` and `_rev` for updates,
and `_deleted: true` for bulk deletions.

## Multiple attachments in one request

`PutDocumentWithAttachments` sends **one** `PUT /{db}/{docid}` request with
`Content-Type: multipart/related`. It performs no preparatory GET and does not
send individual attachment requests. This follows CouchDB's
[multipart attachment protocol](https://docs.couchdb.org/en/stable/api/document/common.html#creating-multiple-attachments).

Build an `Array Of CouchDBAttachment`; each instance is constructed with
`CouchDBAttachment(name, data As Bytes, contentType = "application/octet-stream")`.
Pass your document's ordinary JSON fields and this array to the upload method.
The library generates `_attachments` entries with `follows: true`, content types,
and exact byte lengths, then writes binary MIME parts in the same order as those
entries. Text uses UTF-8 only when you explicitly create its `Bytes`; binary data
is sent unchanged, including NUL and invalid UTF-8 bytes. There is no base64
expansion in the upload.

The boundary is randomized and checked against the JSON and all attachment data;
collisions regenerate it. The implementation uses CRLF framing, a closing boundary,
and one content-type header per part. Names live in escaped JSON metadata rather
than MIME headers. Duplicate names, missing names/data, and control characters
in media types are rejected before sending. Empty files are supported; an empty
attachment array is an error (use `PutDocument` for a document-only write).

For updates, pass a document containing the current `_rev`, or supply the optional
`revision` argument. If both are supplied they must match exactly. The method
does not fetch or resolve revisions automatically. It preserves existing
`_attachments` stubs from your supplied document, replacing entries whose names
appear in the upload array and appending new entries. Fetch with the default
`GetDocument` arguments to obtain stubs. Retained inline base64 or unresolved
`follows` entries are rejected; provide their content as `CouchDBAttachment` values
instead. Omit an existing stub from the supplied document to remove that attachment.

The caller's JSON and attachment buffers are not modified. The write acknowledgment
contains the new `rev`; keep it for later updates. Input buffers and the client
configuration must not be mutated concurrently during upload.

Requests are assembled fully in memory, requiring memory for both the source
buffers and the assembled body. WinHTTP limits the assembled body to less than
4 GiB; Windows timeouts apply to each network phase. On macOS/Linux, libcurl's
timeout covers the entire transfer. Redirects are rejected and TLS verification
remains enabled on both transports.

[The file-upload example](../examples/couchdb_attachments.bui) reads each file
as `Bytes` and sends all files with a new document in one request. To run it:

```sh
buoy-run tools/build_couchdb.bui
buoy examples/couchdb_attachments.bui -o /tmp/couchdb-upload
COUCHDB_DATABASE=my_database /tmp/couchdb-upload my-document /path/to/photo.jpg /path/to/report.pdf
```

The database must already exist. The example uses the same credential environment
variables as the read-only example and defaults to `application/octet-stream` for
each file; set the constructor's third argument when the format is known.

## Errors

Catch `CouchDBException` for any non-2xx HTTP response. It exposes:

- `Status` and inherited `ErrorCode`: the HTTP status.
- `ErrorName` and `Reason`: CouchDB's JSON fields, or empty strings if absent.
- `ResponseBody`: the raw body returned by Buoy's HTTP transport, except for
  the client-generated missing-revision message described above.
- `Message`: a summary containing the HTTP status.

`NetworkingException` propagates for DNS, connection, TLS, and timeout failures.
`JSONException` propagates if a successful response contains malformed JSON.
Invalid local arguments throw `InvalidArgumentException`. Database name rules,
index definitions, and query semantics are validated by CouchDB itself.

## Verification


Maintainers run the private validation suite before release. See the repository README for public build instructions.

