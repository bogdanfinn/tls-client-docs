# Request Headers

### Header Sorting

You can sort your request headers per request by just providing a special header key (`"Header-Order:"`) with a list of header keys as sort order. Please keep in mind that it does not matter if your header keys are uppercase or all lowercase. For the header order **you have to define the header keys in the order list all lowercase.**

```go
req.Header = http.Header{
		"header4": {`value4`},
		"header2": {"value2"},
		"header1": {"value1"},
		"header3": {"value3"},
		"Header-Order:": {
			"header1",
			"header2",
			"header3",
			"header4",
		},
	}
```

#### Shared Library & Standalone API

When you are using the shared library or the standalone api this works exactly the same. You can provide the header order with the same Header Order key.&#x20;

### Header Key Capitalization

Sometimes people are confused when to write header keys all lowercase and when to write them with first uppercase letter. For HTTP2 requests header keys are usually all lowercase. For HTTP1 Requests header keys are usually first uppercase letter. Just as a rule of thumb:

`"accept-encoding"` => HTTP2

`"Accept-Encoding"` => HTTP1
