# Custom Client Profile

**You should know at least a bit about TLS Fingerprints in order to know what you are doing here and what is minimum required.**

You can build complete custom clients which consist of different things. When we look at the factory method we see that you need to provide the following information:

* A clientHelloId which holds the `ClientHelloSpecFactory`. More about that later.
* A key value map of http2 settings
* A list of http2 settings for the order
* A list of [pseudo headers](pseudo-header-order.md) for the order
* A value for the connection flow
* A slice of http2 priority settings
* A optional header priority setting
* A stream id and whether plain `http://` is allowed
* The http3 counterparts: a settings map, their order, the priority param, the pseudo header order and whether to send GREASE frames

The last two groups can be left at their zero values (`0`, `false`, `nil`). A profile without http3 settings simply has no HTTP/3 fingerprint, which only matters once the client actually speaks HTTP/3 - see [Protocol Racing](protocol-racing.md).

```go
func NewClientProfile(clientHelloId tls.ClientHelloID, settings map[http2.SettingID]uint32, settingsOrder []http2.SettingID, pseudoHeaderOrder []string, connectionFlow uint32, priorities []http2.Priority, headerPriority *http2.PriorityParam, streamID uint32, allowHTTP bool, http3Settings map[uint64]uint64, http3SettingsOrder []uint64, http3PriorityParam uint32, http3PseudoHeaderOrder []string, http3SendGreaseFrames bool) ClientProfile {
   return ClientProfile{
      clientHelloId:          clientHelloId,
      settings:               settings,
      settingsOrder:          settingsOrder,
      pseudoHeaderOrder:      pseudoHeaderOrder,
      connectionFlow:         connectionFlow,
      priorities:             priorities,
      headerPriority:         headerPriority,
      streamID:               streamID,
      allowHTTP:              allowHTTP,
      http3Settings:          http3Settings,
      http3SettingsOrder:     http3SettingsOrder,
      http3PriorityParam:     http3PriorityParam,
      http3PseudoHeaderOrder: http3PseudoHeaderOrder,
      http3SendGreaseFrames:  http3SendGreaseFrames,
   }
}
```

Note that `NewClientProfile` lives in the `profiles` package, so you call it as `profiles.NewClientProfile(...)`.

You can create a `ClientHelloSpecFactory` out of a ja3 string by calling GetSpecFactoryFromJa3String. You need to provide in addition the following information, as these can't be derived from the ja3 string:

* A list of supported signature algorithms
* A list of supported delegated credentials algorithms
* A list of supported TLS versions
* A list of supported key share curves
* A list of certificate compression algorithms
* A value for the RecordSizeLimit Extension, as a `uint16`
* The payload for the trust_anchors extension, if your ja3 string lists extension `51764`

```go
ja3 := "771,4865-4866-4867-49195-49199-49196-49200-52393-52392-49171-49172-156-157-47-53,0-10-11-13-16-23-43-51-65281-45-21,29-23-24,0"

ssa := []string{"ECDSAWithP256AndSHA256",
   "PSSWithSHA256",
   "PKCS1WithSHA256",
   "ECDSAWithP384AndSHA384",
   "PSSWithSHA384",
   "PKCS1WithSHA384",
   "PSSWithSHA512",
   "PKCS1WithSHA512"}
dca := []string{"ECDSAWithP256AndSHA256",
   "PSSWithSHA256",
   "PKCS1WithSHA256",
   "ECDSAWithP384AndSHA384",
   "PSSWithSHA384",
   "PKCS1WithSHA384",
   "PSSWithSHA512",
   "PKCS1WithSHA512"}
sv := []string{"GREASE", "1.3", "1.2"}
sc := []string{"GREASE", "X25519"}

alpnProtocols := []string{"h2", "http/1.1"}
alpsProtocols := []string{"h2"}

ccs := []tls_client.CandidateCipherSuites{
   {
      KdfId:  "HKDF_SHA256",
      AeadId: "AEAD_AES_128_GCM",
   },
   {
      KdfId:  "HKDF_SHA256",
      AeadId: "AEAD_CHACHA20_POLY1305",
   },
}
cp := []uint16{128, 160, 192, 224}
certCompressionAlgos := []string{"zlib"}
recordSizeLimit := uint16(0)
trustAnchorsPayload := "" // hex payload, only needed if the ja3 string lists extension 51764

specFunc, err := tls_client.GetSpecFactoryFromJa3String(ja3, ssa, dca, sv, sc, alpnProtocols, alpsProtocols, ccs, cp, certCompressionAlgos, recordSizeLimit, trustAnchorsPayload)
```

