# Client Options

When instantiating the TLS client you can define various options which are documented here:

*   WithClientProfile

    ```
    WithClientProfile configures a TLS client to use the specified client profile.
    ```
*   WithForceHttp1

    ```
    WithForceHttp1 configures a client to force HTTP/1.1 as the used protocol.
    ```
*   WithDisableHttp3

    ```
    WithDisableHttp3 configures a client to disable HTTP 3 as the used protocol. Will most likely fall back to HTTP 2
    ```
*   WithInsecureSkipVerify

    ```
    WithInsecureSkipVerify configures a client to skip SSL certificate verification.
    ```
*   WithTransportOptions

    ```
    WithTransportOptions configures a client to use the specified transport options.
    ```
*   WithProxyUrl

    ```
    WithProxyUrl configures a HTTP client to use the specified proxy URL.
    proxyUrl should be formatted as:
    "http://user:pass@host:port"
    ```
*   WithCharlesProxy

    ```
    WithCharlesProxy configures the HTTP client to use a local running charles as proxy.
    host and port can be empty, then default 127.0.0.1 and port 8888 will be used
    ```
*   WithCookieJar

    ```
    WithCookieJar configures a HTTP client to use the specified cookie jar.
    ```
*   WithServerNameOverwrite

    ```
    WithServerNameOverwrite configures a TLS client to overwrite the server name being used for certificate verification and in the client hello.
    This option does only work properly if WithInsecureSkipVerify is set to true in addition
    ```
*   WithTimeoutMilliseconds

    ```
    WithTimeoutMilliseconds configures an HTTP client to use the specified request timeout.
    timeout is the request timeout in milliseconds.
    ```
*   WithTimeoutSeconds

    ```
    WithTimeoutSeconds configures an HTTP client to use the specified request timeout.
    timeout is the request timeout in seconds.
    ```
*   WithTimeout

    ```
    WithTimeout configures an HTTP client to use the specified request timeout.
    timeout is the request timeout in seconds.
    Deprecated: use either WithTimeoutSeconds or WithTimeoutMilliseconds
    ```
*   WithNotFollowRedirects

    ```
    WithNotFollowRedirects configures an HTTP client to not follow HTTP redirects.
    ```
*   WithCustomRedirectFunc

    ```
    WithCustomRedirectFunc configures an HTTP client to use a custom redirect func.
    The redirect func have to look like that: func(req *http.Request, via []*http.Request) error
    Please only provide a custom redirect function if you know what you are doing.
    Check docs on net/http.Client CheckRedirect
    ```
*   WithRandomTLSExtensionOrder

    ```
    WithRandomTLSExtensionOrder configures a TLS client to randomize the order of TLS extensions being sent in the ClientHello.
    Placement of GREASE and padding is fixed and will not be affected by this.
    ```
*   WithCertificatePinning

    ```
    WithCertificatePinning enables SSL Pinning for the client and will throw an error if the SSL Pin is not matched.
    Please refer to https://github.com/tam7t/hpkp/#examples in order to see how to generate pins. The certificatePins are a map with the host as key.
    You can provide a BadPinHandlerFunc or nil as second argument. This function will be executed once a bad ssl pin is detected.
    ```
*   WithDebug

    ```
    WithDebug configures a client to log debugging information.
    ```
*   WithLocalAddr

    ```
    WithLocalAddr configures an HTTP client to use the specified local address.
    ```
*   WithDisableIPV4

    ```
    WithDisableIPV4 configures a dialer to use tcp6 network argument
    ```
*   WithDisableIPV6

    ```
    WithDisableIPV6 configures a dialer to use tcp4 network argument
    ```
*   WithCatchPanics

    ```
    WithCatchPanics configures a client to catch all go panics happening during a request and not print the stacktrace.
    ```
*   WithDefaultHeaders

    ```
    WithDefaultHeaders configures a client to use a set of default headers if none are specified on the request.
    ```
*   WithConnectHeaders

    ```
    WithConnectHeaders configures a client to use the specified headers for the CONNECT request
    ```

#### Shared Library & Standalone API

When using the shared library or standalone api you can find almost every client option as a separate field to turn on / turn off or configure in the request payload.

Please take a look at the Documentation about the [Payload](../shared-library/payload.md).
