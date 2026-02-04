# Protocol Racing

Protocol Racing allows the TLS-Client to race HTTP/3 (QUIC) and HTTP/2 (TCP) connections in parallel, similar to Chrome's "Happy Eyeballs" approach. Both connection types are started simultaneously and whichever connects first is used.

The client remembers which protocol worked for each host and uses it directly on subsequent requests. If the cached protocol fails, the client falls back to racing again.

### How it works

1. On the first request to a host, both HTTP/3 and HTTP/2 connections are started in parallel.
2. HTTP/2 is delayed by 300ms (same as Chrome) to give HTTP/3 a slight head start.
3. Whichever protocol succeeds first is used for the request.
4. The winning protocol is cached for the host and used directly on future requests.
5. If the cached protocol fails, the cache is cleared and racing restarts.

### Usage (Go)

```go
client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(),
    tls_client.WithClientProfile(profiles.Chrome_133),
    tls_client.WithProtocolRacing(),
)
```

### Configuration Constraints

Protocol Racing cannot be used in combination with certain other options. The following configurations are invalid and will result in an error:

* `WithProtocolRacing()` + `WithDisableHttp3()` — Racing requires HTTP/3 to be enabled.
* `WithProtocolRacing()` + `WithForceHttp1()` — Racing requires HTTP/2 and HTTP/3.
* `WithDisableIPV4()` + `WithDisableIPV6()` — Cannot disable both IP versions.
* `WithCertificatePinning()` + `WithInsecureSkipVerify()` — Certificate pinning is incompatible with skipping verification.

#### Shared Library & Standalone API

When using the shared library or standalone API, set `"withProtocolRacing": true` in the request payload to enable protocol racing. The same configuration constraints apply.
