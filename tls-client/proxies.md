# Proxies

Proxies can be used by either setting them as client option `WithProxyUrl` when creating the TLS client or calling `SetProxy` on an exisiting client instance.

A proxy should be formatted like this: `http://username:password@host:port` or `socks5://username:password@host:port`

If you are using rotating Proxies (proxies with a static URL) then make sure to always call SetProxy with the same proxy again to let the client reconnect to the Proxy Server and receive a new IP-Address.

### Proxies and HTTP/3

HTTP/3 runs over QUIC, which is UDP. An HTTP or SOCKS4 proxy can only carry TCP, so it cannot tunnel HTTP/3 at all. Only SOCKS5 can, through UDP ASSOCIATE.

That is why a client with `WithProtocolRacing()` enabled requires a `socks5://` or `socks5h://` proxy whose server supports UDP ASSOCIATE. Any other scheme is rejected with an error, both when creating the client and when calling `SetProxy`. Without that check the HTTP/2 leg would go through the proxy while the HTTP/3 leg went out directly and exposed your real IP address.

Clients without protocol racing are unaffected and work with every supported proxy scheme.

#### Shared Library & Standalone API

When you are using the shared library or the standalone api you can supply a proxy in the `"proxyUrl"` field and set `"isRotatingProxy"` to `true` or `false`.
