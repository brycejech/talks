# Intro to HTTP APIs

[View Talk on Twitch](https://www.twitch.tv/videos/220751531)

## What is an API

API is an acronym for Application Programming Interface. An API is a means through which we allow third party software to interact with our application. APIs allow us to encapsulate implementation details of a piece of software behind an interface, only exposing the parts of the application that we want other software to be able to use. This allows the users of our API to focus on what the application does; not, necessarily, how it does it. Among other benefits, it also allows us to make changes to the way our application works (provided it still exposes the same interface) without breaking third party software that may use it.

You may hear the term REST API used a lot. Oftentimes, developers will describe their APIs as RESTful, which should mean that the API conforms to the principles of the [REST application architecture](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm). Sometimes, however, what they are actually referring to can be more aptly described as Remote Procedure Calls (RPCs) over HTTP, typically using JSON or XML as a data format.

A brief introduction to both REST and RPC style APIs will be covered. When making a decision on which architecture is most suited to your application, you can apply the following questions:

Is your application primarily based on actions? - Use RPC

Is your application primarily based on data/collections with CRUD functionality? - Use REST

Another type of API worth mentioning that has been popular lately is GraphQL. GraphQL was created by Facebook and then open sourced.

## What is RESTful Architecture

REST stands for Representational State Transfer.

RESTful APIs are particularly well suited to **data driven** applications with CRUD (Create, Read, Update, Delete) functionality.

REST is a particular type of application architecture built on six constraints.

### 1 - Uniform Interface

This constraint defines the interface between clients and servers. It decouples the application and enables each part to work and evolve independently. It has 4 primary principles.

1. Resource Based

  - Resources are identified by URIs, e.g. /lists/3284/items/1
  - Resources are conceptually separate from the representations sent to the client


2. Manipulate Resources via Representations

  - A client that has a representation of a particular resource should have everything it needs to modify that resource on the server (assuming it has permission to do so)


3. Self-descriptive Messages

  - Each message should include enough information to process that message. This can include the MIME type (JSON, XML, HTML, etc), information on whether the response can be cached and for how long, and many many others.


4. Hypermedia as the Engine of Application State (HATEOS)

  - Clients deliver state to the server via the request body, HTTP headers, querystring parameters, and the request URI.
  - Servers deliver state to clients via body contents, status codes, and response headers (hypermedia)
  - As necessary, server responses should also include links to related resources. Related resources can be used to describe relationships, provide links to the next/previous result set when using pagination, etc.


### 2 - Stateless

- All of the state required to process a request should be contained in the request itself. This state can be transferred as part of the URI, request body or headers, or querystring.
- Statelessness allows for much better scalability, as the server does not have to maintain or communicate state.
- When not using sessions to store state, it also makes load balancing simpler and more performant because load balancers do not need to worry about which server a client should be directed to.

### 3 - Cacheable

- Responses should explicitly or implicitly define themselves as cacheable. This allows clients to avoid some trips to the server and further improves scalability and performance.

### 4 - Client-Server

- Clients are not concerned with data storage, which makes client code more portable.

- Servers are not concerned with views or user state, which makes them more scalable and simpler

- Servers and clients can be updated and replaced independently, as long as they expose the same interface

### 5 - Layered System

- Clients should not be able to tell if they are connected directly to the server or to an intermediary server.

- Intermediary servers can handle things like shared response caches, load balancing, and the enforcement of security policies, leading to further improved performance and scalability.

### 6 - Code on Demand (optional)

- Servers can extend client functionality by sending executable code to the client. These could be JavaScript libraries, Java applets, etc.

### RESTful Architecture in the Wild

The [GitHub API v3](https://developer.github.com/v3/) is an excellent example of a truly RESTful API.

--------------------------------------------------------------------------------

## What is an RPC API

RPC stands for Remote Procedure Call. This section will only cover [JSON-RPC](http://www.jsonrpc.org/specification)s. Other popular RPCs include XML-RPC and SOAP.

RPCs are primarily focused on methods that perform calculations, do actions, manipulate data, and the like. The easiest way of thinking about RPCs is that they are essentially just functions that we are calling on a remote server.

For example, consider the following JavaScript code:

```javascript
function sum(arr){
    const count = 0;
    for(let i = 0, len = arr.length; i < len; i++){
        count += arr[i];
    }
    return count;
}

sum([1, 2, 3, 4]); // 10
```

Here is what the calling of the `sum` function above might look like as an HTTP request to a JSON-RPC API:

```http
POST /sum HTTP/1.1
HOST: mydomain.com
Content-Type: application/json

{"jsonrpc": "2.0", "method": "sum", "params": [1, 2, 3, 4], "id": 1}
```

And the server response we are expecting:

```http
HTTP/1.1 200 OK
Date: ...
Server: ...
Content-Length: ...
Content-Type: application/json

{"jsonrpc": "2.0", "result": 10, "id": 1}
```

### JSON-RPC Response Object

Let's look at a breakdown of the response object

Member  | Purpose                                     | Constraints
------- | ------------------------------------------- | ---------------------------------------------------------------------------------
jsonrpc | Identify the version of the spec being used | Must be a string
result  | Relay results to the client                 | Required on success, must not be present on error
error   | Relay error information to the client       | Required on error, must not be present on success. Must be an object
id      | Correlate requests and responses            | Required unless not provided by the client (request is considered a notification)

A couple of things to note about the JSON-RPC spec.

- Each request and response must have a `jsonrpc` member that indicates the version of the spec being used, 2.0 in this case
- If the client is interested in the response, an `id` field must be supplied (otherwise, the request is considered to be a notification), the server must respond with the same `id` number provided by the client so that requests and responses can be correlated
- Procedure calls may be batched by supplying an array of request objects, rather than a single request object. _This is where the id field becomes important_

[JSON-RPC 2.0 Specification](http://www.jsonrpc.org/specification)

### JSON-RPC in the Wild

The [Slack Web API](https://api.slack.com/web) is an excellent example of a JSON-RPC API.

--------------------------------------------------------------------------------

## What is GraphQL?

Despite the implications from its name, GraphQL is not a query language for graph databases. Rather, it is a query language and runtime for building APIs.

GraphQL enables declarative data fetching in which the client can request exactly the data it needs from the API. Rather than having multiple endpoints that each expose fixed data structures, a GraphQL API exposes only a single endpoint and responds with precisely the data requested by the client. Pretty neat!

GraphQL itself is not a database, rather it is a query language for APIs, not databases, and is thus database agnostic and can be used in any context where APIs are used.

GraphQL is especially useful for limiting the number of round trips to the API by the client and also limiting the overall amount of network traffic. Only the requested data is being transferred over the network, and all at once, rather than clients having to query multiple API endpoints to build up a full picture of the data they need. This not only has the benefit of providing greater application performance, due to the lower amount of network requests, but can also tremendously simplify our application code, especially in situations where requests are dependent on one another.

### GraphQL Requests / Responses

Let's look at some sample GraphQL requests and corresponding responses.

Consider the following GraphQL Schema:

```graphql
type Book {
  id:          ID!
  title:       String
  year:        Int
  description: String
  authors:    [String]

}

type Query {
  booksByTitle(title: String!, limit: Int!): [Book]
}
```

In the previous schema, we've defined a `Book` type and a `Query` type. The `Query` type will be our entry point into the GraphQL service.

In order to use the `booksByTitle` query that we created, we would send a request with a body that looks like this:

```graphql
{
    booksByTitle("JavaScript", 3){
        title
        description
        authors
        year
    }
}
```

In the previous example, we pass the parameters `"JavaScript"` and `3` to the `booksByTitle` query along with the properties we want returned. Here is a response that we may expect from the API:

```json
{
    "data": {
        "booksByTitle": [
            {
                "title": "Eloquent JavaScript",
                "description": "Eloquent JavaScript, 2nd Edition dives deep into the JavaScript language to show you how to write beautiful, effective code.",
                "authors": ["Marijn Haverbeke"],
                "year": 2014
            },
            {
                "title": "Secrets of the JavaScript Ninja",
                "description": "More than ever, the web is a universal platform for all types of applications, and JavaScript is the language of the web. If you're serious about web development, it's not enough to be a decent JavaScript coder. You need to be a ninja - stealthy, efficient, and ready for anything. This book shows you how.",
                "authors": ["John Resig", "Bear Bibeault", "Josip Maras"],
                "year": 2016
            },
            {
                "title": "JavaScript: The Good Parts",
                "description": "This is a book about the JavaScript programming language. It is intended for programmers who, by happenstance or curiosity, are venturing into JavaScript for the first time.",
                "authors": ["Douglas Crockford"],
                "year": 2008
            }
        ]
    }
}
```

As you can see, the API returned only the data we requested although it may have much more information availble. In this particular case, we requested all the data available as declared by the schema, but in a real-world example this is usually not the case.

GraphQL queries and schemas can get much more complex than this. This example is used simply to illustrate the syntax and return data structure.

### Benefits of GraphQL

- Schema / Type System
  - GraphQL requires schema built with a strict type system. This allows developers to know exactly what types of data are available, and the format they are in


- Request Only Needed Data
  - Since queries can precisely define the data needed at query time, all data required to render a view can be requested in a single request. This reduces the total number of API requests and, thusly, application latency


- Wraps Existing Data Services
  - GraphQL can work with any database or data layer. This allows a single GraphQL service to load data from multiple sources in a single request

--------------------------------------------------------------------------------

## What benefits does an HTTP API provide

Regardless of whether your API is RESTful or RPC based, it still provides a lot of benefits.

- Loose coupling between client and server
- Client software is extremely resilient to changes on the server
- Uses less resources (compared to similar technologies such as SOAP)
- Performance, scalability
- Cachable (reads)
- Permits different data formats (JSON, XML)
  - JSON has been the de facto standard for some time; it parses faster than XML (less overhead) and is generally a better fit for data transfer.

--------------------------------------------------------------------------------

# Using APIs

In order to use HTTP APIs from the browser, we need a way to make an HTTP request. Several methods exist, such as jQuery's `ajax` method, the native `fetch` API, and native `XMLHTTPRequest`s. There are numerous other libraries that allow you to make HTTP requests.

Because jQuery is the most cross-platform compatible option, we will be using it to create our API calls.

There are quite a few settings for jQuery's ajax method, we will cover everything you need to make the vast majority of API calls you will make.

First, let's go over an example to illustrate the syntax. Let's get a list of all of the repositories Techlahoma has created on GitHub:

```javascript
    $.ajax(
        // Settings object
        {
            url:      'https://api.github.com/orgs/techlahoma/repos',
            method:   'GET',
            dataType: 'json',
            success: function(data){
                data.forEach(function(repo){
                    console.log(repo.name);
                    console.log(repo.html_url);
                    console.log('--------------------');
                });
            },
            error: function(xhr, status, err){
                console.error(err);
            }
        }
    );
```

## jQuery ajax

Let's go over some of the options available to us in the settings object passed to `$.ajax`.

Setting     | Use
----------- | --------------------------------------------------------------------
url         | The URL to which we are sending the request
method      | HTTP verb we want to use (GET, POST, PUT, DELETE, etc)
dataType    | Instructions jQuery on how to parse the response
success     | Success handler function
error       | Error handling function
contentType | The content type of the request body
data        | Data being sent with the request
headers     | Object containing key/value pairs of headers to set with the request


Note the the object returned by `$.ajax` implements the promise interface, which gives it all of the properties, methods, and behavior of a Promise.

With that in mind, we could also write our example like this:

```javascript
$.ajax(
    {
        url:      'https://api.github.com/orgs/techlahoma/repos',
        method:   'GET',
        dataType: 'json'
    }
).then(function(data){
    data.forEach(function(repo){
        console.log(repo.name);
        console.log(repo.html_url);
        console.log('--------------------');
    });
}).catch(function(xhr, status, err){
    console.log(err);
})
```

The `$.ajax` method also provides some other methods that can be chained on to the returned object. They include, but are not limited to `.done`, `.fail`, and `.always`. `done` will fire when the request completes successfully, `fail` fires on error, and `always` fires on success or failure.

Using this interface, we could also write our example like this:

```javascript
$.ajax(
    {
        url:      'https://api.github.com/orgs/techlahoma/repos',
        method:   'GET',
        dataType: 'json'
    }
).done(function(data){
    data.forEach(function(repo){
        console.log(repo.name);
        console.log(repo.html_url);
        console.log('--------------------');
    });
}).fail(function(xhr, status, err){
    console.log(err);
})
```

The previous example is the most common way to use `$.ajax`, though you should be familiar with the other methods.

## Resources

[GitHub API Reference for Repositories](https://developer.github.com/v3/repos/#list-organization-repositories)

[restapitutorial.com](http://www.restapitutorial.com/lessons/whatisrest.html)

[restcookbook.com](http://restcookbook.com)

[REST in Layman's Terms](https://www.quora.com/What-is-REST-in-laymans-terms)

[Understanding REST and RPC for HTTP APIs](https://www.smashingmagazine.com/2016/09/understanding-rest-and-rpc-for-http-apis/)
