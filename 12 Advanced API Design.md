
# Advanced API Design

In this chapter we will move beyond the basics of API design and cover some of the more advanced and powerful features.

How to make your API more 'RESTafarian'

Design principles

1. Extended HTTP methods
2. Error handling
3. Searching, sorting and pagination
4. Checking for content changes
5. Multiple Data Formats
6. Authentication
7. Logging
8. HATEOAS
9. Documenting an API

## 1 Extended HTTP Methods

HTTP Methods are the verbs that act on collections and elements.
CRUD operations require 4 methods.
```
GET		POST 		PUT		DELETE
```

### Additional Methods

- OPTIONS
- HEAD
- PATCH


## 2 Error Handling

API consumers see the API as a black box
Each response needs to include a status code
```
401 UNAUTHORISED
```
Include a human-readable message in the body
```
{
    message: "Basic Authentication required"
}
```

## 3 Searching, Sorting, Partial Responses and Pagination

```

### Searching a Collection

Search should be considered for collections

Search query passed as a parameter
```
/books?q=javascript
```

### Partial Responses

Allows you to supply only the information needed

Reduces the bandwidth
```
/books?fields=title,author
/books/1449336361?fields=title,author

### Collection Pagination

Avoid returning the entire collection!

Default limit should be decided (first 20 records?)

Allow developers to specify fewer records

Also need to specify which block to return.
```
/books?limit=15&offset=45
```

## 4 Checking for Content Changes

If the content has not changed since the last time the resource was requested there should be an option to flag this and to offer the option to not download it again. There are two ways to achieve this.

#### Last-Modified

GET response should include `Last-Modified` header

GET request should include `If-Modified-Since` header

If match the server responds with `304 NOT MODIFIED`  and the headers (no response body)

If content has changed the server responds with `200 OK` and requested resource.

#### ETag

GET response should include `ETag` header, this is a hashed string.

GET request should include `If-None-Match` header

If match the server responds with `304 NOT MODIFIED`  and the headers (no response body)

If content has changed the server responds with `200 OK` and requested resource.

#### Formats

Last-Modified / If-Modified-Since

Uses the RFC 2822 data format
```
Wed, 21 Oct 2015 07:28:00 GMT
```
ETag / If-None-Match

Hash of the response body
```
686897696a7c876b7e
```

#### Generating an ETag

Use the etag module
```javascript
const etag = require('etag')
res.setHeader('ETag', etag(body))
```

#### RFC2822 Date Format
```javascript
Const date = new Date('Tue, 27 Dec 2011 02:12:35 GMT')
Const dateStr = date.toISOString()
```

## 5 Multiple Data Formats

Representations

We have already split our application into resources

Resources are your idea of how to divide up the data and present it

The resource is the useful information being presented

The same resource may have several representations

For example a book resource:

- A representation containing metadata
- A representation containing the book contents

Works in both directions...

Deciding between representations

Two ways to define this:

Unique URI for each representation

Content negotiation

Information sent in the request header

If multiple formats are available client needs to specify

Two options:

- Accept header
```
Accept: application/json
```
- Type format in the URL
```
/books/1449336361.json
```

## 6 Authentication

No authentication status stored in API

Each request must provide the authentication needed

- Basic Access Authentication
- OAuth 2.0

### Basic Access Authentication

Client includes an Authorization header

Username and password combined with a colon
```
testuser:p455w0rd
```
This string is encoded using the RFC2045-MIME variant of base64
```javascript
Printf testuser:p455w0rd | base64
  dGVzdHVzZXI6cDQ1NXcwcmQ=
```
The result is sent in the header prepended with Basic
```
Authorization: Basic dGVzdHVzZXI6cDQ1NXcwcmQ=
```

## 7 Logging

Need to keep logs of all API requests:

- Why is this important?
- What useful data can be found in the request?

## 8 HATEOAS

Lack of standard responses.

- Most APIs have their own format, typically a JSON response that maps neatly to their own data model.
- Results in lots of APIs that do stuff their own way == confusion

Lack of Hyperlinks

- JSON has no built-in support for hyperlinks, which are a fundamental building block on the Web (W3C).
- API endpoints are linked together only through documentation.
- Forces developers to keep detailed, updated documentation
- Forces developers to read and understand it before using the API.

Hypertext As The Engine Of Application State

### Richardson Maturity Model

Breaks down the principle elements of REST into three steps

Each step progresses towards the ideal implementation of REST

Developed by Leonard Richardson

Level 0

- HTTP as a transport system without web mechanisms

Level 1 – Exposes Resources

Level 2 – Uses Verbs to Manipulate Resources

- Can perform different actions on the same resource

Level 3 – Provide links to other resources

- Can figure out how to interact with the system

#### Implementations of HATEOAS

- Hypermedia Application Language (HAL)
- JSON for Linked Documents (JSON-LD)

### Hypermedia Application Language

Open specification describing a generic structure for web resources

Achieves Richardson Maturity Model level 3

Presents a new hypermedia type:
```
application/hal+json
```

#### Individual Resources

```json
{
    "_links": {
        "self": {
            "href": "http://ex.com/books/1449336361"
        }
    },
    "id": "1449336361",
    "name": "JS.Next: ECMAScript 6"
}
```

#### Collections

```json
{
    "_links": {
        "self": {
            "href": "http://ex.com/books/1449336361"
        }
    },
    "id": "1449336361",
    "name": "JS.Next: ECMAScript 6"
}
```

#### Links

```json
"_links": {
        "self": {
            "href": "http://ex.com/books?page=3"
        },
        "first": {
            "href": "http://ex.com/books"
        },
        "prev": {
            "href": "http://ex.com/books?page=2"
        },
        "next": {
            "href": "http://ex.com/books?page=4"
        },
        "last": {
            "href": "http://ex.com/books?page=133"
        }
    }
```

#### Adding a Resource

```json
POST /books HTTP/1.1
Accept: application/json
Content-Type: application/json

{
    "id": "1449336361",
    "name": "JS.Next: ECMAScript 6"
}

HTTP/1.1 201 Created
Content-Type: application/hal+json
Location: http://example.org/api/user/matthew

{
    "_links": {
        "self": {
            "href": "http://ex.com/books/1449336361"
        }
    },
    "id": "1449336361",
    "name": "JS.Next: ECMAScript 6"
}
```

## 9 Documenting the API

Important to produce clear documentation

Helps your users understand how it works

Use APIDoc

http://apidocjs.com

Add inline annotations to source code

npm module apidoc generates the documentation

Example:

```
$ node_modules/.bin/apidoc -e node_modules/ -o docs/api/
```
This generates the API documentation and creates the web page in the `docs/api/` directory. files in the `docs/` directory can be published as a website by **GitHub**. To enable this you should generate the documentation as shown, commit and push the files to GitHub then edit the repository settings and enable **GitHub Pages** as shown.

![GitHub Pages settings](.images/github_pages_settings.png)

This will display a link to the root of the GitHub pages.

![GitHub Pages URL](.images/github_pages_url.png)

Click on this and you get a _page not found_ error. Append `api/` to this url and refresh the page to see the documentation.

![API Documentation](.images/api_documentation.png)

### URLs

There should only be 2 per resource:

Collection (plural)
```
/books
```
Resource
```
/books/1449336361
```
Summary: Keep verbs out of URLS...


