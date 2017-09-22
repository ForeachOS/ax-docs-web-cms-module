### Repositories and services overview

This section lists the most important repositories and services that are provided by WebCmsModule.  These beans are all exposed and can be use in all modules that depend on WebCmsModule.

repositories always require a domain to be set

services abstract from domain and implement the default domain logic

repositories will never return cached items, whereas services might

## Repository beans

Repositories provide direct access to the stored entities.  Use these if you want to interact directly with the datastore.  All repositories of WebCmsModule entities implement both `JpaSpecificationExecutor` and `QueryDslPredicateExecutor`.



## Service beans

### Core services

The core services are always created when WebCmsModule is enabled in your application.

Entity services

WebCmsPublicationService

General usage

### Admin UI services

These are only available when EntityModule and AdminWebModule are enabled in your application.  The focus of these services is to help you build or customize the administration UI.



