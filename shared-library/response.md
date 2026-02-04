# Response

The Response of a call against the shared library looks like this

```
{
  "id": "some response identifier",
  "sessionId": "some reusable sessionId if provided on the request",
  "status": 200,
  "target": "the target url",
  "body": "The Response as string here or the error message",
  "headers": {},
  "cookies": {},
  "usedProtocol": "h2"
}
```

In case of an unexpected error the `"status"` will be 0

The `"id"` is necessary when you want to free the memory used to generate this response. Take a look at [Memory Issues](memory-issues.md).

The `"sessionId"` can be reused to keep the cookies from the previous requests in this session.

The `"status"` indicates the status code of the remote response.

The `"body"` is the response body from the remote response.

The `"headers"` are the headers returned from the remote response.

The `"cookies"` are the cookies returned from the remote response.

The `"usedProtocol"` indicates which protocol was used for the request (e.g. `"h2"`, `"HTTP/2.0"`, `"HTTP/1.1"`).
