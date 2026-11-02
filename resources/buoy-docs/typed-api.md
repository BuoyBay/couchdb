---
title: "couchdb typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["CouchDBAttachment", "CouchDBBinaryResponse", "CouchDBClient", "CouchDBCurlStatus", "CouchDBException", "CouchDBTransportState", "CouchDBURLComponents"]
dependencies: []
symbols: ["CouchDBAttachment", "CouchDBBinaryResponse", "CouchDBClient", "CouchDBCurlStatus", "CouchDBException", "CouchDBTransportState", "CouchDBURLComponents"]
related: ["api.md", "getting-started.md", "index.md"]
---

# couchdb typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## CouchDBAttachment

`Public Class CouchDBAttachment`

One named binary attachment. The upload method generates metadata and MIME parts.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Name` | `String` | No | No | — | — |
| `ContentType` | `String` | No | No | — | — |
| `Data` | `Bytes` | No | No | — | — |

### Constructor

`Public Sub Constructor(name As String, data As Bytes, contentType As String = "application/octet-stream")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |
| `data` | `Bytes` | `ByVal` | Required |
| `contentType` | `String` | `ByVal` | `"application/octet-stream"` |

Returns: no value.

## CouchDBException

`Public Class CouchDBException Inherits Exception`

An HTTP failure, including CouchDB's error fields and the unmodified response.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Status` | `Integer` | No | No | — | — |
| `ErrorName` | `String` | No | No | — | — |
| `Reason` | `String` | No | No | — | — |
| `ResponseBody` | `String` | No | No | — | — |

### Constructor

