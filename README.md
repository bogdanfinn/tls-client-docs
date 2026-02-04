# TLS-Client

## Preface

This TLS Client is built upon [https://github.com/Carcraftz/fhttp](https://github.com/Carcraftz/fhttp) and [https://github.com/Carcraftz/utls](https://github.com/Carcraftz/utls) as well as [https://github.com/refraction-networking/utls](https://github.com/refraction-networking/utls). Big thanks to all contributors so far. Sadly it seems that the original repositories are not maintained anymore and the original \`utls\` repository is not enough without custom modifications.

## What is TLS Fingerprinting?

Some people think it is enough to change the user-agent header of a request to let the server think that the client requesting a resource is a specific browser. Nowadays this is not enough, because the server might use a technique to detect the client browser which is called TLS Fingerprinting.

Even tho this article is about TLS Fingerprinting in NodeJS it well describes the technique in detail:\
[https://httptoolkit.tech/blog/tls-fingerprinting-node-js/#how-does-tls-fingerprinting-work](https://httptoolkit.tech/blog/tls-fingerprinting-node-js/#how-does-tls-fingerprinting-work)

## Why is this library needed?

With this library you are able to create a http client implementing an interface which is similar to golangs net/http client interface. This TLS Client allows you to specify the client profile (Browser) you want to use, when requesting a server.

The interface of the HTTP Client looks like the following and extends the base net/http Client interface by some useful functions. Most likely you will use the `Do()` function like you did before with golangs net/http Client.

```go
type HttpClient interface {
    GetCookies(u *url.URL) []*http.Cookie
    SetCookies(u *url.URL, cookies []*http.Cookie)
    SetCookieJar(jar http.CookieJar)
    GetCookieJar() http.CookieJar
    SetProxy(proxyUrl string) error
    GetProxy() string
    SetFollowRedirect(followRedirect bool)
    GetFollowRedirect() bool
    CloseIdleConnections()
    Do(req *http.Request) (*http.Response, error)
    Get(url string) (resp *http.Response, err error)
    Head(url string) (resp *http.Response, err error)
    Post(url, contentType string, body io.Reader) (resp *http.Response, err error)

    GetBandwidthTracker() bandwidth.BandwidthTracker
    GetDialer() proxy.ContextDialer
    GetTLSDialer() TLSDialerFunc
}
```

The methods should be more or less self explanatory.