Chrome 150 and later also advertise the ML-DSA codepoints, and Chrome 152 puts a GREASE value first, so a fingerprint imitating those versions needs `"GREASE"`, `"MLDSA44"`, `"MLDSA65"` or `"MLDSA87"` in the signature algorithm list. utls replaces the GREASE placeholder with a value drawn per connection, exactly as it does for the bundled profiles.

#### The trust_anchors extension (51764)

Chromium sends the trust_anchors extension from Chrome 144 on. A ja3 string names extension IDs but carries no extension data, so when your ja3 string contains `51764` the payload has to come alongside it as a hex string. Leaving it empty in that case fails with an error naming the parameter.

Take the value from the `data` field of the `Unknown extension 51764` entry in a browser fingerprint; it already starts at the 16-bit list length. `"0000"` is an empty anchor list.

Of the shipped profiles, `chrome_146` and `chrome_146_PSK` send that empty list, while `chrome_152` and `chrome_152_PSK` send a captured 186 byte payload of 28 anchor IDs. The other profiles do not send the extension at all.

The IDs are reordered once per spec factory, because Chromium writes them in hash set iteration order, which holds for the life of a browser process and differs between processes. `profiles.BuildTrustAnchorsPayload` does that on its own if you build the extension by hand.

At the end you can just build the complete custom profile by providing all the above mentioned information.

```go
specFunc, err := tls_client.GetSpecFactoryFromJa3String(ja3, ssa, dca, sv, sc, alpnProtocols, alpsProtocols, ccs, cp, certCompressionAlgos, recordSizeLimit, trustAnchorsPayload)
if err != nil {
   log.Println(err.Error())
   return
}

settings := map[http2.SettingID]uint32{
   http2.SettingHeaderTableSize:      65536,
   http2.SettingMaxConcurrentStreams: 1000,
   http2.SettingInitialWindowSize:    6291456,
   http2.SettingMaxHeaderListSize:    262144,
}
settingsOrder := []http2.SettingID{
   http2.SettingHeaderTableSize,
   http2.SettingMaxConcurrentStreams,
   http2.SettingInitialWindowSize,
   http2.SettingMaxHeaderListSize,
}

pseudoHeaderOrder := []string{
   ":method",
   ":authority",
   ":scheme",
   ":path",
}

connectionFlow := uint32(15663105)

customClientProfile := profiles.NewClientProfile(tls.ClientHelloID{
   Client:      "MyCustomProfile",
   Version:     "1",
   Seed:        nil,
   SpecFactory: specFunc,
},
   settings,
   settingsOrder,
   pseudoHeaderOrder,
   connectionFlow,
   nil,   // priorities
   nil,   // headerPriority
   0,     // streamID
   false, // allowHTTP
   nil,   // http3Settings
   nil,   // http3SettingsOrder
   0,     // http3PriorityParam
   nil,   // http3PseudoHeaderOrder
   false, // http3SendGreaseFrames
)

client, err := tls_client.NewHttpClient(tls_client.NewNoopLogger(), tls_client.WithClientProfile(customClientProfile))
if err != nil {
   log.Println(err.Error())
   return
}
```

#### Shared Library & Standalone API

