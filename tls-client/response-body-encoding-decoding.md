# Response Body Encoding / Decoding

If you do **not** set an `accept-encoding` header, the library adds one for you unless compression is disabled. Since version 1.17.0 the added header is `gzip, deflate, br, zstd` (before: `gzip, deflate, br`).

On `http1` and `http2` the response body is decompressed automatically according to the `Content-Encoding` header of the response, whether you set `accept-encoding` yourself or not. Supported are `gzip`, `deflate`, `br` and `zstd`. The decoded response no longer carries `Content-Encoding` and `Content-Length`. Before version 1.18.0, `http1` only decompressed when the library added the header itself or your `accept-encoding` contained `gzip`.

On `http3` the body is only decompressed when the library added the header itself, and then only `gzip`.

`DecompressBody` is an exported function you can use when the automatic decompression is disabled (see below). On a response that was already decoded it returns the body unchanged, so calling it does no harm. See the following example on how do decompress the response body manually

```go
req, err := http.NewRequest(http.MethodGet, "https://tls.browserleaks.com/json", nil)
if err != nil {
    log.Println(err)
    goreturn
}

req.Header = http.Header{
    "accept":          {"*/*"},
    "accept-encoding": {"gzip"},
    "accept-language": {"de-DE,de;q=0.9,en-US;q=0.8,en;q=0.7"},
    "user-agent":      {"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36"},
    http.HeaderOrderKey: {
        "accept",
        "accept-encoding",
        "accept-language",
        "user-agent",
    },
}

resp, err := client.Do(req)

if err != nil {
    log.Println(err)
    return
}

defer resp.Body.Close()

decomBody := http.DecompressBody(resp)

all, err := ioutil.ReadAll(decomBody)
if err != nil {
    log.Println(err)
    return
}
log.Println(string(all))
```

It is possible to disable the automatic decompression on http1, http2 and http3 by adding the following option to the http client

```go
	tls_client.WithTransportOptions(&tls_client.TransportOptions{
		DisableCompression: true,
	}),
```

#### Automatic Charset Detection

When using the shared library or standalone API, the response body charset is automatically detected and decoded based on the `Content-Type` response header (e.g. `charset=euc-kr`, `charset=shift_jis`, etc.). This means you no longer need to handle specific encodings manually — the correct character encoding is applied automatically for all text responses.

This behavior is active whenever `isByteResponse` is `false` (the default). If you need the raw bytes, set `isByteResponse: true`.

#### Shared Library & Standalone API

When you are using the shared library or standalone api application the response body will always be decompressed and the charset will be automatically detected and decoded.

### Example

A runnable version is in [`./example/decompress`](https://github.com/bogdanfinn/tls-client/tree/master/example/decompress).
