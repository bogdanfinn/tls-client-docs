# Endpoints

You need to do a POST Request against the running API with the following JSON Request Body.

#### Forward Request `/api/forward`

```
curl --location --request POST '127.0.0.1:8080/api/forward' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
    "tlsClientIdentifier": "chrome_105",
    "requestUrl": "https://tls.peet.ws/api/all",
    "requestMethod": "GET"
}'
```

#### Get Cookies from Session `/api/cookies`

```
curl --location --request POST '127.0.0.1:8080/api/cookies' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
  "sessionId":"my-custom-sessionid",
  "url":"http://some-url.com"
}'
```

#### Free Single Session `/api/free-session`

```
curl --location --request POST '127.0.0.1:8080/api/free-session' \
--header 'x-api-key: my-auth-key-1' \
--header 'Content-Type: application/json' \
--data-raw '{
  "sessionId":"my-custom-sessionid"
}'
```

#### Free All Sessions `/api/free-all`

```
curl --location --request GET '127.0.0.1:8080/api/free-all' \
--header 'x-api-key: my-auth-key-1'
```
