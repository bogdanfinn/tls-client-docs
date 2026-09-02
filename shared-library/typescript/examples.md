# Examples

Please take a look at the following Typescript Code in the repository to see examples implemented: `cffi_dist/example_typescript/src/samples/`

We provide examples for the following use cases:

* GET Request (`sync.ts`)
* Async Request (`async.ts`)
* POST Request (`post.ts`)
* Use Cookies (`cookies.ts`)
* Custom Client (`custom_client.ts`)
* Proxy Rotation (`proxy.ts`)
* Switching the Redirect Following Behavior (`redirects.ts`)
* Certificate Pinning (`pinning.ts`)
* Image Download (`download.ts`)

Each sample can be run with its own npm script, e.g. `npm run start:post`. See `package.json` for the full list.

`src/types.ts` mirrors the payload and response structs of the shared library, which live in `cffi_src/types.go` in the tls-client repository. Every field of the request payload, the `customTlsClient` definition and the transport options is typed there, including the HTTP/2 and HTTP/3 setting names as string unions. When you upgrade the shared library, check that go file for new fields.

`src/client.ts` wraps the [exposed methods](../exposed-methods.md) and frees the memory of every response for you, so you do not have to call `freeMemory` yourself.

In general you can also take a look at the [JavaScript Examples](../javascript/examples.md). The Typescript examples are more about how to define the types correctly and use them.
