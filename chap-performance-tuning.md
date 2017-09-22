# Performance tuning

This chapter describes some recommended settings for improving the overall performance of WebCmsModule.

## URL resolving

The mapping infrastructure \(eg. `@WebCmsPageMapping`\) has as a drawback that any request will be matched against all registered `WebCmsUrl` entries.  It is strongly advised to configure the _WebCmsUrlCache_ to cache these lookups, as well as to limit the request paths for lookup if you can do so.

By default all requests will be matched against the `WebCmsUrl` collection.  You can control which requests should be matched by specifying one ore more ANT path patterns in the relevant settings.

### URL resolve path properties

| Property | Description |
| :--- | :--- |
| webCmsModule.urls.included-path-patterns | Paths to be included for `WebCmsUrl` mapping.  If empty, all non-excluded paths will be considered.  If not empty, only requests matching one of these paths will be mapped to a possible `WebCmsUrl`. |
| webCmsModule.urls.excluded-path-patterns | Paths to always be excluded from `WebCmsUrl` mapping.  If empty, only included paths will be considered.  If not empty, requests matching these patterns will be excluded, even if they match the included patterns. |

> **WARNING**
>
> If URL resolving is disabled for a particular path, `@WebCmsEndpointMapping` handler methods that use a `WebCmsUrl` with that path will never match.  Disable URL resolving only if you are sure you will handle all requests for these paths manually.

## Caching

WebCmsModule uses some specific caches to improve performance.  It is recommended to configure these caches in any production application.

### WebCmsModule custom caches

| Cache name | Description |
| :--- | :--- |
| WebCmsUrlCache | Maps request paths to matching `WebCmsUrl` instances. |
| WebCmsMenuCache | Caches the menu structure that should be generated from corresponding `WebCmsMenu` items. |

### Hibernate level 2 cache

To improve overall performance of the domain model, configuring Hibernate level 2 cache is also strongly advised.

