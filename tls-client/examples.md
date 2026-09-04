# Examples

Every example is one focused, standalone program under `./example/<topic>/` in the repository, runnable with `go run ./example/<topic>` and meant to be copied from directly.

### Requests

* [`./example/basic`](https://github.com/bogdanfinn/tls-client/tree/master/example/basic) - GET Request
* [`./example/post`](https://github.com/bogdanfinn/tls-client/tree/master/example/post) - POST Request
* [`./example/headers`](https://github.com/bogdanfinn/tls-client/tree/master/example/headers) - Custom Headers & [Header Order](request-headers.md)
* [`./example/cookies`](https://github.com/bogdanfinn/tls-client/tree/master/example/cookies) - [Cookie Jar](cookiejar.md)
* [`./example/redirects`](https://github.com/bogdanfinn/tls-client/tree/master/example/redirects) - Switching the Redirect Following Behavior
* [`./example/download`](https://github.com/bogdanfinn/tls-client/tree/master/example/download) - Downloading Image / Files
* [`./example/decompress`](https://github.com/bogdanfinn/tls-client/tree/master/example/decompress) - Manual [Response Body Decompression](response-body-encoding-decoding.md)

### Fingerprints

* [`./example/custom_profile`](https://github.com/bogdanfinn/tls-client/tree/master/example/custom_profile) - [Custom Client Profile](custom-client-profile.md) from a JA3 string, including the `trust_anchors` payload
* [`./example/custom_spec`](https://github.com/bogdanfinn/tls-client/tree/master/example/custom_spec) - Hand built `ClientHelloSpec` for extensions a JA3 string cannot express (PSK, ALPS)
* [`./example/session_tickets`](https://github.com/bogdanfinn/tls-client/tree/master/example/session_tickets) - Disabling TLS Session Tickets

### Connection

* [`./example/proxy`](https://github.com/bogdanfinn/tls-client/tree/master/example/proxy) - [Proxy](proxies.md) & Proxy Rotation
* [`./example/protocol_racing`](https://github.com/bogdanfinn/tls-client/tree/master/example/protocol_racing) - [Protocol Racing](protocol-racing.md) (HTTP/3 vs HTTP/2) and its SOCKS5 requirement
* [`./example/websocket`](https://github.com/bogdanfinn/tls-client/tree/master/example/websocket) - [WebSocket](websocket.md) with the client's TLS fingerprint
* [`./example/certificate_pinning`](https://github.com/bogdanfinn/tls-client/tree/master/example/certificate_pinning) - [Certificate Pinning](certificate-pinning.md)

### Concurrency

* [`./example/concurrency`](https://github.com/bogdanfinn/tls-client/tree/master/example/concurrency) - One client shared by many goroutines, decoding every response, with heap statistics and an optional pprof heap profile
* [`./example/client_per_worker`](https://github.com/bogdanfinn/tls-client/tree/master/example/client_per_worker) - The same setup with a client per worker instead of a shared one, counting the dials that a shared client would have avoided
* [`./example/connection_reuse`](https://github.com/bogdanfinn/tls-client/tree/master/example/connection_reuse) - Verifies that one TCP connection is reused across requests, counting dials and cross-checking with `httptrace`

### Observability

* [`./example/bandwidth`](https://github.com/bogdanfinn/tls-client/tree/master/example/bandwidth) - Bandwidth Tracking
* [`./example/hooks`](https://github.com/bogdanfinn/tls-client/tree/master/example/hooks) - Pre-Request & Post-Response Hooks

For the shared library and the standalone API the same topics are covered per language under `cffi_dist/`, see the [JavaScript](../shared-library/javascript/examples.md), [Python](../shared-library/python/examples.md), [TypeScript](../shared-library/typescript/examples.md) and [C#](../shared-library/c/examples.md) example pages.