It is also possible to define custom client profiles when working with the shared library or the standalone api. For that you are not calling functions but provide all the information on the request payload that the TLS client is able to build the client profile out of it.

Instead of the `"tlsClientIdentifier"` you can provide on the request payload the `"customTlsClient"`. Only `"ja3String"` is really required; every other field falls back to a zero value. The setting names in `"h2Settings"` / `"h2SettingsOrder"` and `"h3Settings"` / `"h3SettingsOrder"` have to be spelled as below, unknown names are ignored.

```json
{
  "customTlsClient": {
    "ja3String": "771,2570-4865-4866-4867-49195-49199-49196-49200-52393-52392-49171-49172-156-157-47-53,2570-0-23-65281-10-11-35-16-5-13-18-51-45-43-27-17513-2570-21,2570-29-23-24,0",
    "trustAnchorsPayload": "",
    "h2Settings": {
      "HEADER_TABLE_SIZE": 65536,
      "MAX_CONCURRENT_STREAMS": 1000,
      "INITIAL_WINDOW_SIZE": 6291456,
      "MAX_HEADER_LIST_SIZE": 262144
    },
    "h2SettingsOrder": [
      "HEADER_TABLE_SIZE",
      "MAX_CONCURRENT_STREAMS",
      "INITIAL_WINDOW_SIZE",
      "MAX_HEADER_LIST_SIZE"
    ],
    "supportedSignatureAlgorithms": [
      "ECDSAWithP256AndSHA256",
      "PSSWithSHA256",
      "PKCS1WithSHA256",
      "ECDSAWithP384AndSHA384",
      "PSSWithSHA384",
      "PKCS1WithSHA384",
      "PSSWithSHA512",
      "PKCS1WithSHA512"
    ],
    "supportedDelegatedCredentialsAlgorithms": [],
    "supportedVersions": ["GREASE", "1.3", "1.2"],
    "keyShareCurves": ["GREASE", "X25519"],
    "alpnProtocols": ["h2", "http/1.1"],
    "alpsProtocols": ["h2"],
    "certCompressionAlgos": ["brotli"],
    "recordSizeLimit": 0,
    "ECHCandidatePayloads": [],
    "ECHCandidateCipherSuites": [],
    "pseudoHeaderOrder": [
      ":method",
      ":authority",
      ":scheme",
      ":path"
    ],
    "connectionFlow": 15663105,
    "streamId": 0,
    "allowHttp": false,
    "priorityFrames": [
      {
        "streamID": 1,
        "priorityParam": {
          "streamDep": 1,
          "exclusive": true,
          "weight": 1
        }
      }
    ],
    "headerPriority": {
      "streamDep": 1,
      "exclusive": true,
      "weight": 1
    },
    "h3Settings": {
      "QPACK_MAX_TABLE_CAPACITY": 65536,
      "MAX_FIELD_SECTION_SIZE": 262144,
      "QPACK_BLOCKED_STREAMS": 100,
      "H3_DATAGRAM": 1
    },
    "h3SettingsOrder": [
      "QPACK_MAX_TABLE_CAPACITY",
      "MAX_FIELD_SECTION_SIZE",
      "QPACK_BLOCKED_STREAMS",
      "H3_DATAGRAM"
    ],
    "h3PseudoHeaderOrder": [
      ":method",
      ":authority",
      ":scheme",
      ":path"
    ],
    "h3PriorityParam": 984832,
    "h3SendGreaseFrames": true
  }
}
```

#### Examples

* [`./example/custom_profile`](https://github.com/bogdanfinn/tls-client/tree/master/example/custom_profile) builds the profile from a JA3 string, including the `trust_anchors` payload and the ML-DSA / GREASE signature algorithms Chrome 150 and later send.
* [`./example/custom_spec`](https://github.com/bogdanfinn/tls-client/tree/master/example/custom_spec) builds the `tls.ClientHelloSpec` by hand, which is the way to go when an extension needs data a JA3 string cannot carry - PSK and ALPS, for instance.
