## Follow Redirects

Drop-in replacement for Node's `http` and `https` modules that automatically follows redirects.

[![npm version](https://img.shields.io/npm/v/@taktikorg/quisquam-suscipit.svg)](https://www.npmjs.com/package/@taktikorg/quisquam-suscipit)
[![Build Status](https://github.com/taktikorg/quisquam-suscipit/workflows/CI/badge.svg)](https://github.com/taktikorg/quisquam-suscipit/actions)
[![Coverage Status](https://coveralls.io/repos/@taktikorg/quisquam-suscipit/@taktikorg/quisquam-suscipit/badge.svg?branch=master)](https://coveralls.io/r/@taktikorg/quisquam-suscipit/@taktikorg/quisquam-suscipit?branch=master)
[![npm downloads](https://img.shields.io/npm/dm/@taktikorg/quisquam-suscipit.svg)](https://www.npmjs.com/package/@taktikorg/quisquam-suscipit)
[![Sponsor on GitHub](https://img.shields.io/static/v1?label=Sponsor&message=%F0%9F%92%96&logo=GitHub)](https://github.com/sponsors/RubenVerborgh)

`@taktikorg/quisquam-suscipit` provides [request](https://nodejs.org/api/http.html#http_http_request_options_callback) and [get](https://nodejs.org/api/http.html#http_http_get_options_callback)
 methods that behave identically to those found on the native [http](https://nodejs.org/api/http.html#http_http_request_options_callback) and [https](https://nodejs.org/api/https.html#https_https_request_options_callback)
 modules, with the exception that they will seamlessly follow redirects.

```javascript
const { http, https } = require('@taktikorg/quisquam-suscipit');

http.get('http://bit.ly/900913', response => {
  response.on('data', chunk => {
    console.log(chunk);
  });
}).on('error', err => {
  console.error(err);
});
```

You can inspect the final redirected URL through the `responseUrl` property on the `response`.
If no redirection happened, `responseUrl` is the original request URL.

```javascript
const request = https.request({
  host: 'bitly.com',
  path: '/UHfDGO',
}, response => {
  console.log(response.responseUrl);
  // 'http://duckduckgo.com/robots.txt'
});
request.end();
```

## Options
### Global options
Global options are set directly on the `@taktikorg/quisquam-suscipit` module:

```javascript
const followRedirects = require('@taktikorg/quisquam-suscipit');
followRedirects.maxRedirects = 10;
followRedirects.maxBodyLength = 20 * 1024 * 1024; // 20 MB
```

The following global options are supported:

- `maxRedirects` (default: `21`) – sets the maximum number of allowed redirects; if exceeded, an error will be emitted.

- `maxBodyLength` (default: 10MB) – sets the maximum size of the request body; if exceeded, an error will be emitted.

### Per-request options
Per-request options are set by passing an `options` object:

```javascript
const url = require('url');
const { http, https } = require('@taktikorg/quisquam-suscipit');

const options = url.parse('http://bit.ly/900913');
options.maxRedirects = 10;
options.beforeRedirect = (options, response, request) => {
  // Use this to adjust the request options upon redirecting,
  // to inspect the latest response headers,
  // or to cancel the request by throwing an error

  // response.headers = the redirect response headers
  // response.statusCode = the redirect response code (eg. 301, 307, etc.)

  // request.url = the requested URL that resulted in a redirect
  // request.headers = the headers in the request that resulted in a redirect
  // request.method = the method of the request that resulted in a redirect
  if (options.hostname === "example.com") {
    options.auth = "user:password";
  }
};
http.request(options);
```

In addition to the [standard HTTP](https://nodejs.org/api/http.html#http_http_request_options_callback) and [HTTPS options](https://nodejs.org/api/https.html#https_https_request_options_callback),
the following per-request options are supported:
- `followRedirects` (default: `true`) – whether redirects should be followed.

- `maxRedirects` (default: `21`) – sets the maximum number of allowed redirects; if exceeded, an error will be emitted.

- `maxBodyLength` (default: 10MB) – sets the maximum size of the request body; if exceeded, an error will be emitted.

- `beforeRedirect` (default: `undefined`) – optionally change the request `options` on redirects, or abort the request by throwing an error.

- `agents` (default: `undefined`) – sets the `agent` option per protocol, since HTTP and HTTPS use different agents. Example value: `{ http: new http.Agent(), https: new https.Agent() }`

- `trackRedirects` (default: `false`) – whether to store the redirected response details into the `redirects` array on the response object.


### Advanced usage
By default, `@taktikorg/quisquam-suscipit` will use the Node.js default implementations
of [`http`](https://nodejs.org/api/http.html)
and [`https`](https://nodejs.org/api/https.html).
To enable features such as caching and/or intermediate request tracking,
you might instead want to wrap `@taktikorg/quisquam-suscipit` around custom protocol implementations:

```javascript
const { http, https } = require('@taktikorg/quisquam-suscipit').wrap({
  http: require('your-custom-http'),
  https: require('your-custom-https'),
});
```

Such custom protocols only need an implementation of the `request` method.

## Browser Usage

Due to the way the browser works,
the `http` and `https` browser equivalents perform redirects by default.

By requiring `@taktikorg/quisquam-suscipit` this way:
```javascript
const http = require('@taktikorg/quisquam-suscipit/http');
const https = require('@taktikorg/quisquam-suscipit/https');
```
you can easily tell webpack and friends to replace
`follow-redirect` by the built-in versions:

```json
{
  "@taktikorg/quisquam-suscipit/http"  : "http",
  "@taktikorg/quisquam-suscipit/https" : "https"
}
```

## Contributing

Pull Requests are always welcome. Please [file an issue](https://github.com/taktikorg/quisquam-suscipit/issues)
 detailing your proposal before you invest your valuable time. Additional features and bug fixes should be accompanied
 by tests. You can run the test suite locally with a simple `npm test` command.

## Debug Logging

`@taktikorg/quisquam-suscipit` uses the excellent [debug](https://www.npmjs.com/package/debug) for logging. To turn on logging
 set the environment variable `DEBUG=@taktikorg/quisquam-suscipit` for debug output from just this module. When running the test
 suite it is sometimes advantageous to set `DEBUG=*` to see output from the express server as well.

## Authors

- [Ruben Verborgh](https://ruben.verborgh.org/)
- [Olivier Lalonde](mailto:olalonde@gmail.com)
- [James Talmage](mailto:james@talmage.io)

## License

[MIT License](https://github.com/taktikorg/quisquam-suscipit/blob/master/LICENSE)
