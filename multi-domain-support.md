# Multi-domain support

The default configuration of WebCmsModule considers all entities as part of a single pool of data \(the "domain"\).  Usually a domain corresponds with a single website, often represented by an actual DNS record.

WebCmsModule also supports multiple domains within a single application, with a domain being represented by a `WebCmsDomain` entity.  Default configuration is provided for the use case where a domain corresponds with a website.  However a `WebCmsDomain` is an abstract grouping of asset and configuration data, and the domain concept could be used in an entirely different fashion if so wanted.

Any entity that can be attached to a particular `WebCmsDomain` should implement the `WebCmsDomainBound` interface.  WebCmsModule will automatically reconfigure EntityModule settings for any `WebCmsDomainBound` entity, based on the multi-domain configuration provided.

All default implementations provided by WebCmsModule implement this interface.  When using one of the default base classes \(eg. `WebCmsObjectSuperClass`\), you automatically implement `WebCmsDomainBound`.

## Configuring multi-domain support in your application

By default, multi-domain support is disabled entirely.  Entities will never be attached to a domain and - if the administration UI is active - you will not be offered any domain related management options.

You can activate multi-domain support by providing a `WebCmsMultiDomainConfiguration` bean in your application.  WebCmsModule will inspect the configuration bean and setup its basic infrastructure accordingly.  This does not alter the database schema in any way, but it does configure a lot of default behaviour of your application.

> Because a lot of configuration options are altered based on your `WebCmsMultiDomainConfiguration`, this bean should be available before the WebCmsModule bootstraps.  You should either provide this bean on the application or AcrossContext level, or in a `@ModuleConfiguration` extension directly in the WebCmsModule.

TODO: example of providing a WebCmsMultiDomainConfiguration bean

### Multi-domain configuration options

TODO: explain the different options of WebCmsMultiDomainConfiguration

### Customizing a single domain configuration: WebCmsDomainData

`WebCmsDomain` is a simple entity with support for an infinite number of String based attributes.  Because this is usually not very efficient to work with, you can implement a `WebCmsDomainData` class that wraps around a `WebCmsDomain` providing strong-typed access to domain-related configuration properties.

TODO: explain the WebCmsDomainData interface

TODO: example with the default dns based implementation

## Using domains

This section explains how you can use the domain concept directly in your controllers or business logic.

### Accessing the current domain: WebCmsDomainContext

TODO: explain WebCmsDomainContext and WebCmsDomainContextHolder

### Entity Query Language extensions

TODO: document and explain the extensions

* currentDomain\(\)

### Resolving the current domain

TODO: explain cookie based resolver and dns based resolver

## Manually adding multi-domain support to your entities

Default configuration changes are the following:

* customize the EntityFactory on the EntityConfiguration
* default selection options for that type are set to match only the current domain
* list views are modified to only show the items attached to the current domain

TODO: how to implement domain bound validation for an entity \(eg name must be unique within the domain\)

## Importing domain configuration

`WebCmsDomain` is a `WebCmsObject` like most other entities provided by WebCmsModule.  It has full support for being imported using YAML \(or another format - see the section [Importing Data](/chap-placeholder.adoc) for more information\).

TODO: example

### Importing domain configuration for WebCmsObjects

All default `WebCmsObject`s \(`WebCmsPage`, `WebCmsMenu`...\), with the exception of `WebCmsTypeSpecifier`s are domainbound. This means that their identifiers can be reused across different domains. To attach a `WebCmsObject `to a specific `WebCmsDomain `you only need to add the _domain _property in the yaml configuration. The domain property can be any of the following:

* objectId of the WebCmsDomain \(e.g. `"wcm:domain:my-domain"` \)
* the domainKey \(e.g. `my-domain`\)

Example domainbound menu import - YAML

```yaml
menus:
 topNav:
   description: Top navigation
   domain: my-domain
 sideNav:
   description: Side navigation
   domain: "wcm:domain:my-domain"
```

To import a `WebCmsTypeSpecifier `you are required to prefix the _typeKey _with the attached domain for all _newly _created types. This is however not required when updating existing types.

Example domainbound component type  import - YAML

```yaml
types:
  component:
    my-domain:my-teaser:
      name: My teaser
      domain: my-domain
    my-domain:another-teaser:
      name: Another teaser
      domain: "wcm:domain:my-domain"
```

TODO: wcm:domain block

