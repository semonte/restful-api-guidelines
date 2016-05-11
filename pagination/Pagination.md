# Pagination

## {{ book.must }} Support Pagination

For client side batch processing and iteration experience, access to lists of data items must support pagination. This holds for all lists that are (potentially) larger than just a
few hundred entries.

There are two page iteration techniques:

* [Offset/Limit-based pagination](http://developer.infoconnect.com/paging-results-limit-and-offset):
  numeric offset identifies the first page entry
* [Cursor-based](https://dev.twitter.com/overview/api/cursoring) — aka key-based — pagination: a
  unique key element identifies the first page entry (see also
  [Facebook’s guide](https://developers.facebook.com/docs/graph-api/using-graph-api/v2.4#paging))

The technical conception of pagination should also consider user experience related issues. As mentioned
in this [article](https://www.smashingmagazine.com/2016/03/pagination-infinite-scrolling-load-more-buttons/),
jumping to a specific page is far less used than navigation via next/previous page links. This favours
cursor-based over offset-based pagination.

## {{ book.should }} Prefer Cursor-Based Pagination, Avoid Offset-Based Pagination

Cursor-based pagination is better for more efficient implementation, especially when it comes to
high-data volumes and / or storage in NoSQL databases.

Before choosing offset-based pagination,  make sure that it is feasible for efficient realization.
Carefully consider the following trade-offs:

* Usability/framework support:

    * offset/limit pagination is more familiar than cursor-based, so it has more framework support and
      is easier to use for API clients

* Use case: Jump to a certain page

    * If jumping to a particular page in a range (e.g., 51 of 100) is really a required use case,
      cursor-based navigation is not feasible

* Variability of data may lead to anomalies in result pages

    * Using offset will create duplicates or lead to missed entries if rows are inserted or  deleted,
      respectively, between fetching two pages
    * When using cursor-based pagination, paging cannot continue when the cursor entry has been
      deleted while fetching two pages

* Performance considerations
  Efficient server-side processing using offset-based pagination is hardly feasible for...

    * higher data list volumes, especially if they do not reside in the database’s main memory
    * sharded or NoSQL databases

  This also holds for total count and backward iteration support

Further reading:

* [Twitter](https://dev.twitter.com/rest/public/timelines)
* [Use the Index, Luke](http://use-the-index-luke.com/no-offset)

## {{ book.could }} Use Pagination Links Where Applicable

* Set `X-Total-Count` to send back the total count of entities.
* Set links to provide information to the client about subsequent paging options.

For example:

```http
HTTP/1.1 200 OK
Content-Type: application/hal+json
X-Total-Count: 1309

{
  "_links": {
    "next": {
      "href": "http://catalog-service.zalando.net/products?offset=15&limit=5"
    },
    "prev": {
      "href": "http://catalog-service.zalando.net/products?offset=5&limit=5"
    }
  },
  "products": [
    {"id": "7e4ab218-1772-11e6-892c-836df3feeaee"},
    {"id": "9469725a-1772-11e6-83c2-ab22ac368913"},
    {"id": "a3625d80-1772-11e6-9213-d3a20f9e6bf4"},
    {"id": "a802f070-1772-11e6-a772-c3020d55eb5f"},
    {"id": "adb407ac-1772-11e6-b255-078fb28ff55b"}
  ]
}
```

[Possible relations](http://www.iana.org/assignments/link-relations/link-relations.xml):
`next`, `prev`, `last`, `first`
