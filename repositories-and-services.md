### Repositories and services overview

This section lists the most important repositories and services that are provided by WebCmsModule.  These beans are all exposed and can be use in all modules that depend on WebCmsModule.

Use repositories to directly access the datastore.  Repository methods are multi-domain agnostic and usually require an explicit WebCmsDomain as parameter to your queries.  Repositories will also never return cached items.

Repositories are all implemented using Spring Data JPA.

Services usually are domain aware and will inspect the WebCmsMultiDomainConfiguration to automatically adjust queries to match for the domain bound to a thread.  This makes them transparent in use, especially for front-end application building.  Services will also use available caches and can return cached items.

Administration UIs will often require more direct use of repositories, whereas services provide convenience methods to avoid too much domain related clotter in your application code.

## Repository beans

Repositories provide direct access to the stored entities.  Use these if you want to interact directly with the datastore.  All repositories of WebCmsModule entities implement both `JpaSpecificationExecutor` and `QueryDslPredicateExecutor`.  Q classes \(e.g. `QWebCmsArticle`\) are also available for all WebCmsModule entities.  Pretty much every entity has its own repository.

* general
  * `WebCmsDomainRepository`
  * `WebCmsComponentRepository`
  * `WebCmsMenuRepository`
  * `WebCmsMenuItemRepository`
* types
  * `WebCmsTypeSpecifierRepository`
  * `WebCmsArticleTypeRepository`
  * `WebCmsComponentTypeRepository`
  * `WebCmsPageTypeRepository`
  * `WebCmsPublicationTypeRepository`
* assets
  * `WebCmsAssetRepository`
  * `WebCmsArticleRepository`
  * `WebCmsImageRepository`
  * `WebCmsPageRepository`
  * `WebCmsPublicationRepository`
* endpoints
  * `WebCmsEndpointRepository`
  * `WebCmsAssetEndpointRepository`
  * `WebCmsRemoteEndpointRepository`
  * `WebCmsUrlRepository`
* entity links
  * `WebCmsTypeSpecifierLinkRepository`

> **NOTE**
>
> Working with `WebCmsComponent` is usually not done directly but through a `WebCmsComponentModel`.  The `WebCmsComponentModelService` is probably all you need, but if not, you will almost always want to build the `WebCmsComponentModel` for your fetched components using the `WebCmsComponentModelService`.

When implementing your own `WebCmsAsset`, `WebCmsTypeSpecifier` or basic `WebCmsObject`, you should check the generic interfaces for reuse:

* `WebCmsObjectRepository`: basic contract for fetching a `WebCmsObject` by object id
* `WebCmsObjectEntityRepository`: base Spring Data repository interface for entities extending `WebCmsObjectSuperClass`
* `BaseWebCmsTypeSpecifier`: base Spring Data repository for custom `WebCmsTypeSpecifier` implementations

## Service beans

### Core services

The core services are always created when WebCmsModule is enabled in your application.

| Service | Description |
| :--- | :--- |
| `WebCmsArticleService` | `WebCmsArticle` and `WebCmsArticleType` functionality. |
| `WebCmsAssetService` | Generic asset related functions \(e.g. generate preview URL for an asset\). |
| `WebCmsEndpointService` | URL \(for asset\) related functions. |
| `WebCmsMenuService` | `WebCmsMenu` functionality. |
| `WebCmsPageService` | `WebCmsPage` and `WebCmsPageType` functionality. |
| `WebCmsPublicationService` | `WebCmsPublication` functionality. |
| `WebCmsComponentModelService` | Loading and updating components. |
| `WebCmsComponentModelHierarchy` | Request scoped bean that can be used to register components to render on the current request. |
| `WebCmsContentMarkerService` | Functionality for working with [content markers](/components/chap-web-components-content-markers.adoc) in text. |
| `WebCmsDataConversionService` | Specific Spring `ConversionService` implementation used for data importing.  Contains specific converters for WebCmsModule types. |
| `WebCmsDataImportService` | General service for importing WebCmsModule data from generic `Map<String,Object>` structures. |
| `WebCmsDefaultComponentsService` | Service for creating default components for any `WebCmsAsset` by checking for a **contentTemplate** attached to the `WebCmsAssetType`. |
| `WebCmsMultiDomainService` | Service for accessing the current domain context and inspecting the multi-domain configuration. |
| `WebCmsPlaceholderLookupService` | Request scoped service for retrieving placeholder content registered on the current request. |
| `WebCmsPlaceholderContentModel` | Request scoped bean to set and retrieve placeholder data for the current request. |
| `WebCmsRenderUtilityService` | Provides [utility functions](/thymeleaf-dialect.adoc) for using during output rendering. |
| `WebCmsTypeSpecifierService` | Functionality for retrieving type specifiers. |
| `WebCmsUriComponentsService` | Utility service for creating a `UriComponentsBuilder` for either `WebCmsAsset`, `WebCmsEndpoint` or `WebCmsUrl`. |
| `WebCmsComponentAutoCreateService` | Service for automatic creation of `WebCmsComponent` based on parsed template content.  Mostly used internally, you can use this service if you want to implement your own auto-create functionality. |

### Admin UI services

These are only available when EntityModule and AdminWebModule are enabled in your application.  The focus of these services is to help you build or customize the administration UI.

| Service | Description |
| :--- | :--- |
| `WebCmsComponentModelAdminRenderService` | Service for building `WebCmsComponentModel` administration form elements. |
| `WebCmsMultiDomainAdminUiService` | Helper service for retrieving multi-domain configuration for the current request in an admin web context.  Utility functions for building domain-aware filters. |



