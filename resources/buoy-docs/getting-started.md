---
title: "CouchDB getting started"
summary: "Install and use CouchDB with explicit configuration and resource ownership."
category: "Guides"
order: 10
keywords: ["database", "documents", "attachments", "mango", "changes"]
related: ["index.md", "api.md", "typed-api.md"]
---

# CouchDB getting started

Copy the couchdb archive into your project packages directory and use the import below. The example reads server metadata from a local CouchDB instance; it does not create or delete a database. Configure a different base URL and credentials in your application when needed.

Run network work away from the GUI thread. TimeoutSeconds defaults to 30 and must be positive. Do not mutate client configuration or attachment buffers concurrently with a request.

```buoy
Import couchdb

Sub Main()
    Dim client As New CouchDBClient()
    client.TimeoutSeconds = 10
    Try
        Dim info As JSONItem = client.ServerInfo()
        Print info.ToString()
    Catch e As CouchDBException
        Print e.Reason
    End Try
End Sub
```

## Writing and paging

Fetch a document before updating it, keep its _rev, and pass the object back with the same document ID. Successful writes return a new rev but do not update your object. Deletes require the current revision. Inspect every BulkDocuments result because HTTP success can still contain per-document errors.

Supply Mango bookmark values to subsequent Find calls; no method fetches all pages automatically. For Changes, persist last_seq and pass it as since. Longpoll requires a server timeout shorter than TimeoutSeconds.

## Attachments

CouchDBAttachment contains Name, Data, and ContentType. Raw uploads avoid base64 expansion. Multipart uploads combine the document and all attachment bytes into one PUT and preserve supplied attachment stubs; omitted stubs are removed. Uploads remain fully assembled in memory.

## Read next

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
