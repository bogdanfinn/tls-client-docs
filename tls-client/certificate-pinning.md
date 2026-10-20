# Certificate Pinning

You can enable certificate pinning for a TLS-Client instance. You need to provide a map of pins by host when you create the client. See the example code below

```
pins := map[string][]string{
   "bstn.com": {
      "NQvy9sFS99nBqk/nZCUF44hFhshrkvxqYtfrZq3i+Ww=",
      "4a6cPehI7OG6cuDZka5NDZ7FR8a60d3auda+sKfg4Ng=",
      "x4QzPSC810K5/cMjb05Qm4k3Bw5zBn4lTdO/nEW/Td4=",
   },
}

options := []tls_client.HttpClientOption{
   tls_client.WithCertificatePinning(pins, tls_client.DefaultBadPinHandler),
}

client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(), options...)
```

This example code shows how to enabled certificate pinning for bstn.com. Next to it you can supply a BadPinHandler function as second argument for the WithCertificatePinning option.

The default BadPinHandler looks like this. This function will be executed when a bad pin is detected:

```
var DefaultBadPinHandler = func(req *http.Request) {
   fmt.Println("this is the default bad pin handler")
}
```

#### Shared Library & Standalone API

When using the shared library you can supply certificate pinning settings via the `"certificatePinningHosts"` field. This should look similar to this:

```
"certificatePinningHosts": {
        "bstn.com": [
            "NQvy9sFS99nBqk/nZCUF44hFhshrkvxqYtfrZq3i+Ww=",
            "4a6cPehI7OG6cuDZka5NDZ7FR8a60d3auda+sKfg4Ng=",
            "x4QzPSC810K5/cMjb05Qm4k3Bw5zBn4lTdO/nEW/Td4=",
        ],
    },
```

#### Wildcards

You can define wildcards for subdomains for example like this

```
pins := map[string][]string{
   "*.bstn.com": {
      "NQvy9sFS99nBqk/nZCUF44hFhshrkvxqYtfrZq3i+Ww=",
      "4a6cPehI7OG6cuDZka5NDZ7FR8a60d3auda+sKfg4Ng=",
      "x4QzPSC810K5/cMjb05Qm4k3Bw5zBn4lTdO/nEW/Td4=",
   },
}

options := []tls_client.HttpClientOption{
   tls_client.WithCertificatePinning(pins, tls_client.DefaultBadPinHandler),
}

client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(), options...)
```

#### How to generate pins?

You can easilly generate pins with this helpful tool: [https://github.com/tam7t/hpkp](https://github.com/tam7t/hpkp) \
Just install the tool and run the following command against the site you want to generate pins for:

```
hpkp-pins -server=bstn.com:443
```

#### Custom pinner

If a hash comparison is not enough, for example to check the subject or the SAN entries of the peer certificate, implement the `CertificatePinner` interface and pass it with `WithCertificatePinner`. It replaces the built-in pinner and takes precedence over `WithCertificatePinning`.

```go
type myPinner struct{}

func (myPinner) Pin(conn *tls.UConn, host string) error {
   // conn.ConnectionState().PeerCertificates is available here
   return nil
}

client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(),
   tls_client.WithClientProfile(profiles.Chrome_152),
   tls_client.WithCertificatePinner(myPinner{}),
)
```

Neither pinner runs on HTTP/3 connections, because those use their own QUIC TLS stack. If you enable `WithProtocolRacing()` and need the check on every connection, add `WithDisableHttp3()`.

### Example

A runnable version is in [`./example/certificate_pinning`](https://github.com/bogdanfinn/tls-client/tree/master/example/certificate_pinning).
