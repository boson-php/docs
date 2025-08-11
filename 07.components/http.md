# HTTP Requests and Responses

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
echo $request->method; // string("GET")
```

### Method Name

To get the name of the HTTP method, you can use the read-only `name` property.

Method name is **case-insensitive** and always converted to uppercase. For 
example, if you write "`get`" or "`post`", then the method name will always be 
`GET` or `POST`.

```php
use Boson\Component\Http\Request;

$request = new Request(method: 'get');

echo $request->method->name; // string("GET")
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

### Method Comparison

You can use the strict comparison method of objects, but if the objects are
different, the result MAY be a `false` although technically both objects 
implement an identical HTTP method.

```php
// Comparison of objects, the result may be undefined
$request1->method === $request2->method; // bool(true) or bool(false)
```

To compare methods by meaning, even if the implementations may differ, 
there is the `equals()` method, which guarantees true if the HTTP methods 
are identical, even if they implement different objects.

```php
$request1->method->equals($request2->method); // bool(true)
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

