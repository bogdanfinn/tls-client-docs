# Examples

Please take a look at the following C# code in the repository to see examples implemented: `cffi_dist/example_csharp/`

`Requester.cs` shows loading and defining the shared library, plus a small session wrapper class.

`Example.csproj` is a runnable console project (`dotnet run -- <topic>`) with a short, focused example per topic:

* `basic` - GET Request
* `post` - POST Request
* `cookies` - Use Cookies
* `custom_client` - Custom Client
* `proxy` - Proxy Rotation
* `redirects` - Switching the Redirect Following Behavior
* `pinning` - Certificate Pinning
* `download` - Image Download

Adjust `LibraryPath` in `TlsClientLibrary.cs` to point at the shared library you downloaded for your OS/arch/version before running.
