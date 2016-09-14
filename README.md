# Rails API Format

## Versioning

Version number must be present in the URL, e.g.:

```
/v1/articles
```

Prefer not to have a default version, instead require clients to explicitly peg their usage to a specific version.

## Document structure

### Root element

A document's top level must contain a root element as a representation of the resource or collection of resources primarily targeted by a request:

* in single form for individual resource, e.g.: `post`
* in plural form for collection of resources, e.g.: `posts`

### Individual resource representations

An individual resource must be represented as a single "resource object".

```
{
  "post": {
    "id": "1",
    // ... attributes of this post
  }
}
```

### Resource collection representations

A collection of any number of resources must be represented as an array of resource objects.

```
{
  "posts": [{
    "id": "1"
    // ... attributes of this post
  }, {
    "id": "2"
    // ... attributes of this post
  }]
}
```

### Resource foreign keys

Serialize foreign key references with a nested object, e.g.:

```
{
  "article" : {
    "name": "service-production",
    "owner": {
      "id": "1"
    }
  }
}
```

Instead of e.g:

```
{
  "article" : {
    "name": "service-production",
    "owner_id": "1",
  }
}
```

This approach makes it possible to inline more information about the related resource without having to change the structure of the response or introduce more top-level response fields, e.g.:

```
{
  "article" : {
    "name": "service-production",
    "owner": {
      "id": "5d8201b0...",
      "name": "Alice",
      "email": "alice@heroku.com"
    }
  }
}
```

### Resource date&time format

Accept and return dates in UTC only. Render dates in ISO8601 format, e.g.:

```
{
  "finished_at": "2012-01-01T12:00:00Z"
}
```

### Resource relationships

Nest resource relationships in to the primary resource.

For example, the following post is associated with a single author and a collection of comments:

```
{
  "post": {
    "id": "1",
    "title": "Rails is Omakase",
    "author": {
      "id": "1",
      "name": "DHH"
    },
    "comments": [
      {
        "id": "1",
        "text": "..."
      },
      {
        "id": "2",
        "text": "..."
      }
    ]
  }
}
```

### Errors

Error objects are specialized resource objects that may be returned in a response to provide additional information about problems encountered while performing an operation.

Error object should be returned as a resource keyed by "error" in the top level of a JSON API document, and should not be returned with any other top level resources.

Error object may have the following keys:
* __id__: a unique identifier for this particular occurrence of the problem.
* __status__: the HTTP status code applicable to this problem, expressed as a string value.
* __error__: general description, which can be used on client side, should be understandable for users.
* __validations__: contain validation error messages for resource fields.

```
HTTP/1.1 401 Unauthorized

{
  "error": {
    "id": "f6d7af54-5d5b-4845-8c17-cdd645fbfa5d",
    "status": 401,
    "error": "Authentication failed"
  }
}

HTTP/1.1 404 Not Found

{
  "error": {
    "status": 404,
    "error": "Not Found."
  }
}

HTTP/1.1 422 Unprocessable Entity

{
  "error": {
    "id": "f6d7af54-5d5b-4845-8c17-cdd645fbfa5d",
    "status": 422,
    "error": "Validation Error",
    "validations": {
      "first_name": ["can't be blank"]
    }
  }
}
```

## Fetching Resources

Resource, or collection of resources, can be fetched by sending a GET request to specific URL.

### Fetch collection of resources

`GET /v1/posts`

### Fetch single resource

`GET /v1/posts/1`

### Pagination

Collection of resources could be paginated according to specific page and per_page parameters in the query string:

`GET /v1/posts?per_page=2&page=1`

#### Response

A document's top level must have meta key to specify pagination information with following elements:

* __total__: total number of resources in the paginated collection.
* __per_page__: number of resources per page.
* __page__: requested page number.

```
HTTP/1.1 200 OK

{
  "meta": {
    "total": "10",
    "per_page": "2",
    "page": "1"
  },
  "posts": [{
    "id": "1"
    // ... attributes of this post
  }, {
    "id": "2"
    // ... attributes of this post
  }]
}
```

## Creating resource

Resource can be created by making a POST request to the URL that represents a collection of resources to which the new resource should belong.

A request to create a resource must include a single primary resource object.

```
POST /v1/photos

{
  "photo": {
    "title": "Ember Hamster",
    "src": "http://example.com/images/productivity.png"
  }
}
```

### Response

A server must respond `201 Created` to a successful resource creation request.

A response must also include a document that contains the primary resource created.

```
HTTP/1.1 201 Created

{
  "photo": {
    "id": "1",
    "title": "Ember Hamster",
    "src": "http://example.com/images/productivity.png"
  }
}
```

## Updating resource

Resources can be updated by making a PUT or PATCH request to the URL that represents resources. A request must include a single top-level resource object.

```
PUT /v1/articles/1

{
  "article": {
    "id": "1",
    "title": "To TDD or Not"
  }
}
```

### Response

A server must respond `200 OK` to a successful resource update request.

A response must also include a document that contains updated resource.

```
HTTP/1.1 200 Success

{
  "article": {
    "id": "1",
    "title": "To TDD or Not"
  }
}
```

## Deleting resources

Resource can be deleted by making a DELETE request to the resource's URL:

`DELETE /v1/photos/1`

### Response

A server must respond `200 OK` to a successful resource delete request.

A response must also include a document that contains deleted resource.

```
HTTP/1.1 200 Success

{
  "photo": {
    "id": "1",
    "title": "Ember Hamster",
    "src": "http://example.com/images/productivity.png"
  }
}
```
