# Request Headers

### Header Sorting

You can sort your request headers per request by just providing a special header key (`"Header-Order:"`) with a list of header keys as sort order. Please keep in mind that it does not matter if your header keys are uppercase or all lowercase. For the header order **you have to define the header keys in the order list all lowercase.**

```go
req.Header = http.Header{
		"header4": {`value4`},
		"header2": {"value2"},
		"header1": {"value1"},
		"header3": {"value3"},
		"Header-Order:": {
			"header1",
			"header2",
			"header3",
			"header4",
		},
	}
```

The header order is applied for HTTP/1.1, HTTP/2 and HTTP/3 requests alike.

#### Shared Library & Standalone API

When you are using the shared library or the standalone api this works exactly the same. You can provide the header order with the same Header Order key.&#x20;

### Header Key Capitalization

Sometimes people are confused when to write header keys all lowercase and when to write them with first uppercase letter. For HTTP2 requests header keys are usually all lowercase. For HTTP1 Requests header keys are usually first uppercase letter. Just as a rule of thumb:

`"accept-encoding"` => HTTP2

`"Accept-Encoding"` => HTTP1

### User-Agent

If you do not set a `User-Agent` header, a default one is sent: `Go-http-client/1.1` for HTTP/1.1, `Go-http-client/2.0` for HTTP/2 and `quic-go HTTP/3` for HTTP/3.

To send no `User-Agent` header at all, set the header to `nil` or to the empty string. This works for HTTP/1.1, HTTP/2 and HTTP/3 requests alike. It is not possible to send a `User-Agent` header with an empty value.

```go
req.Header = http.Header{
		"accept":     {"*/*"},
		"user-agent": {""},
	}
```

The same applies to the headers of the CONNECT request to a proxy, which you configure with `WithConnectHeaders`.

#### Shared Library & Standalone API

Set the `user-agent` key in `headers` to an empty string to send no `User-Agent` header. For the CONNECT request use an empty string or an empty list in `connectHeaders`.

### Example

A runnable version is in [`./example/headers`](https://github.com/bogdanfinn/tls-client/tree/master/example/headers).
