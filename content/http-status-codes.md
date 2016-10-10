# HTTP Status Codes

Http can be thought of as the language that the web speaks. 

Status codes fall into 5 categories, notated by the first digit in them

| group |     type      |                         example/explanaion                         |
| ----- |     ----      |                              -------                               |
|  100  | informational | 100 Continue acknowledges the request and says to carry on with it |
|  200  |    success    |                       request was successful                       |
|  300  |  redirection  |                    requested resource has moved                    |
|  400  | client errors |                you (the browser) sent a bad request                |
|  500  | server errors |                 something went wrong on the server                 |

Today we'll focus on the last two, 400 and 500 errors.

Here we'll cover some of the most common 400 and 500 errors. You'll see a brief
explanation for each, and a common scenario in which you might encounter the
code.

Codes are conventions. While the w3 might say how you are supposed to use the
code, you very well might see the code being used in different ways in the wild.

--------------------------------------------------------------------------------

## 400 Bad Request

The request is malformed, that is, there is a syntax error in the request. The
request cannot be parsed correctly.

### You'll see it when...

You send a request with a json payload, but have a syntax error in the json you
are trying to send.

## 401 Unauthorized

You do not have proper authorization to view whatever resource you are
requesting.

### You'll see it when...

You try to make a request to an api that requires a token, but forget to include
said token.

## 403 Forbidden

The server acknowledges the request, but will not do anything about it. This
differs from the 401 unauthorized above in that a 403 response means that the
request is not allowed, period. While a 401 means the request might be responded
to with the proper authorization.

### You'll see it when...

Most often when you try to access your site and your web server is not setup
properly. For example, if you turn off directory listing and you see a 403 error
message when trying to visit your site, you very well might have forgotten to
include an `index.html` file.

## 404 Not Found

A Code many are familiar with, this means that the requested resource was not
found. 

### You'll see it when...

A typo is made in a url.

### Note

Many websites will use a 404 code when a user tries to access something they
don't have authorization for. Notable Github will do this if you try to access a
private repository you don't have authorization for. This is done as a security
precaution to prevent any excess information getting out.

## 405 Method not allowed

The http method that the request was made with isn't available for the requested
resource.

### You'll see it when...

You use the wrong method into your ajax request. For exapmle, an api endpoint
only responds to `POST` requests, but you make a request to that endpoint with
the `GET` method.

## 410 Gone

A resource used to be here, but is no longer

### You'll see it when

Content on a site has been permanently removed (*not* relocated).

## 413 Request Entity to Large

Too much content is being sent in the request.

### You'll see it when...

You try to upload a file that is larger than the server can handle.

## 422 Unproccessable Entity

The server is unable to process the content of the request.

### You'll see it when...

You send a syntactically valid request, but the content of the request fails
validation. For example, you send a ajax request and the json is well formed,
but one of the fields doesn't meet a length requirement.

## 429 Too Many Requests

The client is making requests too frequently

### You'll see it when...

You are accessing a rate-limited api, and enough requests that you run up
against the api's rate limit.

## 500 Internal Server Error

Something unexpected went wrong with the server.

### You'll see it when

Something messed up internally on the server side, for example, there's a syntax
error in the server side PHP code.

## 502 Bad Gateway

The server is being used as a proxy or a gateway to another server and the
upstream server hits an error.

### You'll see it when

You're using nginx as a reverse proxy and something goes awry with the server
you are reverse proxying to.

## 503 Service Unavailable

The server is unable to handle the request due to too much traffic or
maintenance.

### You'll see it when...

A website is under heavy load and cannot handle all the requests it's
receiving, you'll see this on [reddit](https://www.reddit.com) from time to
time.
