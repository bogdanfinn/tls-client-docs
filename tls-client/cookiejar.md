# Cookiejar

Since version 1.0.0 the TLS-Client uses the custom cookiejar implemented which can be found here: [https://github.com/bogdanfinn/tls-client/blob/master/jar.go](https://github.com/bogdanfinn/tls-client/blob/master/jar.go) \
Before it was more or less golangs default cookiejar: [https://github.com/bogdanfinn/fhttp/blob/master/cookiejar/jar.go](https://github.com/bogdanfinn/fhttp/blob/master/cookiejar/jar.go)

The custom cookiejar is designed for some specific (sneaker)botting related use cases when it comes to cookie overwriting or sharing cookies across different TLD. Often these use cases were about overwriting exisiting cookies in the jar with newer values or something like that.

When using the TLS-Client in go you can easilly switch between both jars just by instantiating the specific cookiejar you need:

#### Default Go Cookiejar

```
jar, _ := cookiejar.New(nil)
options := []tls_client.HttpClientOption{
   tls_client.WithCookieJar(jar),
}

client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(), options...)
```

#### Custom Cookiejar

```
jar := tls_client.NewCookieJar()

options := []tls_client.HttpClientOption{
   tls_client.WithCookieJar(jar),
}

client, err := tls_client.NewHttpClient(logger, options...)
```

#### Shared Library & Standalone API

If you are using the shared library or standalone api you can either completely deactiavte cookie jar handling by setting `"withoutCookieJar"` to `true` or you can switch to the default Go cookiejar by setting `"withDefaultCookieJar"` to `true`. By default the Custom Cookiejar will always be used.
