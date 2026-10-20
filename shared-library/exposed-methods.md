# Exposed Methods

The shared library exposes the following endpoints / methods you can call. The payload is always a JSON string expect for the `freeMemory` method. You will find the methods also in use in the examples.&#x20;

#### request(payload: string) => string

This is the basic method you will use to do requests. For full detailed documentation about the payload please take a look at [Payload](payload.md). As you see here on other methods you have to turn the JSON Object into a JSON string before supplying it as the payload parameter.

#### getCookiesFromSession(payload: string) => string

Get all cookies which are in the provided session for a given URL. The payload should look like this:

```json
"{\"sessionId\": \"someExistingSessionId\", \"url\": \"urlToGetCookiesFor\"}"
```

#### addCookiesToSession(payload: string) => string

Add manual cookies to an existing session. The payload should look like this:

```json
"{\"sessionId\": \"someExistingSessionId\", \"url\": \"urlToAddCookiesFor\", \"cookies\": []}"
```

#### freeMemory(responseId: string) => void

Supply the `"id"` of a previous response as string in order to free the allocated memory of the previous response.

#### destroyAll() => string

Destroy all existing sessions in order to release allocated memory.

#### destroySession(payload: string) => string

Destroy a specific session in order to release allocated memory. The payload should look like this:

```json
"{\"sessionId\": \"someExistingSessionId\"}"
```


### WebSocket

The WebSocket methods follow the same lifecycle as `request`: a JSON string in, a JSON string out, then `freeMemory` with the `"id"` of the response. Errors use the normal error response with `status: 0` and the message in `body`. The handshake goes through the same uTLS dialer as `request`, so it has the same TLS fingerprint.

#### wsConnect(payload: string) => string

Opens a WebSocket connection and returns a `connectionId` for the other methods.

```json
{
  "tlsClientIdentifier": "chrome_133",
  "url": "wss://example.com/ws",
  "headers": { "User-Agent": "...", "Origin": "https://example.com" },
  "headerOrder": ["host", "upgrade", "connection", "user-agent", "origin"],
  "handshakeTimeoutMilliseconds": 10000,
  "proxyUrl": "http://user:pass@host:port"
}
```

Further optional fields: `sessionId`, `customTlsClient`, `readBufferSize`, `writeBufferSize`, `insecureSkipVerify`, `withRandomTLSExtensionOrder`. permessage-deflate is always offered in the handshake, as browsers do.

Without `sessionId` an HTTP/1.1 client is built for the connection and nothing is added to the session store. With `sessionId` the existing session is used as it is, so it must have been created with `forceHttp1: true`, otherwise the handshake fails when the server negotiates HTTP/2.

Response:

```json
{ "id": "...", "connectionId": "ws-uuid", "status": 101 }
```

#### wsRead(payload: string) => string

Returns the next message. `messageType` is `1` for text and `2` for binary, binary data is base64 encoded.

```json
{ "connectionId": "ws-uuid", "timeoutMilliseconds": 30000 }
```

```json
{ "id": "...", "connectionId": "ws-uuid", "messageType": 1, "data": "..." }
```

If no message arrives within `timeoutMilliseconds`, the error `body` is exactly `websocket read timeout` and the connection stays open. Any other read error, including a close from the peer, closes the connection and the `connectionId` becomes invalid. Without a timeout the call blocks until a message arrives or the connection is closed.

Text frames are not UTF-8 validated, invalid bytes become U+FFFD in the JSON response.

#### wsWrite(payload: string) => string

Sends a message. For `messageType` `2` the `data` has to be base64 encoded. `timeoutMilliseconds` is optional, `0` means no deadline. A failed write closes the connection.

```json
{ "connectionId": "ws-uuid", "messageType": 2, "data": "<base64>", "timeoutMilliseconds": 5000 }
```

```json
{ "id": "...", "connectionId": "ws-uuid", "success": true }
```

#### wsClose(payload: string) => string

Closes the connection and releases a blocked `wsRead` or `wsWrite`.

```json
{ "connectionId": "ws-uuid" }
```

```json
{ "id": "...", "success": true }
```

`destroySession` and `destroyAll` do not close WebSocket connections, call `wsClose` for each of them.

#### wsStats(payload: string) => string

Diagnostics of a live connection, it never touches the wire. `tcp` holds the kernel TCP_INFO of the socket and is only filled on Linux; with a proxy it describes the connection to the proxy.

```json
{ "connectionId": "ws-uuid" }
```

```json
{
  "id": "...", "connectionId": "ws-uuid", "ageMs": 1506,
  "messagesRead": 2, "messagesWritten": 1, "bytesRead": 34, "bytesWritten": 2,
  "msSinceLastRead": 1464, "msSinceLastWrite": 1503,
  "lastWriteDurationMs": 0, "maxWriteDurationMs": 0, "unreadPending": false,
  "localAddr": "...", "remoteAddr": "...",
  "tcp": { "state": 1, "retransmits": 0, "rtoMs": 233, "rttMs": 32, "unacked": 0 }
}
```

### Threading

`wsWrite` is safe alongside `wsRead` and other `wsWrite` calls on the same connection. A reader goroutine per connection answers pings even while nobody is inside `wsRead`. Messages are not buffered: if you stop calling `wsRead`, TCP backpressure applies.
