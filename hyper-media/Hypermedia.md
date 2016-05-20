# Hypermedia

## {{ book.could }} Use HATEOAS

We prefer [REST Maturity Level 2](http://martinfowler.com/articles/richardsonMaturityModel.html#level2). Only if HATEOAS - as [Level 3](http://martinfowler.com/articles/richardsonMaturityModel.html#level3) - adds value to your API, consider using it. Because we are following API First, HATEOAS adds not much value for us in terms of self-descriptiveness. And we don't believe in generic HATEOAS clients which crawl and use APIs on their own. So, only use HATEOAS if your API really defines possible client actions (one part of hypermedia) and leverages other HATEOAS features.

Decoupled from HATEOAS, adding [links and relationships](#must-use-hal) should be considered as useful addition to APIs as it adds context and helps navigation in APIs to decent degree.

Additional resources: [Wikipedia](http://en.wikipedia.org/wiki/HATEOAS), [The RESTful CookBook](http://restcookbook.com/Basics/hateoas/)

## {{ book.must }} Use HAL

Links to other resources must be defined exclusively using [HAL](http://stateless.co/hal_specification.html) and
preferably using standard [link relations](http://www.iana.org/assignments/link-relations/link-relations.xml).

We opted for HAL after conduction a comparison of different hypermedia formats based on properties like:

* Simplicity: easy to understand and implement in a non-breaking way
* Adoption: use in open-source libraries and tools as well as other companies
* Docs: degree of good documentation

<p></p>

| Standard                                                       | Simplicity | Adoption | Primary Focus           | Docs | Last Update |
|----------------------------------------------------------------|------------|----------|-------------------------|------|-------------|
| [HAL](http://stateless.co/hal_specification.html)              | ✓          | ✓        | Links and relationships | ✓    | 2013-09-18  |
| [JSON API](http://jsonapi.org/)                                | ✗          | ✓        | Response format         | ✓    | 2015-05-29  |
| [JSON-LD](http://json-ld.org/)                                 | ✗          | ?        | Link data               | ?    | 2014-01-16  |
| [Siren](https://github.com/kevinswiber/siren)                  | ✗          | ✗        | Entities and navigation | ✗    | 2015-10-21  |
| [Collection+JSON](http://amundsen.com/media-types/collection/) | ✗          | ✗        | Collections and queries | ✗    | 2013-02-24  |

Interesting articles for comparisons of different hypermedia formats:
* [Kevin Sookocheff’s On choosing a hypermedia type for your API](http://sookocheff.com/post/api/on-choosing-a-hypermedia-format/)
* [Mike Stowe's API Best Practices: Hypermedia](http://blogs.mulesoft.com/dev/api-dev/api-best-practices-hypermedia-part-3/)

## {{ book.must }} Do Not Use Link Headers with JSON entities

We don't allow the usage of [`Link` headers](http://tools.ietf.org/html/rfc5988#section-5) and favor [HAL](#must-use-hal) instead.
The primary reason is to have a consistent place for links as well as the better support for links in JSON payloads compared
to the uncommon link header syntax.

## {{ book.could }} Use URIs for Custom Link Relations

You should consider using a custom link relation if and only if standard [link relations](http://www.iana.org/assignments/link-relations/link-relations.xml)
are not sufficient to express a relation.
Related or even embedded (sub-) resources should be linked via “meta link attributes” within the response payload; use
the following [HAL](http://stateless.co/hal_specification.html) compliant syntax:

    {
      ...
      “_links”: {
        “https://docs.team.zalan.do/rels/my-entity”: [{
          “href”: “https://service.team.zalan.do/my-entities/123”
        }]
      }
    }

## {{ book.should }} Allow Optional Embedding of Sub-Resources

Embedding related resources (also know as *Resource expansion*) is a great way to reduce the number of requests. In
cases where clients know upfront that they need some related resources they can instruct the server to prefetch that
data eagerly. Whether this is optimized on the server, e.g. a database join, or done in a generic way, e.g. an HTTP
proxy that transparently embeds resources, is up to the implementation.

See [*Conventional Query Strings*](../naming/Naming.md#could-use-conventional-query-strings) for naming.

## {{ book.must}} Modify the Content-Type for Embedded Resources

Embedded resources requires to change the media type of the response accordingly:

```http
GET /order/123?embed=(items) HTTP/1.1
Accept: application/x.order+json
```

```http
HTTP/1.1 200 OK
Content-Type: application/x.order+json;embed=(items)
```
