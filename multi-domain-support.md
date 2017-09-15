# Multi-domain and multi-site support

The default configuration of WebCmsModule considers all entities as part of a single pool of data \(the "domain"\).  Usually a domain corresponds with a single website, often represented by an actual DNS record.

WebCmsModule also supports multiple domains within a single application, with a domain being represented by a `WebCmsDomain` entity.  Default configuration is provided for the use case where a domain corresponds with a website.  However a `WebCmsDomain` is an abstract grouping of asset and configuration data, and the domain concept could be used in an entirely different fashion if so wanted.

Any entity that can be attached to a particular `WebCmsDomain` should implement the `WebCmsDomainBound` interface.  WebCmsModule will automatically reconfigure EntityModule settings for any `WebCmsDomainBound` entity, based on the multi-domain configuration provided.

All default implementations provided by WebCmsModule implement this interface.  When using one of the default base classes \(eg. `WebCmsObjectSuperClass`\), you automatically implement `WebCmsDomainBound`.

## Configuring multi-domain support in your application

By default, multi-domain support is disabled entirely.  Entities will never be attached to a domain and - if the administration UI is active - you will not be offered any domain related management options.

You can activate multi-domain support by providing a `WebCmsMultiDomainConfiguration` bean in your application.  WebCmsModule will inspect the configuration bean and setup its basic infrastructure accordingly.  This does not alter the database schema in any way, but it does configure a lot of default behaviour of your application.

> Because a lot of configuration options are altered based on your `WebCmsMultiDomainConfiguration`, this bean should be available before the WebCmsModule bootstraps.  You should either provide this bean on the application or AcrossContext level, or in a `@ModuleConfiguration` extension directly in the WebCmsModule.

TODO: example of providing a WebCmsMultiDomainConfiguration bean

##### WebCmsDomain used to represent "no-domain"

If your application is configured in a single-domain, the actual `WebCmsDomain` used is the `null` value.  For programmatic purposes you can use the `WebCmsDomain.NONE` constant.

In a multi-domain configuration,  `WebCmsDomain.NONE` \(`null`\) represents items shared across all domains.

> ##### NOTE
>
> Switching from a single-domain to a multi-domain configuration \(or vice versa\) after data has been added to your application is not advised.  It will usually require manual actions to ensure all data is still accessible and attached to the proper domain.

### Multi-domain configuration options

TODO: explain the different options of WebCmsMultiDomainConfiguration

#### EntityModule auto-configuration

WebCmsModule attempts to auto-configure the EntityModule entities if multi-domain support is active.  Depending on your multi-domain configuration the following entity configurations will be modified:

1. list views of `EntityConfiguration` and `EntityAssociation` will be modified with domain aware base predicates \(this ensures that you will only see entities bound to the domain your are managing\)
2. options queries of entities (`EntityAttributes.OPTIONS_ENTITY_QUERY`) will be modified so you can only select entities of the currently selectable domains
3. the `EntityModel` of `WebCmsDomainBound` entities will be adjusted with a custom `EntityFactory` that will pre-set the currently selected domain
4. the `EntityConfigurationAllowableActionsBuilder` of all entities will be wrapped with a domain-aware actions builder that will deny any action attempting to modify an entity on a domain it does not belong to

In many cases the auto-configuration will be just what you need and there won't be any need for tweaking.  However if you want to manually configure some of your entities, you can force the auto-configuration parts to be skipped.

##### Auto-configuration related attributes

Domain auto-configuration is performed during a post-processing of all entity configurations.  Custom attributes allow you to skip parts of the auto-configuration or to tweak auto-configuration settings.

###### Skipping automatic list view adjustment

When you manually configure a list view with a domain specific base predicate, you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.LIST_VIEW_ADJUSTED` to `true`.  This attribute is supported on any `EntityConfiguration` or `EntityAssociation`.

###### Skipping automatic options query adjustment

When you manually configure the options filtering of an entity (e.g. by setting `EntityAttributes.OPTIONS_ENTITY_QUERY` you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.OPTIONS_QUERY_ADJUSTED` to `true`.  This attribute is supported on any `EntityConfiguration` or `EntityPropertyDescriptor`.

###### Skipping automatic EntityModel adjustment

If you don't want the default `EntityFactory` to be modified, you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.ENTITY_MODEL_ADJUSTED` to `true` on the `EntityConfiguration`.

###### Skipping automatic AllowableActions adjustment

If you don't want the default `EntityConfigurationAllowableActionsBuilder` to be modified, you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.ALLOWABLE_ACTIONS_ADJUSTED` to `true` on the `EntityConfiguration`.

###### Skipping auto-configuration of an entity

