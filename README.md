# Query CouchDB documents, views, changes and attachments

A Buoy source client for CouchDB's HTTP API. Requests are synchronous;
use a worker thread for calls from a graphical application. The current release
covers databases, documents, design-document views, one-shot and longpoll changes
feeds, UUIDs, raw single-attachment download/upload/delete, HEAD revision checks,
multipart attachment uploads, Mango queries and indexes, and bulk writes.
Replication, continuous/eventsource changes streams, streaming file transfers,
and cookie authentication are outside this release's scope.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add couchdb --git https://github.com/BuoyBay/couchdb.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer couchdb
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```

The generated agent files and editor settings stay local and are ignored by Git.

## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
