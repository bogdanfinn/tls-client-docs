# Pseudo Header Order

Pseudo headers are the following headers: `":method"`, `":authority"`, `":scheme"`, `":path"` and the order of them varies per client profile. Therefore you can define the order of the pseudo headers on the (custom-)client Profile.

```go
pseudoHeaderOrder := []string{
   ":method",
   ":authority",
   ":scheme",
   ":path",
}

customClientProfile := tls_client.NewClientProfile(tls.ClientHelloID{
   Client:      "MyCustomProfile",
   Version:     "1",
   Seed:        nil,
   SpecFactory: specFunc,
}, settings, settingsOrder, pseudoHeaderOrder, connectionFlow, nil, nil)
```

#### Shared Library & Standalone API

You will see on the CustomClient object [payload](../shared-library/payload.md) that you can define the order as a list of strings.
