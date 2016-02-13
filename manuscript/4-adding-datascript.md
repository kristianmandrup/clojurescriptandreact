# Adding DataScript

DataScript is a client Database similar to Datomic, developed by @tonsky. Why only have a Database on the server?
As most of the application logic in an SPA is now on the client, it makes sense to have an in-memory DB which acts as a cache
to the server DB. This has a number of benefits:

- Minimize request/response data roundtrips to the server
- Offline first
- ...