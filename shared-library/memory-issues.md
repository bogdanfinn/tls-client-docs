# Memory Issues

#### Go TLS Client & Standalone API

For the pure go implementation there is no real possibility to run into memory leak issues, except of the fact that you implemented your request handling badly and the references to your objects are never really freed that the go garbage collection can not clean up the used memory.

If you are running into memory leak issues with the Go client or the standalone api please double check your request handling in order so see if you are doing things not as recommended (for example not closing or deferred closing the response body inside an infinite loop) &#x20;

#### Shared Library

If you are using the shared library there is potential to run into memory leak issues. Luckily you can avoid that by following a couple simple steps.

By design there is memory allocated for every response coming from the go implementation (shared library) to the invoking application (python, node, etc.). The caller has to free the memory when he is done with handling the response. otherwise the memory will never be freed and you run into memory issues.

On every response from the shared library you will see an `"id"` property. You can pass the value from the `"id"` field as parameter to the `freeMemory()` call of the shared library.

```javascript
const response = tlsClientLibrary.request(JSON.stringify(requestPayload));
const responseObject = JSON.parse(response)
tlsClientLibrary.freeMemory(responseObject.id) // free memory
```

```python
response = request(dumps(request_payload).encode('utf-8'))
response_bytes = ctypes.string_at(response)
response_string = response_bytes.decode('utf-8')
response_object = loads(response_string)
freeMemory(response_object['id'].encode('utf-8'))
```
