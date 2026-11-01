---
title: "CouchDB"
summary: "Read and write CouchDB documents, views, queries, changes, and binary attachments."
category: "Overview"
order: 0
keywords: ["database", "documents", "attachments", "mango", "changes"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# CouchDB

CouchDB is a synchronous HTTP client with JSON responses and exact binary attachment support. It covers database and document operations, design-document views, normal and longpoll changes feeds, Mango queries/indexes, UUIDs, bulk writes, HEAD revisions, and multipart attachment uploads.

## Transport and deployment

JSON uses Buoy URLConnection. Binary, multipart, and HEAD requests use system libcurl on macOS/Linux and WinHTTP on Windows. Linux requires libcurl.so.4. The package supplies source and system-library link stubs, rather than bundled native runtime binaries.

Pass credentials as constructor arguments, never inside the URL. Anonymous loopback access is the default; use HTTPS for remote Basic authentication. TLS verification remains enabled and redirects are returned as errors. Configuration is not a credential store.

## Scope

Use a worker for GUI calls. Responses and multipart bodies are buffered in memory; there is no streaming file-transfer interface. Replication, cookie authentication, continuous/eventsource changes, and automatic conflict retries are not provided. Database changes and deletion occur only through explicit calls.

## Read next

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
