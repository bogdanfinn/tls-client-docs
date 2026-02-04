# Payload

This page shows once the full possible request payload against the shared library, with all default values.

#### Basic Request Input

```
{
  "catchPanics": false,
  "certificatePinningHosts": null,
  "customTlsClient": null,
  "transportOptions": null,
  "followRedirects": false,
  "forceHttp1": false,
  "disableHttp3": false,
  "headerOrder": null,
  "headers": null,
  "insecureSkipVerify": false,
  "isByteRequest": false,
  "isByteResponse": false,
  "isRotatingProxy": false,
  "proxyUrl": null,
  "requestBody": null,
  "requestCookies": null,
  "requestHostOverride": null,
  "defaultHeaders": null,
  "connectHeaders": null,
  "requestMethod": "",
  "requestUrl": "",
  "disableIPV6": false,
  "disableIPV4": false,
  "localAddress": null,
  "sessionId": null,
  "serverNameOverwrite": "";
  "streamOutputBlockSize": null,
  "streamOutputEOFSymbol": null,
  "streamOutputPath": null,
  "timeoutMilliseconds": 0,
  "timeoutSeconds": 0,
  "tlsClientIdentifier": "",
  "withDebug": false,
  "withDefaultCookieJar": false,
  "withoutCookieJar": false,
  "withRandomTLSExtensionOrder": false
}
```



| Field               | Type                         | Description                                                                                                                              |
| ------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| headers             | Map\<string, string>         | Headers to attach on the current request. defaultHeaders will be used when empty.                                                        |
| defaultHeaders      | Map\<string, Array\<string>> | Default Headers to be used when no request headers are specified. The default header order can be specified with the Key "Header-Order:" |
| connectHeaders      | Map\<string,Array\<string>>  | Headers to be used during the CONNECT request.                                                                                           |
| serverNameOverwrite | string                       | Lookup [Client Option ](../tls-client/client-options.md)"WithServerNameOverwrite"                                                        |



* `sessionId` is optional. When not provided the API does not create a Session. On every forwarded request with a given sessionId you will receive the sessionId in the response to be able to reuse sessions (cookies).
* Be aware that `insecureSkipVerify` and the `timeoutSeconds` can not be changed during a session.
* `followRedirects` and `proxyUrl` can be changed within a session.
* If you do not want to set `requestBody` or `proxyUrl` use `null` instead of empty string
* When you set `isByteResponse` to `true` the response body will be a base64 encoded string. Useful when you want to download images for example.
* When you set `isByteRequest` to `true` the request body needs to be a base64 encoded string. Useful when you want to upload images for example.

#### Custom TLS-Client

```
{
  "certCompressionAlgo": "",
  "connectionFlow": 0,
  "h2Settings": null,
  "h2SettingsOrder": null,
  "headerPriority": null,
  "ja3String": "",
  "keyShareCurves": null,
  "priorityFrames": null,
  "alpnProtocols": null,
  "alpsProtocols": null,
  "ECHCandidatePayloads": null,
  "ECHCandidateCipherSuites": null,
  "pseudoHeaderOrder": null,
  "supportedDelegatedCredentialsAlgorithms": null,
  "supportedSignatureAlgorithms": null,
  "supportedVersions": null
}
```

<table><thead><tr><th width="277.3333333333333">Field</th><th width="258">Type</th><th>Descirption</th></tr></thead><tbody><tr><td>certCompressionAlgo</td><td>string</td><td>See possible values at the end of this page</td></tr><tr><td>connectionFlow</td><td>integer</td><td></td></tr><tr><td>h2Settings</td><td>Map&#x3C;string, int></td><td>See possible values for the Map keys at the end of this page.</td></tr><tr><td>h2SettingsOrder</td><td>Array&#x3C;string></td><td>Array of string keys which are used in the h2Settings property but ordered.</td></tr><tr><td>headerPriority</td><td>PriorityParam</td><td>See type definition below in next section</td></tr><tr><td>ja3String</td><td>string</td><td></td></tr><tr><td>keyShareCurves</td><td>Array&#x3C;string></td><td>See possible values at the end of this page</td></tr><tr><td>priorityFrames</td><td>Array&#x3C;PriorityFrames></td><td>See type definition below in next section</td></tr><tr><td>alpnProtocols</td><td>Array&#x3C;string></td><td>List of supported protocols for the ALPN Extension</td></tr><tr><td>alpsProtocols</td><td>Array&#x3C;string></td><td>List of supported protocols for the ALPS Extension</td></tr><tr><td>ECHCandidatePayloads</td><td>Array&#x3C;uint16></td><td>List of ECH Candidate Payloads</td></tr><tr><td>ECHCandidateCipherSuites</td><td>Array&#x3C;CanidateCipherSuite></td><td>See type definition below in next section</td></tr><tr><td>pseudoHeaderOrder</td><td>Array&#x3C;string></td><td>See possible values at the end of this page</td></tr><tr><td>supportedDelegatedCredentialsAlgorithms</td><td>Array&#x3C;string></td><td>See possible values at the end of this page</td></tr><tr><td>supportedSignatureAlgorithms</td><td>Array&#x3C;string></td><td>See possible values at the end of this page</td></tr><tr><td>supportedVersions</td><td>Array&#x3C;string></td><td>See possible values at the end of this page</td></tr></tbody></table>

#### TransportOptions

