# Frequently Asked Questions / Errors

Here are some frequently asked questions about the cookiejar

1. How can i delete cookies from the session / jar?
   * Pass a new cookiejar instance to the clients SetCookiejar() method
   * For the shared library there is no option to delete all cookies of a session
   * You can delete a single cookie inside a session if you give the cookie a negative maxAge value. (Does only work for the tls\_client cookiejar not the standard cookiejar)
2. I can not do a successful POST Request with the shared library or standalone api
   * Be aware that when you do a POST Request and want to provide a forwarded request body in the `requestBody` field it has to be a string. That means if you want to send JSON you need to stringify this JSON to a string first and set the correct `content-type` header.
3. How can I use other request body content types besides json?
   * `requestBody` accepts strings and forwards them as the payload. combined with the `content-type` header the api makes the actual request body out of it. You can use for example `application/x-www-form-urlencoded` content type in the header and then just provide as request body a string similar to `key=value&key=value`
4. x509: certificate signed by unknown authority
   * As the message indicates you are trying to request a server which most likely has a self signed ssl certificate. There is a client option to skip the cerification.
5. x509: certificate has expired or is not yet valid
   * As the message indicates you are trying to request a server which most likely has an expired or invalid ssl certificate. There is a client option to skip the cerification.
6. stream error: stream ID 3; PROTOCOL\_ERROR
   * Sometimes this happens when people set a wrong `content-length` header on the request. Usually you never need to set the `content-length` header because the client will take care of that for you.
