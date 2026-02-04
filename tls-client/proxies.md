# Proxies

Proxies can be used by either setting them as client option `WithProxyUrl` when creating the TLS client or calling `SetProxy` on an exisiting client instance.

A proxy should be formatted like this: `http://username:password@host:port` or `socks5://username:password@host:port`

If you are using rotating Proxies (proxies with a static URL) then make sure to always call SetProxy with the same proxy again to let the client reconnect to the Proxy Server and receive a new IP-Address.

#### Shared Library & Standalone API

When you are using the shared library or the standalone api you can supply a proxy in the `"proxyUrl"` field and set `"isRotatingProxy"` to `true` or `false`.