```
{
  "disableKeepAlives": false,
  "disableCompression": false,
  "maxIdleConns": 0,
  "maxIdleConnsPerHost": 0,
  "maxConnsPerHost": 0,
  "maxResponseHeaderBytes": 0,
  "writeBufferSize": 0,
  "readBufferSize": 0,
  "idleConnTimeout": 0,
}
```

| Field                  | Type    | Description                                                                                                       |
| ---------------------- | ------- | ----------------------------------------------------------------------------------------------------------------- |
| disableKeepAlives      | boolean |                                                                                                                   |
| disableCompression     | boolean | disables (automatic) decompression behavor as documented [here](../tls-client/response-body-encoding-decoding.md) |
| maxIdleConns           | integer |                                                                                                                   |
| maxIdleConnsPerHost    | integer |                                                                                                                   |
| maxConnsPerHost        | integer |                                                                                                                   |
| maxResponseHeaderBytes | integer | If zero, a default is used                                                                                        |
| writeBufferSize        | integer | If zero, a default (currently 4KB) is used                                                                        |
| readBufferSize         | integer | If zero, a default (currently 4KB) is used                                                                        |
| idleConnTimeout        | integer | Duration in Nano Seconds                                                                                          |

#### CandidateCipherSuite

<pre><code>{
<strong>    "kdfId": "",
</strong><strong>    "aeadId": "",
</strong><strong>}
</strong></code></pre>

| Field  | Type   | Description                                 |
| ------ | ------ | ------------------------------------------- |
| kdfId  | string | See possible values at the end of this page |
| aeadId | string | See possible values at the end of this page |

#### PriorityParam

<pre><code>{
<strong>    "streamDep": 0,
</strong><strong>    "exclusive": false,
</strong><strong>    "weight": 0
</strong><strong>}
</strong></code></pre>

| Field     | Type    | Description |
| --------- | ------- | ----------- |
| streamDep | integer |             |
| exclusive | boolean |             |
| weight    | integer |             |

#### PriorityFrames

```
{
    "streamID": 0,
    "priorityParam": null
}
```

| Field         | Type          | Description                   |
| ------------- | ------------- | ----------------------------- |
| streamId      | integer       |                               |
| priorityParam | PriorityParam | See type in the section above |
|               |               |                               |

#### Cookie Input

```
{
  "domain": "",
  "expires": 0,
  "maxAge": 0,
  "name": "",
  "path": "",
  "value": ""
}
```

<table><thead><tr><th>Field</th><th width="148">Type</th><th>Description</th></tr></thead><tbody><tr><td>domain</td><td>string</td><td></td></tr><tr><td>expires</td><td>integer</td><td>Unix Timestamp</td></tr><tr><td>maxAge</td><td>integer</td><td>Number of seconds the cookie is valid.</td></tr><tr><td>name</td><td>string</td><td></td></tr><tr><td>path</td><td>string</td><td></td></tr><tr><td>value</td><td>string</td><td></td></tr></tbody></table>

If both `Expires` and `Max-Age` are set, `Max-Age` has precedence.

### Values

Here you can find the allowed possible string values to supply for fields like `supportedDelegatedCredentialsAlgorithms`, `supportedSignatureAlgorithms`, `supportedVersions` and much more.

#### H2Settings

```
"HEADER_TABLE_SIZE",
"ENABLE_PUSH",
"MAX_CONCURRENT_STREAMS",
"INITIAL_WINDOW_SIZE",
"MAX_FRAME_SIZE",
"MAX_HEADER_LIST_SIZE",
"UNKNOWN_SETTING_7",
"UNKNOWN_SETTING_8",
"UNKNOWN_SETTING_9",
```

#### Supported Versions

```
"GREASE",
"1.3",
"1.2",
"1.1",
"1.0",
```

#### Supported Signature Algorithms

```
"PKCS1WithSHA256",
"PKCS1WithSHA384",
"PKCS1WithSHA512",
"PSSWithSHA256",
"PSSWithSHA384",
"PSSWithSHA512",
"ECDSAWithP256AndSHA256",
"ECDSAWithP384AndSHA384",
"ECDSAWithP521AndSHA512",
"PKCS1WithSHA1",
"ECDSAWithSHA1",
"Ed25519",
"SHA224_RSA",
"SHA224_ECDSA",
```

#### certCompressionAlgorithm

```
"zlib",
"brotli",
"zstd",
```

#### Supported delegated credentials

```
"PKCS1WithSHA256",
"PKCS1WithSHA384",
"PKCS1WithSHA512",
"PSSWithSHA256",
"PSSWithSHA384",
"PSSWithSHA512",
"ECDSAWithP256AndSHA256",
"ECDSAWithP384AndSHA384",
"ECDSAWithP521AndSHA512",
"PKCS1WithSHA1",
"ECDSAWithSHA1",
"Ed25519",
"SHA224_RSA",
"SHA224_ECDSA"
```

#### KeyShareCurves

```
"GREASE",
"P256",
"P384",
"P521",
"X25519",
"P256Kyber768",
"X25519Kyber512D",
"X25519Kyber768",
"X25519MLKEM768",
```

#### kdfIds

```
"HKDF_SHA256",
"HKDF_SHA384",
"HKDF_SHA512",
```

#### aeadIds

```
"AEAD_AES_128_GCM",
"AEAD_AES_256_GCM",
"AEAD_CHACHA20_POLY1305",
```

