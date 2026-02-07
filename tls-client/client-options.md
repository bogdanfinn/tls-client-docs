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
*   WithProtocolRacing

    ```
    WithProtocolRacing configures a client to race HTTP/3 (QUIC) and HTTP/2 (TCP) connections in parallel.
    Similar to Chrome's "Happy Eyeballs" approach, this starts both connection types simultaneously
    and uses whichever connects first.
    The client will remember which protocol worked for each host and use it directly on subsequent requests.
    This option is ignored if WithForceHttp1 or WithDisableHttp3 is set.
    ```
*   WithBandwidthTracker

    ```
    WithBandwidthTracker configures a client to track the bandwidth used by the client.
    You can retrieve the tracker via GetBandwidthTracker() and call GetTotalBandwidth(), GetWriteBytes(), GetReadBytes() or Reset() on it.
    ```
*   WithDialer

    ```
    WithDialer configures an HTTP client to use the specified dialer. This allows the use of a custom DNS resolver.
    ```
*   WithProxyDialerFactory

    ```
    WithProxyDialerFactory configures an HTTP client to use a custom proxyDialerFactory instead of the default newConnectDialer().
    This allows to implement custom proxy dialer use cases.
    ```
*   WithEnableEuckrResponse

    ```
    WithEnableEuckrResponse configures the client to decode the response body using EUC-KR encoding.
    ```
*   WithDialContext

    ```
    WithDialContext sets a custom dialer for TCP connections, allowing advanced networking
    (Zero-DNS, socket tagging, DPI bypass).

    WARNING: This overrides built-in proxy settings. If you need a proxy, you must handle the CONNECT handshake manually.
    ```

    Example:
    ```go
    client, err := tls_client.NewHttpClient(
        tls_client.NewNoopLogger(),
        tls_client.WithClientProfile(profiles.Chrome_124),
        tls_client.WithDialContext(func(ctx context.Context, network, addr string) (net.Conn, error) {
            // Custom dialing logic here
            return net.Dial(network, addr)
        }),
    )
    ```
*   WithPreHook

    ```
    WithPreHook adds a pre-request hook that is called before each request is sent.
    Multiple hooks can be added and they will be executed in the order they were added.
    If any hook returns an error, the request is aborted and subsequent hooks are not called.
    To continue hook execution despite an error, wrap your error with ErrContinueHooks.
    ```

    The hook function signature is:
    ```go
    type PreRequestHookFunc func(req *http.Request) error
    ```

    Example:
    ```go
    client, err := tls_client.NewHttpClient(
        tls_client.NewNoopLogger(),
        tls_client.WithClientProfile(profiles.Chrome_124),
        tls_client.WithPreHook(func(req *http.Request) error {
            req.Header.Set("X-Custom-Header", "my-value")
            return nil
        }),
    )
    ```

    You can also add hooks at runtime using `client.AddPreRequestHook(hook)`.
*   WithPostHook

    ```
    WithPostHook adds a post-response hook that is called after each request completes.
    Multiple hooks can be added and they will be executed in the order they were added.
    Hooks receive a PostResponseContext containing the request, response, and any error that occurred.
    If a hook returns an error, subsequent hooks are not called (unless wrapped with ErrContinueHooks).
    ```

    The hook function signature and context struct are:
    ```go
    type PostResponseContext struct {
        Request  *http.Request
        Response *http.Response
        Error    error // Non-nil if request failed
    }

    type PostResponseHookFunc func(ctx *PostResponseContext) error
    ```

    Example:
    ```go
    client, err := tls_client.NewHttpClient(
        tls_client.NewNoopLogger(),
        tls_client.WithClientProfile(profiles.Chrome_124),
        tls_client.WithPostHook(func(ctx *tls_client.PostResponseContext) error {
            if ctx.Error != nil {
                log.Printf("Request failed: %v", ctx.Error)
            } else {
                log.Printf("Response status: %d", ctx.Response.StatusCode)
            }
            return nil
        }),
    )
    ```

    You can also add hooks at runtime using `client.AddPostResponseHook(hook)`.
*   ErrContinueHooks

    ```
    ErrContinueHooks can be returned (or wrapped) by a hook function to signal that
    the error should be logged but hook execution should continue to the next hook.
    By default any error returned from a hook aborts subsequent hooks (and for pre-hooks, the request).
    ```

    Example:
    ```go
    tls_client.WithPreHook(func(req *http.Request) error {
        // This error will be logged but subsequent hooks and the request will continue
        return fmt.Errorf("something went wrong: %w", tls_client.ErrContinueHooks)
    })
    ```

#### Shared Library & Standalone API

When using the shared library or standalone api you can find almost every client option as a separate field to turn on / turn off or configure in the request payload.

Please take a look at the Documentation about the [Payload](../shared-library/payload.md).
