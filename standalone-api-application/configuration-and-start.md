# Configuration & Start

* Configure things like api port and authentication keys in the `cmd/tls-client-api/config.dist.yml` file.
* The default endpoint is `http://127.0.0.1:8080/api/forward`
* You need to set a `x-api-key` header with an auth key from the config file. This is for protecting the API when you host it on some server. Requests without the correct keys in the header will be rejected.
