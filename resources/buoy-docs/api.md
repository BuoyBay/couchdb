---
title: "CouchDB API"
summary: "Public types, operations, error behavior, and limits for CouchDB."
category: "Reference"
order: 20
keywords: ["database", "documents", "attachments", "mango", "changes"]
symbols: ["CouchDBClient", "CouchDBAttachment", "CouchDBException", "CouchDBClient.GetDocument", "CouchDBClient.Find", "CouchDBClient.Changes", "CouchDBClient.PutDocumentWithAttachments", "EncodeComponent"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# CouchDB API

| API | Contract |
| --- | --- |
| CouchDBClient(baseURL, username, password) | Optional URL defaults to anonymous loopback port 5984; reverse-proxy prefixes are supported. |
| ServerInfo, ListDatabases, DatabaseInfo | Return JSON server/database metadata. |
| CreateDatabase, DeleteDatabase | Explicit database mutations; server errors propagate. |
| GetDocument, CreateDocument, PutDocument, DeleteDocument | JSON document operations; raw IDs/revisions are encoded by the client. |
| AllDocuments, QueryView | POST JSON options; the options object is not modified. |
| Find, CreateIndex | Mango requests; Find requires an object selector. |
| Changes, UUIDs, BulkDocuments | One changes response, requested IDs, or every bulk write acknowledgment. |
| GetDocumentRevision | HEAD and unquoted ETag as String; no body download. |
| GetAttachment, GetAttachmentWithType | Return Bytes or CouchDBAttachment, preserving binary data. |
| PutAttachment, DeleteAttachment | Raw single-attachment mutation using the current revision. |
| PutDocumentWithAttachments | Document plus Array Of CouchDBAttachment in one multipart request. |
| EncodeComponent | UTF-8 encoding for one URL component. |

## Errors and revisions

CouchDBException exposes Status, ErrorName, Reason, ResponseBody, and inherited ErrorCode. Non-2xx responses throw it; non-2xx HEAD errors have no response body. A successful HEAD with a missing or empty ETag also throws CouchDBException with the original 2xx Status/ErrorCode, empty ErrorName/Reason, and the client-generated ResponseBody `Response did not include an ETag revision`. Do not treat a caught exception as success solely because its status is 2xx. NetworkingException covers connection/TLS/timeouts, JSONException covers malformed successful JSON, and InvalidArgumentException covers local validation.

A 409 conflict is returned to the caller without retry or resolution. Names, document IDs, and revisions are raw strings; do not pre-encode them. _design and _local document prefixes keep their routing meaning.

## Read next

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
