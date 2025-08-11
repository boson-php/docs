# HTTP

The component provides a set of HTTP structures that provide information 
about requests, responses, and their dependencies.

> This component already included in the `boson-php/runtime`,
> so no separate installation is required when using the runtime.
{.note}


## Installation

Via [Composer](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies):

```bash
composer require boson-php/http
```

**Requirements:**

* `PHP ^8.4`

## Request

The `Request` class represents an immutable HTTP request:

```php
use Boson\Component\Http\Request;

// Create a new request with default values
$request = new Request();

// Create a custom request
$request = new Request(
    method: 'POST',
    url: 'https://example.com/api/users',
    headers: [
        'content-type' => 'application/json',
        'authorization' => 'Bearer token123'
    ],
    body: '{"name": "John", "age": 30}'
);
```

All properties are immutable and can only be accessed, 
not modified.

> All request objects are created by the Boson itself within events, such as 
> [SchemeRequestReceived](../05.webview/schemes-api.md#request-intention). 
> Therefore, to ensure that the object within an event or intention will 
> **NOT be changed** and all listeners receive identical information, the 
> request object **is immutable**.
>
> ```php
> $app->on(function (SchemeRequestReceived $e): void {
>     $request = $e->request;
>
>     echo $request->url;
>     echo $request->method;
>     // etc.
> });
> ```


## Response

The `Response` class represents a mutable HTTP response:

```php
use Boson\Component\Http\Response;

// Create a new response with default values
$response = new Response();

// Create a custom response
$response = new Response(
    body: '<h1>Hello World</h1>',
    headers: [
        'content-type' => 'text/html',
        'x-custom-header' => 'value'
    ],
    status: 200
);

// Modify response
$response->headers->add('x-new-header', 'new value');
$response->body = 'New content';
$response->status = 201;
```

> All responses are created by developer in any form. Therefore, 
> for convenience, they are made **mutable** by default.
>
> ```php
> $app->on(function (SchemeRequestReceived $e): void {
>     $e->response = new Response('hello!');
>     $e->response->status = 404;
> });
> ```


## Headers

Both request and response use the `HeadersMap` class for header management.
To get a list of headers, use the `headers` property in HTTP objects
(for example, `$request->headers` or `$response->headers`).

The [request](../07.components/http.md#request) contains an immutable
`HeadersMap` object which provides methods for reading header information.

```php
// Check if header exists
if ($request->headers->has('content-type')) {
    // Get header value
    $contentType = $request->headers->first('content-type');
}
```

While the [response](../07.components/http.md#response) contains a mutable
implementation of the list of `MutableHeadersMap` headers which represents
not only methods of obtaining information, but also its modifications.

```php
// Add new header
if (!$response->headers->has('x-custom-header')) {
    $response->headers->add('x-custom-header', 'value');
}

// Remove header
$response->headers->remove('content-type');
```

> The headers map is **case-insensitive** (lowercased) for header names
{.note}

## Method

HTTP defines a set of request methods to indicate the purpose of the request
and what is expected if the request is successful. Although they can also be
nouns, these request methods are sometimes referred to as HTTP verbs. Each
request method has its own semantics, but some characteristics are shared
across multiple methods, specifically request methods can be safe,
idempotent, or cacheable.

The HTTP [request`s](../07.components/http.md#request) method can be represented
and set as a string, but it implements a full-fledged enum-like value object
containing a lot of additional information.

```php
use Boson\Component\Http\Request;

// A string may be used during instantiation.
$request = new Request(method: 'GET');

// The method can be output as a string
echo $request->method; 

//
// Expected Output:
//
//  GET
//
```

### Method Class

Methods are part of the [request](../07.components/http.md#request), but you 
can use them separately. For this, Boson component provides an enum-like 
`Boson\Component\Http\Component\Method` class containing a set of known 
HTTP cases:

- `Method::Get` – The [GET HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/GET)
- `Method::Head` – The [HEAD HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/HEAD)
- `Method::Options` – The [OPTIONS HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/OPTIONS) 
- `Method::Trace` – The [TRACE HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/TRACE)
- `Method::Put` – The [PUT HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/PUT)
- `Method::Delete` – The [DELETE HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/DELETE)
- `Method::Post` – The [POST HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/POST)
- `Method::Patch` – The [PATCH HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/PATCH)
- `Method::Connect` – The [CONNECT HTTP method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/CONNECT)

Since the `Method` class implements behavior similar to
[PHP enums](https://www.php.net/manual/ru/language.types.enumerations.php),
you also have access to the `from(), `tryFrom()` and `cases()` methods.

> Due to many technical limitations of the PHP (for example,
> [unable to use properties](https://externals.io/message/126332),
> [unable to define the `__toString()` method](https://externals.io/message/124991),
> unable to override `from()` & `tryFrom()` methods, etc.),
> this class cannot be implemented using the classic PHP enum.
{.warning}

```php
use Boson\Component\Http\Component\Method;

echo Method::from('get'); 
// GET

echo Method::from('wtf');
// Uncaught ValueError: "wtf" is not a valid backing value for
// enum-like Boson\Component\Http\Component\Method

echo Method::tryFrom('wtf'); 
// null
```

> Please note that the `from()` and `tryFrom()` methods 
> are case-insensitive.
{.note}

```php
use Boson\Component\Http\Component\Method;

foreach (Method::cases() as $method) {
    echo $method . "\n";
}

//
// Expected Output:
//
//   GET
//   HEAD
//   OPTIONS
//   TRACE
//   PUT
//   DELETE
//   POST
//   PATCH
//   CONNECT
//
```

### Method Name

To get the name of the HTTP method, you can use the read-only `name` property.

Method name is **case-insensitive** and always converted to uppercase. For 
example, if you write "`get`" or "`post`", then the method name will always be 
`GET` or `POST`.

```php
use Boson\Component\Http\Request;

$request = new Request(method: 'get');

echo $request->method->name; // GET
```

### Method Idempotency

An HTTP method is idempotent if the intended effect on the server of making a
single request is the same as the effect of making several identical requests.

For example, the HTTP specification defines several HTTP methods and their
semantics, which includes whether they are idempotent or not. All
[safe](../07.components/http.md#method-safety) methods are idempotent, as well
as `PUT` and `DELETE`. The `POST` and `PATCH` methods are not guaranteed
to be idempotent.

In any case, you don't need to remember by heart which HTTP methods are
idempotent, it is enough to use the `isIdempotent` property to get this
information.

```php
$get = new Request(method: 'get');

if ($get->method->isIdempotent) {
    echo 'GET is idempotent';
}

$post = new Request(method: 'post');

if ($post->method->isIdempotent) {
    echo 'POST is idempotent';
}

//
// Expected Output:
//
//  GET is idempotent
//
```

### Method Safety

An HTTP method is safe if it doesn't alter the state of the server.
In other words, a method is safe if it leads to a read-only operation.
Several common HTTP methods are safe: `GET`, `HEAD`, or `OPTIONS`. All safe
methods are also [idempotent](../07.components/http.md#method-idempotency),
but not all idempotent methods are safe. For example, `PUT` and `DELETE` are
both idempotent but unsafe.

To get information about the methods safety you can use the `isSafe`
property.

```php
use Boson\Component\Http\Request;

$get = new Request(method: 'get');

if ($get->method->isSafe) {
    echo 'GET is safe';
}

$post = new Request(method: 'post');

if ($post->method->isSafe) {
    echo 'POST is safe';
}

//
// Expected Output:
//
//  GET is safe
//
```


### User Defined Method

In addition to the basic HTTP methods, you may need to create your 
own non-HTTP method. This is a rare task, but it can happen.

To do this, you should create an instance of the `Method` object.

```php
use Boson\Component\Http\Component\Method;

echo new Method('PROPFIND'); // PROPFIND
```

> The `Method`s constructor is case-sensitive

Note that the [safety](../07.components/http.md#method-safety) and
[idempotency](../07.components/http.md#method-idempotency) properties
for such methods are undefined. 

```php
use Boson\Component\Http\Component\Method;

$method = new Method('PROPFIND');

$method->isSafe;        // null
$method->isIdempotent;  // null
```

You should specify them explicitly if you want 
to provide this information.

```php
use Boson\Component\Http\Component\Method;

$method = new Method(
    name: 'PROPFIND',
    isIdempotent: true,
    isSafe: false,
);

$method->isSafe;        // false
$method->isIdempotent;  // true
```


### Method Comparison

You can use the strict comparison method of objects, but if the objects are
different, the result MAY be a `false` although technically both objects
implement an identical HTTP method.

```php
use Boson\Component\Http\Component\Method;

echo Method::Get;       // GET
echo new Method('GET'); // GET

// Comparison of objects, the result may be undefined
Method::Get === new Method('GET'); // false
```

To compare methods by meaning, even if the implementations may differ,
there is the `equals()` method, which guarantees true if the HTTP methods
are identical, even if they implement different objects.

```php
new Method('GET')->equals(Method::Get);  // true

new Method('POST')->equals(Method::Get); // false
```


## Status Code

HTTP [response](../07.components/http.md#response) status codes indicate whether
a specific HTTP request has been successfully completed.

To get information about the status code, use the `status` property of the 
[response](../07.components/http.md#response) object.

```php
use Boson\Component\Http\Response;

echo new Response(status: 200)
    ->status;

//
// Expected Output:
//
//  200 OK
//

echo new Response(status: 418)
    ->status;

//
// Expected Output:
//
//  418 I’m A Teapot
//
```

### Status Code Class

Status codes are part of the [response](../07.components/http.md#response), but 
you can use them separately. For this, Boson component provides an enum-like
`Boson\Component\Http\Component\StatusCode` class containing a set of known
HTTP cases:

- `StatusCode::Continue` – 100 Continue
- `StatusCode::SwitchingProtocols` – 101 Switching Protocols
- `StatusCode::Processing` – 102 Processing
- `StatusCode::EarlyHints` – 103 Early Hints
- `StatusCode::ResponseIsStale` – 110 Response Is Stale
- `StatusCode::RevalidationFailed` – 111 Revalidation Failed
- `StatusCode::DisconnectedOperation` – 112 Disconnected Operation
- `StatusCode::HeuristicExpiration` – 113 Heuristic Expiration
- `StatusCode::MiscellaneousWarning` – 199 Miscellaneous Warning
- `StatusCode::Ok` – 200 OK
- `StatusCode::Created` – 201 Created
- `StatusCode::Accepted` – 202 Accepted
- `StatusCode::NonAuthoritativeInformation` – 203 Non-Authoritative Information
- `StatusCode::NoContent` – 204 No Content
- `StatusCode::ResetContent` – 205 Reset Content
- `StatusCode::PartialContent` – 206 Partial Content
- `StatusCode::MultiStatus` – 207 Multi-Status
- `StatusCode::AlreadyReported` – 208 Already Reported
- `StatusCode::TransformationApplied` – 214 Transformation Applied
- `StatusCode::ImUsed` – 226 IM Used
- `StatusCode::MiscellaneousPersistentWarning` – 299 Miscellaneous Persistent Warnin
- `StatusCode::MultipleChoices` – 300 Multiple Choices
- `StatusCode::MovedPermanently` – 301 Moved Permanently
- `StatusCode::Found` – 302 Found
- `StatusCode::SeeOther` – 303 See Other
- `StatusCode::NotModified` – 304 Not Modified
- `StatusCode::UseProxy` – 305 Use Proxy
- `StatusCode::Unused` – 306 Unused
- `StatusCode::TemporaryRedirect` – 307 Temporary Redirect
- `StatusCode::PermanentRedirect` – 308 Permanent Redirect
- `StatusCode::BadRequest` – 400 Bad Request
- `StatusCode::Unauthorized` – 401 Unauthorized
- `StatusCode::PaymentRequired` – 402 Payment Required
- `StatusCode::Forbidden` – 403 Forbidden
- `StatusCode::NotFound` – 404 Not Found
- `StatusCode::MethodNotAllowed` – 405 Method Not Allowed
- `StatusCode::NotAcceptable` – 406 Not Acceptable
- `StatusCode::ProxyAuthenticationRequired` – 407 Proxy Authentication Required
- `StatusCode::RequestTimeout` – 408 Request Timeout
- `StatusCode::Conflict` – 409 Conflict
- `StatusCode::Gone` – 410 Gone
- `StatusCode::LengthRequired` – 411 Length Required
- `StatusCode::PreconditionFailed` – 412 Precondition Failed
- `StatusCode::PayloadTooLarge` – 413 Payload Too Large
- `StatusCode::UriTooLong` – 414 URI Too Long
- `StatusCode::UnsupportedMediaType` – 415 Unsupported Media Type
- `StatusCode::RangeNotSatisfiable` – 416 Range Not Satisfiable
- `StatusCode::ExpectationFailed` – 417 Expectation Failed
- `StatusCode::ImATeapot` – 418 I’m A Teapot
- `StatusCode::MisdirectedRequest` – 421 Misdirected Request
- `StatusCode::UnprocessableEntity` – 422 Unprocessable Entity
- `StatusCode::EntityLocked` – 423 Locked
- `StatusCode::FailedDependency` – 424 Failed Dependency
- `StatusCode::HttpTooEarly` – 425 Too Early
- `StatusCode::UpgradeRequired` – 426 Upgrade Required
- `StatusCode::PreconditionRequired` – 428 Precondition Required
- `StatusCode::TooManyRequests` – 429 Too Many Requests
- `StatusCode::RequestHeaderFieldsTooLarge` – 431 Request Header Fields Too Large
- `StatusCode::Close` – 444 No Response
- `StatusCode::UnavailableForLegalReasons` – 451 Unavailable For Legal Reasons
- `StatusCode::ClientClosedRequest` – 499 Client Closed Request
- `StatusCode::InternalServerError` – 500 Internal Server Error
- `StatusCode::NotImplemented` – 501 Not Implemented
- `StatusCode::BadGateway` – 502 Bad Gateway
- `StatusCode::ServiceUnavailable` – 503 Service Unavailable
- `StatusCode::GatewayTimeout` – 504 Gateway Timeout
- `StatusCode::HttpVersionNotSupported` – 505 HTTP Version Not Supported
- `StatusCode::HttpVariantAlsoNegotiates` – 506 Variant Also Negotiates
- `StatusCode::HttpInsufficientStorage` – 507 Insufficient Storage
- `StatusCode::HttpLoopDetected` – 508 Loop Detected
- `StatusCode::HttpNotExtended` – 510 Not Extended
- `StatusCode::HttpNetworkAuthenticationRequired` – 511 Network Authentication Required
- `StatusCode::NetworkConnectTimeout` – 599 Network Connect Timeout Error

Since the `StatusCode` class implements behavior similar to
[PHP enums](https://www.php.net/manual/ru/language.types.enumerations.php),
you also have access to the `from(), `tryFrom()` and `cases()` methods.

> Due to many technical limitations of the PHP (for example,
> [unable to use properties](https://externals.io/message/126332),
> [unable to define the `__toString()` method](https://externals.io/message/124991),
> unable to override `from()` & `tryFrom()` methods, etc.),
> this class cannot be implemented using the classic PHP enum.
{.warning}

```php
use Boson\Component\Http\Component\Method;

echo StatusCode::from(418);
// 418 I’m A Teapot

echo StatusCode::from(42);
// Uncaught ValueError: "42" is not a valid backing value for
// enum-like Boson\Component\Http\Component\StatusCode

echo StatusCode::tryFrom(42); 
// null
```

To obtain a list of known HTTP codes, you can use the `cases()` method.

```php
use Boson\Component\Http\Component\StatusCode;

foreach (StatusCode::cases() as $status) {
    echo $status . "\n";
}

//
// Expected Output:
//
//   100 Continue
//   101 Switching Protocols
//   102 Processing
//   103 Early Hints
//   110 Response Is Stale
//   111 Revalidation Failed
//   112 Disconnected Operation
//   113 Heuristic Expiration
//   ...etc.
//
```

### Status Codes and Messages

As with the [method](../07.components/http.md#method), the status code is a 
value object. The status code instance contains information about the 
response code and message.

```php
use Boson\Component\Http\Response;

$response = new Response(status: 418);

echo 'Code: ' . $response->status->code;
echo 'Message: ' . $response->status->reason;

//
// Expected Output:
//
//  Code: 418
//  Message: I’m A Teapot
//
```


### Status Categories

Responses are grouped in five categories:

- Informational responses (`100` – `199`)
- Successful responses (`200` – `299`)
- Redirection messages (`300` – `399`)
- Client error responses (`400` – `499`)
- Server error responses (`500` – `599`)

To get information about category of the status, you can use 
the `category` property.

```php
$response = new Response(status: 418);

echo $response->status->category;

//
// Expected Output:
//
//  Client Error
//
```


### User Defined Status

In some cases you may need to define your own status code. To do this, 
you should create an instance of the `StatusCode` class.

> The status code message is optional.

```php
use Boson\Component\Http\Component\StatusCode;

echo new StatusCode(1001, 'DNS Resolution Error');

//
// Expected Output:
//
//   1001 DNS Resolution Error
//
```


### Status Comparison

You can use the strict objects comparison, but if the objects are
different, the result MAY be a `false` although technically both objects
implement an identical code number.

```php
use Boson\Component\Http\Component\StatusCode;

echo StatusCode::Ok;       // 200 OK
echo new StatusCode(200);  // 200

// Comparison of objects, the result may be undefined
StatusCode::Ok === new StatusCode(200); // false
```

To compare status codes by meaning, even if the implementations may differ,
there is the `equals()` method, which guarantees true if the HTTP statuses
are identical, even if they implement different objects.

```php
new StatusCode(200)->equals(StatusCode::Ok);  // true

new StatusCode(200)->equals(StatusCode::Ok); // false
```


## JSON Response

The `JsonResponse` class extends `Response` to provide JSON-specific 
functionality:

```php
use Boson\Component\Http\JsonResponse;

// Create a JSON response with default values
$response = new JsonResponse();

// Create a custom JSON response
$response = new JsonResponse(
    data: ['name' => 'John', 'age' => 30],
    headers: ['x-custom-header' => 'value'],
    status: 200
);

// Create a JSON response with custom encoding flags
$response = new JsonResponse(
    data: ['name' => 'John', 'age' => 30],
    jsonEncodingFlags: JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE
);
```

