# Exposed Methods

The shared library exposes the following endpoints / methods you can call. The payload is always a JSON string expect for the `freeMemory` method. You will find the methods also in use in the examples.&#x20;

#### request(payload: string) => string

This is the basic method you will use to do requests. For full detailed documentation about the payload please take a look at [Payload](payload.md). As you see here on other methods you have to turn the JSON Object into a JSON string before supplying it as the payload parameter.

#### getCookiesFromSession(payload: string) => string

Get all cookies which are in the provided session for a given URL. The payload should look like this:

```json
"{\"sessionId\": \"someExistingSessionId\", \"url\": \"urlToGetCookiesFor\"}"
```

#### addCookiesToSession(payload: string) => string

Add manual cookies to an existing session. The payload should look like this:

```json
"{\"sessionId\": \"someExistingSessionId\", \"url\": \"urlToAddCookiesFor\", \"cookies\": []}"
```

#### freeMemory(responseId: string) => void

Supply the `"id"` of a previous response as string in order to free the allocated memory of the previous response.

#### destroyAll() => string

Destroy all existing sessions in order to release allocated memory.

#### destroySession(payload: string) => string

Destroy a specific session in order to release allocated memory. The payload should look like this:

```json
"{\"sessionId\": \"someExistingSessionId\"}"
```

