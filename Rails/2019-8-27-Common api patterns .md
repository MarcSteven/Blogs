# Common api patterns 

| URI Pattern | Description |
| --- | --- |
| example.com/api/ | This pattern is very common, and it’s actually a good way to go when you don’t want to namespace your api under a subdomain |
| example.com/api/v1 | This seems like a good idea, by setting the version ot the API through the URL seems like a more descriptive pattern, but this way you enforce the version to be included on URL on each request, so if you ever decide to change this pattern, this becomes a problem of maintenance in the long-term. |
| api.example.com/ | In my view this is the way to go ,gives you a better interface and isolation, and in the long term can help you to quick scalate() |

# Useful resources to design your API
- [Best practices api design](https://docs.microsoft.com/zh-cn/azure/architecture/best-practices/api-design)
-  [public interface](https://martinfowler.com/ieeeSoftware/published.pdf)
-  [tools to design your api](https://swagger.io/tools/swaggerhub/)