If you want to skip the entire auto-configuration of an `EntityConfiguration` you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.FINISHED` to `true` on that `EntityConfiguration`.

This will ensure no processing is done on the `EntityConfiguration` or any of its registered associations.

###### Setting a custom property representing the WebCmsDomain

If you want to activate \(partial\) multi-domain auto-configuration for entities not implementing `WebCmsDomainBound`, you can specify an explicit property that links to the `WebCmsDomain` by setting `WebCmsEntityAttributes.DOMAIN_PROPERTY` on the `EntityConfiguration`.

**An example:**

`WebCmsUrl` does not implement `WebCmsDomainBound`.  But a `WebCmsUrl` is linked to a `WebCmsEndpoint` that does  implement `WebCmsDomainBound`, so an URL is also bound implicitly.  To auto-configure the domain-based filtering for `WebCmsUrl`: set `WebCmsEntityAttributes.DOMAIN_PROPERTY` to **endpoint.domain**.

### Customizing a single domain configuration: metadata

`WebCmsDomain` is a simple entity with support for an infinite number of String based attributes.  Because this is usually not very efficient to work with, you can implement a `WebCmsDomainData` class that wraps around a `WebCmsDomain` providing strong-typed access to domain-related configuration properties.

TODO: explain the WebCmsDomainAware interface

TODO: example with the default dns based implementation

## Using domains

This section explains how you can use the domain concept directly in your controllers or business logic.

### Accessing the current domain: WebCmsDomainContext

TODO: explain WebCmsDomainContext and WebCmsDomainContextHolder

Mapping handler methods to domain

use @WebCmsDomainMapping  or use domain attribute on other mapping annotations

### Entity Query Language extensions

TODO: document and explain the extensions

* currentDomain\(\)

explain WebCmsEntityAttributes

### Resolving the current domain

TODO: explain cookie based resolver and dns based resolver

## Manually adding multi-domain support to your entities

Default configuration changes are the following:

* customize the EntityFactory on the EntityConfiguration
* default selection options for that type are set to match only the current domain
* list views are modified to only show the items attached to the current domain

TODO: how to implement domain bound validation for an entity \(eg name must be unique within the domain\)

Repositories & services

* repositories are usually used in the backend - especially if you want to support both multi-domain and no-domain configurations
* services usually use the current domain to interact with the repository, making them very easy to use in frontend business logic

## Importing domain configuration

`WebCmsDomain` is a `WebCmsObject` like most other entities provided by WebCmsModule.  It has full support for being imported using YAML \(or another format - see the section [Importing Data](/chap-placeholder.adoc) for more information\).

TODO: example

### Importing domain configuration for WebCmsObjects

All default `WebCmsObject`s \(`WebCmsPage`, `WebCmsMenu`...\) are domainbound. This means that their identifiers can be reused across different domains. To attach a `WebCmsObject`to a specific `WebCmsDomain`you only need to add the _domain_ property in the yaml configuration. The domain property can be any of the following:

* objectId of the WebCmsDomain \(e.g. `"wcm:domain:my-domain"` \)
* the domainKey \(e.g. `my-domain`\)

Example domainbound menu import

```yaml
menus:
 topNav:
   description: Top navigation
   domain: my-domain
 sideNav:
   description: Side navigation
   domain: "wcm:domain:my-domain"
```

To import a `WebCmsTypeSpecifier`you are required to prefix the _typeKey_ with the attached domain for all _newly_ created types. This is however not required when updating existing types.

Example domainbound component type  import

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

### Scoping imports to a domain

With the use of `wcm:domain` it is possible to set the domain of the surrounding block and it's children to the specified domain.

Example scoped imports:

```yaml
wcm:domain: my-domain
types:
    component:
        my-teaser:
            name: My teaser
            attributes:
                componentType: fixed-container
            wcm:components:
                componentTemplate:
                    componentType: container
                    wcm:components:
                        body:
                            componentType: rich-text

assets:
    component:
        my-teaser:
            name: My component
            componentType: my-teaser
            wcm:components:
                body:
                    content: My teaser body
```

* All `WebCmsDomainBound` objects will be imported under _my-domain_

```yaml
types:
    component:
        my-teaser:
            name: My teaser
            attributes:
                componentType: fixed-container
            wcm:components:
                componentTemplate:
                    componentType: container
                    wcm:components:
                        body:
                            componentType: rich-text

assets:
    wcm:domain: my-domain
    component:
        my-teaser:
            name: My component
            componentType: my-teaser
            wcm:components:
                body:
                    content: My teaser body
```

* All `WebCmsDomainBound` assets will be imported under _my-domain_

```yaml
types:
    component:
        my-teaser:
            name: My teaser
            attributes:
                componentType: fixed-container
            wcm:components:
                componentTemplate:
                    componentType: container
                    wcm:components:
                        body:
                            componentType: rich-text

assets:
    component:
        wcm:domain: my-domain
        my-teaser:
            name: My component
            componentType: my-teaser
            wcm:components:
                body:
                    content: My teaser body
```

* All `WebCmsDomainBound` component assets \(=global components\) and their children will be imported under _my-domain. _

```yaml
types:
    component:
        my-teaser:
            name: My teaser
            attributes:
                componentType: fixed-container
            wcm:components:
                componentTemplate:
                    componentType: container
                    wcm:components:
                        body:
                            componentType: rich-text

assets:
    component:
        my-teaser:
            wcm:domain: my-domain
            name: My component
            componentType: my-teaser
            wcm:components:
                body:
                    content: My teaser body
```

* The component _my-teaser_ and it's child component, _body_, will be imported under _my-domain_.

Note: In a multi-domain supported setup, all newly created `WebCmsDomainBound` objects will be imported under their currently scoped domain \(by default: no domain\). The domain of a specific entry can still be changed by using the _domain_ property.

