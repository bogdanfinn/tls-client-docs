# WebSocket

The TLS-Client supports WebSocket connections with the same TLS fingerprinting as regular HTTP requests. This ensures consistent fingerprinting across both HTTP and WebSocket connections.

**Important:** WebSocket connections require HTTP/1.1. You **must** use `WithForceHttp1()` when creating the HTTP client.

### Usage (Go)

```go
// Create HTTP client with ForceHttp1 (required for WebSocket!)
client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(),
    tls_client.WithClientProfile(profiles.Chrome_133),
    tls_client.WithForceHttp1(),
)

// Create WebSocket with optional header ordering
headers := http.Header{
    "User-Agent": {"MyBot/1.0"},
    http.HeaderOrderKey: {"host", "upgrade", "connection", "user-agent"},
}

ws, err := tls_client.NewWebsocket(nil,
    tls_client.WithUrl("wss://example.com/ws"),
    tls_client.WithTlsClient(client),
    tls_client.WithHeaders(headers),
)

conn, err := ws.Connect(context.Background())
defer conn.Close()
```

### WebSocket Options

*   WithUrl

    ```
    WithUrl sets the WebSocket URL to connect to. Required.
    ```
*   WithTlsClient

    ```
    WithTlsClient sets the tls-client HttpClient to use for the WebSocket connection.
    The underlying dialer from this client will be used to establish the connection,
    preserving TLS fingerprinting and other client configurations. Required.
    ```
*   WithHeaders

    ```
    WithHeaders sets the HTTP headers to send during the WebSocket handshake.
    You can use the http.HeaderOrderKey to control header ordering.
    ```
*   WithReadBufferSize

    ```
    WithReadBufferSize sets the read buffer size for the WebSocket connection.
    ```
*   WithWriteBufferSize

    ```
    WithWriteBufferSize sets the write buffer size for the WebSocket connection.
    ```
*   WithHandshakeTimeoutMilliseconds

    ```
    WithHandshakeTimeoutMilliseconds sets the timeout for the WebSocket handshake in milliseconds.
    ```
*   WithCookiejar

    ```
    WithCookiejar sets the cookie jar to use for the WebSocket connection.
    ```
