# Attention

* Applications powered with [gosoline](https://github.com/justtrackio/gosoline) automatically host a health check endpoint which is by default on port `8090` under the path `/health`. So in our case it would be `http://127.0.0.1:8090/health`.
* Applications powered with [gosoline](https://github.com/justtrackio/gosoline) automatically host a metadata server for your application to provide insights into your application. The metadata server is hosted on port `8070` and has three endpoints. `/`, `/config`, `/memory` this should help you debugging your application.&#x20;
  * **Do not make this endpoints public available when you host the Application on some server in the internet. You would make your config file public available.**
