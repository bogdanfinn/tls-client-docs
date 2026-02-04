# Response Body Encoding / Decoding

If you are specifying `accept-encoding` header yourself and you are on a `http1` connection than you have to take care of the response body decompression yourself. It is not done automatically.&#x20;

Only if you are **not** adding `accept-encoding` header then the library adds it for you if not explicit disabled and also handles the decompression automatically.

On `http2` the automatic decompression should always be in place according to the `content-type` Header on the Response.

`DecompressBody` is an exported function you can use. See the following example on how do decompress the response body manually

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
    "user-agent":      {"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36"},
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

It is possible to disable the automatic decompression on http2 by adding the following option to the http client

```go
	tls_client.WithTransportOptions(&tls_client.TransportOptions{
		DisableCompression: true,
	}),
```

#### Shared Library & Standalone API

When you are using the shared library or standalone api application the response body will always be decompressed.
