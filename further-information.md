# Further Information

This library uses the following api: [https://tls.peet.ws/api/all](https://tls.peet.ws/api/all) to verify the hashes and fingerprints for http2 and ja3. Be aware that also peets api does not show every extension/cipher a TLS client is using. Do not rely just on ja3 strings. The API does not include the GREASE Extenson into the ja3 string. It’s omitted on purpose. Always have a look at the extension list and not only on the ja3 string.

Show some love for peet here: [https://www.buymeacoffee.com/peeet](https://www.buymeacoffee.com/peeet)

If you appreciate my work feel free to [buy me a coffee](https://www.buymeacoffee.com/CaptainBarnius) or donate some money to charity.

### License

Since version 1.17.0 the tls-client, and with it the shared library, is licensed under the [BSD-3-Clause license](https://github.com/bogdanfinn/tls-client/blob/master/LICENSE). Before that it was under the BSD-4-Clause license; the advertising clause, which required an acknowledgement in all advertising materials, no longer applies.
