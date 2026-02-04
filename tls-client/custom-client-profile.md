# Custom Client Profile

**You should know at least a bit about TLS Fingerprints in order to know what you are doing here and what is minimum required.**

You can build complete custom clients which consist of different things. When we look at the factory method we see that you need to provide the following information:&#x20;

* A clientHelloId which holds the `ClientHelloSpecFactory`. More about that later.
* A key value map of http2 settings&#x20;
* A list of http2 settings for the order
* A list of [pseudo headers](pseudo-header-order.md) for the order
* A value for the connection flow
* A slice of http2 priority settings
* A optional header priority setting&#x20;

```go

func NewClientProfile(clientHelloId tls.ClientHelloID, settings map[http2.SettingID]uint32, settingsOrder []http2.SettingID, pseudoHeaderOrder []string, connectionFlow uint32, priorities []http2.Priority, headerPriority *http2.PriorityParam) ClientProfile {
   return ClientProfile{
      clientHelloId:     clientHelloId,
      settings:          settings,
      settingsOrder:     settingsOrder,
      pseudoHeaderOrder: pseudoHeaderOrder,
      connectionFlow:    connectionFlow,
      priorities:        priorities,
      headerPriority:    headerPriority,
   }
}
```

You can create a `ClientHelloSpecFactory` out of a ja3 string by calling GetSpecFactoryFromJa3String. You need to provide in addition the following information, as these can't be derived from the ja3 string:

* A list of supported signature algorithms
* A list of supported delegated credentials algorithms
* A list of supported TLS versions
* A list of supported key share curves
* A list of certificate compression algorithms.
* A value for the RecordSizeLimit Extension

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
	recordSizeLimit := 0
	
	specFunc, err := tls_client.GetSpecFactoryFromJa3String(input, ssa, dca, sv, sc, alpnProtocols, alpsProtocols, ccs, cp, certCompressionAlgos, recordSizeLimit)

```

At the end you can just build the complete custom profile by providing all the above mentioned information.

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
recordSizeLimit := 0
	
specFunc, err := tls_client.GetSpecFactoryFromJa3String(input, ssa, dca, sv, sc, alpnProtocols, alpsProtocols, ccs, cp, certCompressionAlgos, recordSizeLimit)

if err != nil {
   log.Println(err.Error())
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
customClientProfile := tls_client.NewClientProfile(tls.ClientHelloID{
   Client:      "MyCustomProfile",
   Version:     "1",
   Seed:        nil,
   SpecFactory: specFunc,
}, settings, settingsOrder, pseudoHeaderOrder, connectionFlow, nil, nil)
```

#### Shared Library & Standalone API

It is also possible to define custom client profiles when working with the shared library or the standalone api. For that you are not calling functions but provide all the information on the request payload that the TLS client is able to build the client profile out of it.

Instead of the `"tlsClientIdentifier"` you can provide on the request payload the `"customTlsClient"`.

```json
"customTlsClient": {
    "ja3String": "771,2570-4865-4866-4867-49195-49199-49196-49200-52393-52392-49171-49172-156-157-47-53,2570-0-23-65281-10-11-35-16-5-13-18-51-45-43-27-17513-2570-21,2570-29-23-24,0",
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
        "PKCS1WithSHA512",
    ],
    "supportedVersions": ["GREASE", "1.3", "1.2"],
    "keyShareCurves": ["GREASE", "X25519"],
    "ECHCandidatePayloads": [],
    "certCompressionAlgos": ["brotli"],
    "recordSizeLimit": 0,
    "pseudoHeaderOrder": [
        ":method",
        ":authority",
        ":scheme",
        ":path"
    ],
    "connectionFlow": 15663105,
    "priorityFrames": [{
        "streamID": 1,
        "priorityParam": {
            "streamDep": 1,
            "exclusive": true,
            "weight": 1
        }
    }],
    "headerPriority": {
        "streamDep": 1,
        "exclusive": true,
        "weight": 1
    },
},
```
