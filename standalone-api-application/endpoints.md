# Endpoints

You need to do a POST Request against the running API with the following JSON Request Body.

#### Forward Request `/api/forward`

```
curl --location --request POST '127.0.0.1:8080/api/forward' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
    "tlsClientIdentifier": "chrome_150",
    "requestUrl": "https://tls.peet.ws/api/all",
    "requestMethod": "GET"
}'
```

#### Get Cookies from Session `/api/cookies`

```
curl --location --request POST '127.0.0.1:8080/api/cookies' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
  "sessionId":"my-custom-sessionid",
  "url":"http://some-url.com"
}'
```

#### Free Single Session `/api/free-session`

```
curl --location --request POST '127.0.0.1:8080/api/free-session' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
  "sessionId":"my-custom-sessionid"
}'
```

#### Free All Sessions `/api/free-all`

```
curl --location --request GET '127.0.0.1:8080/api/free-all' \
--header 'x-api-key: my-auth-key-1'
```

#### WebSocket `/api/ws/*`

The WebSocket endpoints take the same JSON payloads and return the same JSON responses as the shared library methods `wsConnect`, `wsRead`, `wsWrite`, `wsClose` and `wsStats`. See [Exposed Methods](../shared-library/exposed-methods.md#websocket) for all fields. Errors use the normal error response with `status: 0` and the message in `body`.

| Endpoint | Shared library method |
| --- | --- |
| `POST /api/ws/connect` | `wsConnect` |
| `POST /api/ws/read` | `wsRead` |
| `POST /api/ws/write` | `wsWrite` |
| `POST /api/ws/close` | `wsClose` |
| `POST /api/ws/stats` | `wsStats` |

```
curl --location --request POST '127.0.0.1:8080/api/ws/connect' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
  "tlsClientIdentifier": "chrome_150",
  "url": "wss://example.com/ws"
}'
```

```
curl --location --request POST '127.0.0.1:8080/api/ws/read' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
  "connectionId": "ws-uuid",
  "timeoutMilliseconds": 30000
}'
```

`/api/ws/read` is a long poll: one HTTP request per message. Unlike the shared library, the read is always bounded: a `timeoutMilliseconds` of `0` or above the configured `api.timeout.write` minus one second is capped to that value. This way the read never outlives the HTTP request, which would consume a message nobody receives. On timeout the error `body` is `websocket read timeout` and the connection stays open, so just read again.
