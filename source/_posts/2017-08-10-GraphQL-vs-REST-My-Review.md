---
title: GraphQL vs REST - My Review
date: 2017-08-10 22:31:47
tags: [GraphQL, REST, web, network, HTTP/HTTPS, backend]
---

# Prologue
I heard there is a hype train of GraphQL. In order to find out how good it is, I decided to give GraphQL a try in my recently made side project - [graphql-sso-example](https://github.com/zushenyan/graphql-sso-example).

And yep, it's a practice of GraphQL and SSO(Single Sign On). You can view the demo [here](https://graphql-sso.herokuapp.com/#/).

So there are already tons of articles talking about the comparison between REST and GraphQL, and combine with public article and my review. Here is the short version:

# GraphQL vs REST - General Comparison
### No Custom Status Code
Since GraphQL doesn't care about transport layer error, You can't customize status code like REST.

The only way for clients to get them is putting them in the resolver (e.g. JSON response).

### Increase Development Time
GraphQL is a whole standalone tool for solving how APIs should present. You have to define schemes for a simple API response, which is an overkill sometimes.

### REST Can Be Query Language Too
GraphQL is an query language, and actually, you can make REST doing so by adding query to URL like `[GET] /api/user?include=email,name,age`.

### Cache Missing
Since all the GraphQL requests are sent in `POST` method, you can't use `browser caching` (with HTTP headers `etag` or `cache-control`) and `network caching`(reverse proxy like [varnish](https://www.varnish-cache.org/)).

The only way to cache it is by using `application caching`, for example Facebook's [dataloader](https://github.com/facebook/dataloader).

However if you use REST in query `include` style, it also shares the similar issue.

# TL;DR Conclusion
GraphQL is not an replacement for REST. There is no winner. Both of them have its strength and weakness.

If you don't have data with highly relations dependency or the need for supporting various platforms (e.g. mobile, desktop, TV...), you probably don't need GraphQL.

Since there is no exclusion between two, try using both of them if you really need to.

# References
- [GraphQL vs REST: Overview](https://philsturgeon.uk/api/2017/01/24/graphql-vs-rest-overview/)
- [GraphQL vs REST: Caching](https://philsturgeon.uk/api/2017/01/26/graphql-vs-rest-caching/)
