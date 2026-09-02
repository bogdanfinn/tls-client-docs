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
    tls_client.WithClientProfile(profiles.Chrome_150),
    tls_client.WithProtocolRacing(),
)
```

### Using Protocol Racing with a Proxy

The HTTP/2 leg of the race runs over TCP, the HTTP/3 leg runs over QUIC, which is UDP. A proxy therefore has to be able to carry UDP, and of the supported proxy schemes only SOCKS5 can, through UDP ASSOCIATE.

So when you combine protocol racing with a proxy, that proxy has to be a `socks5://` or `socks5h://` proxy **and the proxy server has to support UDP ASSOCIATE**. Not every SOCKS5 provider enables it.

```go
client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(),
    tls_client.WithClientProfile(profiles.Chrome_150),
    tls_client.WithProtocolRacing(),
    tls_client.WithProxyUrl("socks5://user:pass@host:port"),
)
```

Any other proxy scheme is rejected with an error, both when the client is created and when you call `SetProxy` later. That is deliberate: an HTTP or SOCKS4 proxy can only carry the HTTP/2 leg, so the HTTP/3 leg would go out directly and expose your real IP address without any visible sign that it happened.

If you have to use an HTTP proxy, disable protocol racing or HTTP/3:

```go
client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(),
    tls_client.WithClientProfile(profiles.Chrome_150),
    tls_client.WithDisableHttp3(),
    tls_client.WithProxyUrl("http://user:pass@host:port"),
)
```

### Configuration Constraints

Protocol Racing cannot be used in combination with certain other options. The following configurations are invalid and will result in an error:

* `WithProtocolRacing()` + `WithDisableHttp3()` — Racing requires HTTP/3 to be enabled.
* `WithProtocolRacing()` + `WithForceHttp1()` — Racing requires HTTP/2 and HTTP/3.
* `WithProtocolRacing()` + a non-SOCKS5 proxy — Only SOCKS5 can tunnel the UDP traffic HTTP/3 needs.
* `WithDisableIPV4()` + `WithDisableIPV6()` — Cannot disable both IP versions.
* `WithCertificatePinning()` + `WithInsecureSkipVerify()` — Certificate pinning is incompatible with skipping verification.

#### Shared Library & Standalone API

When using the shared library or standalone API, set `"withProtocolRacing": true` in the request payload to enable protocol racing. The same configuration constraints apply, including the SOCKS5 requirement when a `"proxyUrl"` is set.

### Known Issues

Two limitations apply to protocol racing as of version 1.16.0. Both only affect clients that actually use HTTP/3, so a client without protocol racing is unaffected.

#### Most profiles have no HTTP/3 fingerprint

Only five of the shipped profiles carry HTTP/3 settings: `chrome_144`, `chrome_144_PSK`, `firefox_147`, `firefox_147_PSK` and `firefox_148`. Every other profile falls back to a minimal SETTINGS frame, so its HTTP/3 fingerprint does not resemble the browser it imitates:

```
chrome_144   1:65536;6:262144;7:100;51:1;GREASE|GREASE|984832|m,a,s,p
chrome_150   51:1|m,a,s,p
chrome_152   51:1|m,a,s,p
firefox_147  1:65536;7:20;727725890:0;16765559:1;51:1;8:1|GREASE|m,s,a,p
```

The TLS and HTTP/2 fingerprints of those profiles are unaffected; this is only about the HTTP/3 SETTINGS frame, the GREASE setting and the priority parameter.

If the HTTP/3 fingerprint matters for your target, use one of the five profiles above, or turn HTTP/3 off with `WithDisableHttp3()` so the request goes over HTTP/2 with a correct fingerprint.

#### Data race between the two racing legs

Both legs of a race are handed the same `*http.Request`. The HTTP/3 transport writes to its header map while the HTTP/2 transport reads from it, which the Go race detector reports as a data race. It is unlikely to be observed on an occasional request but shows up reliably when many requests race at the same time, and a concurrent map access can crash the process.

If you send a high volume of concurrent requests, prefer `WithDisableHttp3()` until this is fixed.