`Public Sub Constructor(status As Integer, body As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `status` | `Integer` | `ByVal` | Required |
| `body` | `String` | `ByVal` | Required |

Returns: no value.

### EncodeComponent

`Public Function EncodeComponent(value As String) As String`

Encode one URL component using UTF-8 bytes and RFC 3986 unreserved characters.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `value` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `String` |

## CouchDBClient

`Public Class CouchDBClient`

Reusable client for a server URL, optionally including a reverse proxy prefix.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `TimeoutSeconds` | `Integer` | No | No | `30` | — |

### Constructor

`Public Sub Constructor(baseURL As String = "http://127.0.0.1:5984", username As String = "", password As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `baseURL` | `String` | `ByVal` | `"http://127.0.0.1:5984"` |
| `username` | `String` | `ByVal` | `""` |
| `password` | `String` | `ByVal` | `""` |

Returns: no value.

### ServerInfo

`Public Function ServerInfo() As JSONItem`

No parameters.

#### Returns

| Type |
| --- |
| `JSONItem` |

### ListDatabases

`Public Function ListDatabases() As JSONItem`

No parameters.

#### Returns

| Type |
| --- |
| `JSONItem` |

### DatabaseInfo

`Public Function DatabaseInfo(database As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CreateDatabase

`Public Function CreateDatabase(database As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### DeleteDatabase

`Public Function DeleteDatabase(database As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### GetDocument

`Public Function GetDocument(database As String, documentID As String, revision As String = "", includeAttachments As Boolean = False) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `revision` | `String` | `ByVal` | `""` |
| `includeAttachments` | `Boolean` | `ByVal` | `False` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CreateDocument

`Public Function CreateDocument(database As String, document As JSONItem) As JSONItem`

Create with a generated ID, or the _id supplied in the document.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `document` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### PutDocument

`Public Function PutDocument(database As String, documentID As String, document As JSONItem) As JSONItem`

Create or replace by ID. Existing documents require their current _rev.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `document` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### PutDocumentWithAttachments

`Public Function PutDocumentWithAttachments(database As String, documentID As String, document As JSONItem, attachments As Array Of CouchDBAttachment, revision As String = "") As JSONItem`

Create or update a document and upload all supplied attachments in one PUT. Existing _attachments stubs are retained; supplied names replace those entries.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `document` | `JSONItem` | `ByVal` | Required |
| `attachments` | `Array Of CouchDBAttachment` | `ByVal` | Required |
| `revision` | `String` | `ByVal` | `""` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### GetAttachment

`Public Function GetAttachment(database As String, documentID As String, attachmentName As String, revision As String = "") As Bytes`

Download one attachment's exact bytes. Supply a revision to read an older one.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `attachmentName` | `String` | `ByVal` | Required |
| `revision` | `String` | `ByVal` | `""` |

#### Returns

| Type |
| --- |
| `Bytes` |

### GetAttachmentWithType

`Public Function GetAttachmentWithType(database As String, documentID As String, name As String, revision As String = "") As CouchDBAttachment`

Download one attachment as name, exact bytes, and the server's Content-Type.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `name` | `String` | `ByVal` | Required |
| `revision` | `String` | `ByVal` | `""` |

#### Returns

| Type |
| --- |
| `CouchDBAttachment` |

### PutAttachment

`Public Function PutAttachment(database As String, documentID As String, attachment As CouchDBAttachment, revision As String = "") As JSONItem`

Upload one attachment as raw bytes without resending the document body. Omit the revision only when creating a new document.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `attachment` | `CouchDBAttachment` | `ByVal` | Required |
| `revision` | `String` | `ByVal` | `""` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### DeleteAttachment

`Public Function DeleteAttachment(database As String, documentID As String, name As String, revision As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `name` | `String` | `ByVal` | Required |
| `revision` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### GetDocumentRevision

`Public Function GetDocumentRevision(database As String, documentID As String) As String`

Return the current revision from a HEAD request without downloading the document.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `String` |

### QueryView

`Public Function QueryView(database As String, designDocument As String, viewName As String, options As JSONItem) As JSONItem`

Query a design document view. Options are the documented view parameters (key, keys, start_key, end_key, descending, include_docs, limit, skip, reduce, group, ...) sent as a JSON POST body, so keys of any JSON type need no URL encoding.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `designDocument` | `String` | `ByVal` | Required |
| `viewName` | `String` | `ByVal` | Required |
| `options` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### Changes

`Public Function Changes(database As String, options As JSONItem) As JSONItem`

Read the changes feed once (normal or longpoll). doc_ids or selector options are sent in a POST body with the matching built-in filter; every other option becomes a query parameter.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `options` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### UUIDs

`Public Function UUIDs(count As Integer = 1) As JSONItem`

Ask the server for count new document IDs (returned in the "uuids" array).

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `count` | `Integer` | `ByVal` | `1` |

#### Returns

| Type |
| --- |
| `JSONItem` |

### DeleteDocument

`Public Function DeleteDocument(database As String, documentID As String, revision As String) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documentID` | `String` | `ByVal` | Required |
| `revision` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### AllDocuments

`Public Function AllDocuments(database As String, options As JSONItem) As JSONItem`

Pass CouchDB _all_docs options as a JSON object (keys, limit, include_docs, etc.).

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `options` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### Find

`Public Function Find(database As String, query As JSONItem) As JSONItem`

Pass the complete Mango request including selector and optional bookmark.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `query` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### CreateIndex

`Public Function CreateIndex(database As String, definition As JSONItem) As JSONItem`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `definition` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

### BulkDocuments

`Public Function BulkDocuments(database As String, documents As JSONItem) As JSONItem`

Returns per-document results. A successful HTTP response can contain errors.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `database` | `String` | `ByVal` | Required |
| `documents` | `JSONItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `JSONItem` |

## CouchDBBinaryResponse

`Public Class CouchDBBinaryResponse`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Status` | `Integer` | No | No | — | — |
| `Body` | `Bytes` | No | No | `New Bytes()` | — |
| `RawHeaders` | `Bytes` | No | No | `New Bytes()` | — |

### HeaderValue

`Public Function HeaderValue(name As String) As String`

The named header from the final response (after any 1xx), or empty if absent.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `String` |

## CouchDBURLComponents

`Sequential Structure CouchDBURLComponents`

### Fields

| Name | Type | Value / Default | Description |
| --- | --- | --- | --- |
| `StructSize` | `UInt32` | — | — |
| `SchemeText` | `Ptr` | — | — |
| `SchemeLength` | `UInt32` | — | — |
| `Scheme` | `Int32` | — | — |
| `HostText` | `Ptr` | — | — |
| `HostLength` | `UInt32` | — | — |
| `Port` | `UInt16` | — | — |
| `UserText` | `Ptr` | — | — |
| `UserLength` | `UInt32` | — | — |
| `PasswordText` | `Ptr` | — | — |
| `PasswordLength` | `UInt32` | — | — |
| `PathText` | `Ptr` | — | — |
| `PathLength` | `UInt32` | — | — |
| `ExtraText` | `Ptr` | — | — |
| `ExtraLength` | `UInt32` | — | — |

### CouchDBInitializeTransport

`Public Sub CouchDBInitializeTransport()`

WinHTTP needs no process-wide setup; present so callers can initialize on every platform.

Availability: `#If TargetWindows Then`

No parameters.

Returns: no value.

### SendCouchDBBinary

`Public Function SendCouchDBBinary(method As String, url As String, headers As String, body As Bytes, timeout As Integer) As CouchDBBinaryResponse`

Availability: `#If TargetWindows Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `method` | `String` | `ByVal` | Required |
| `url` | `String` | `ByVal` | Required |
| `headers` | `String` | `ByVal` | Required |
| `body` | `Bytes` | `ByVal` | Required |
| `timeout` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `CouchDBBinaryResponse` |

### CouchDBInitializeTransport (overload 2)

`Public Sub CouchDBInitializeTransport()`

Runs curl_global_init once. libcurl before 7.84 does not make that call thread-safe, so call this on the main thread before starting worker requests. Later calls are no-ops.

Availability: `#Else`

No parameters.

Returns: no value.

### SendCouchDBBinary (overload 2)

`Public Function SendCouchDBBinary(method As String, url As String, headers As String, body As Bytes, timeout As Integer) As CouchDBBinaryResponse`

Availability: `#Else`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `method` | `String` | `ByVal` | Required |
| `url` | `String` | `ByVal` | Required |
| `headers` | `String` | `ByVal` | Required |
| `body` | `Bytes` | `ByVal` | Required |
| `timeout` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `CouchDBBinaryResponse` |

## CouchDBCurlStatus

`Sequential Structure CouchDBCurlStatus`

### Fields

| Name | Type | Value / Default | Description |
| --- | --- | --- | --- |
| `Value` | `Integer` | — | — |

## CouchDBTransportState

`Class CouchDBTransportState`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Initialized` | `Boolean` | No | Yes | `False` | — |
