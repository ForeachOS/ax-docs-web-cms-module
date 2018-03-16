# Multi-domain and multi-site support

The default configuration of WebCmsModule considers all entities as part of a single pool of data \(the "domain"\).  Usually a domain corresponds with a single website, often represented by an actual DNS record.

WebCmsModule also supports multiple domains within a single application, with a domain being represented by a `WebCmsDomain` entity.  Default configuration is provided for the use case where a domain corresponds with a website.  However a `WebCmsDomain` is an abstract grouping of asset and configuration data, and the domain concept could be used in an entirely different fashion if so wanted.

Any entity that can be attached to a particular `WebCmsDomain` should implement the `WebCmsDomainBound` interface.  WebCmsModule will automatically reconfigure EntityModule settings for any `WebCmsDomainBound` entity, based on the multi-domain configuration provided.

All default implementations provided by WebCmsModule implement this interface.  When using one of the default base classes \(eg. `WebCmsObjectSuperClass`\), you automatically implement `WebCmsDomainBound`.

---

**In a multi-domain setup, entities not attached to a specific domain are shared across all domains.  Entities attached to a single domain are available only on that specific domain.**

---

## Configuring multi-domain support in your application

By default, multi-domain support is disabled entirely.  Entities will never be attached to a domain and - if the administration UI is active - you will not be offered any domain related management options.

You can activate multi-domain support by providing a `WebCmsMultiDomainConfiguration` bean in your application.  WebCmsModule will inspect the configuration bean and setup its basic infrastructure accordingly.  This does not alter the database schema in any way, but it does configure a lot of default behaviour of your application.

###### Example activating multi-domain support with default configuration

```java
@Bean
public WebCmsMultiDomainConfiguration multiDomainConfiguration() {
    return WebCmsMultiDomainConfiguration.managementPerDomain().build();
}
```

> **WARNING**
>
> Because a lot of configuration options are altered based on your `WebCmsMultiDomainConfiguration`, this bean should be available before the WebCmsModule bootstraps.  You should either provide this bean on the application or AcrossContext level, or in a `@ModuleConfiguration` extension directly in the WebCmsModule.
>
> Once the application has been started, you can no longer modify the multi-domain configuration.

### Multi-domain configuration options

The `WebCmsMultiDomainConfiguration` allows you to configure which entities are supposed to be domain-bound, and which are shared across domains.  Any domain-bound entity will be linked to a specific `WebCmsDomain`, whereas shared entities will have a `null` value for their `WebCmsDomain`.

#### Administration UI presets

The `WebCmsMultiDomainConfiguration` has 2 configuration presets:

* The `managementPerDomain()` option will configure the EntityModule and administration UI so you select the specific domain you wish to manage. This is often the most practical use case where you never manually select the domain for an asset as it is determined by the context you are managing.
* The `managementPerEntity()` option will configure the administration UI to allow you to select the right domain per entity.  This is a much more complex configuration that allows the user to be very aware of how entities can be linked across domains.  Usually a lot more application specific configuration will be required for this option.

Either preset allows you to configure the domain bound entities in exactly the same way.

#### Configuring domain-bound entities

You can configure exactly which entities are domain-bound and which are not.

* a non-domain-bound entity only allows a `null` value for its `WebCmsDomain`
* a domain-bound entity allows a specific value for its `WebCmsDomain` and - depending on the configuration - could possibly also allow a `null` value for its `WebCmsDomain`

> **NOTE**
>
> In a management per domain configuration, non-domain-bound entities would be managed under the shared settings.

The default multi-domain configurations apply the following rules:

1. any `WebCmsDomainBound` instance is configured as domain-bound and requiring a specific domain
2. a `WebCmsTypeSpecifier` is configured as not-domain-bound - shared across all domains
3. a `WebCmsComponent` is configured as domain-bound but no domain is required, allowing components to be set per domain or to be shared across all domains

The more specific rules for `WebCmsTypeSpecifier` and `WebCmsComponent` overrule the initial `WebCmsDomainBound` rule.

You can set configure domain-bound rules on the `WebCmsMultiDomainConfiguration`:

* use `domainBoundTypes()` to specify types that should be domain bound
* use `domainIgnoredTypes(`\)  to specify non-domain-bound types
* use `noDomainAllowedTypes()` to specify for which domain-bound types the domain is optional

> **NOTE**
>
> If you want to disable shared settings altogether, you can do so using the `noDomainAllowed()` option.

###### Example multi-domain configuration that shares the WebCmsImage asset across all domains

```java
@Bean
public WebCmsMultiDomainConfiguration multiDomainConfiguration() {
    return WebCmsMultiDomainConfiguration.managementPerEntity()
                                         .domainIgnoredTypes( WebCmsImage.class )
                                         .build();
}
```

#### Configuring domain metadata and domain filter

The `WebCmsMultiDomainConfiguration` also allows you to specify the type of domain metadata your application should use, as well as the Servlet filter that should be used to select the domain in the front-end.  These options will apply regardless of

The default domain metadata is implemented as `WebCmsSiteConfiguration` and represents a single website.  The default domain filter is an implementation of `WebCmsSiteConfigurationFilter` that selects the domain based on the hostname of the incoming request.

For more information on domain metadata, see the [separate chapter](#domain-metadata).

#### WebCmsDomain used to represent "no-domain"

If your application is configured in a single-domain, the actual `WebCmsDomain` used is the `null` value.  For programmatic purposes you can use the `WebCmsDomain.NONE` constant.

In a multi-domain configuration,  `WebCmsDomain.NONE` \(`null`\) represents the items shared across all domains.

> ##### WARNING
>
> Switching from a single-domain to a multi-domain configuration \(or vice versa\) after data has been added to your application is not advised.  It will usually require manual actions to ensure all data is still accessible and attached to the proper domain.
>
> If you switch from a multi-domain configuration to a single-domain configuration, you will only be able to access items that were not attached to a specific domain

#### EntityModule auto-configuration

WebCmsModule attempts to auto-configure the EntityModule entities if multi-domain support is active.  Depending on your multi-domain configuration the following entity configurations will be modified:

1. list views of `EntityConfiguration` and `EntityAssociation` will be modified with domain aware base predicates \(this ensures that you will only see entities bound to the domain your are managing\)
2. options queries of entities \(`EntityAttributes.OPTIONS_ENTITY_QUERY`\) will be modified so you can only select entities of the currently selectable domains
3. the `EntityModel` of `WebCmsDomainBound` entities will be adjusted with a custom `EntityFactory` that will pre-set the currently selected domain
4. the `EntityConfigurationAllowableActionsBuilder` of all entities will be wrapped with a domain-aware actions builder that will deny any action attempting to modify an entity on a domain it does not belong to

In many cases the auto-configuration will be just what you need and there won't be any need for tweaking.  However if you want to manually configure some of your entities, you can force the auto-configuration parts to be skipped.

##### Auto-configuration related attributes

Domain auto-configuration is performed during a post-processing of all entity configurations.  Custom attributes allow you to skip parts of the auto-configuration or to tweak auto-configuration settings.  For all WebCmsModule related entity attirbutes, see [the relevant appendix](/docs/entitymodule-attributes.md).

###### Skipping automatic list view adjustment

When you manually configure a list view with a domain specific base predicate, you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.LIST_VIEW_ADJUSTED` to `true`.  This attribute is supported on any `EntityConfiguration` or `EntityAssociation`.

###### Skipping automatic options query adjustment

When you manually configure the options filtering of an entity \(e.g. by setting `EntityAttributes.OPTIONS_ENTITY_QUERY` you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.OPTIONS_QUERY_ADJUSTED` to `true`.  This attribute is supported on any `EntityConfiguration` or `EntityPropertyDescriptor`.

###### Skipping automatic EntityModel adjustment

If you don't want the default `EntityFactory` to be modified, you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.ENTITY_MODEL_ADJUSTED` to `true` on the `EntityConfiguration`.

###### Skipping automatic AllowableActions adjustment

If you don't want the default `EntityConfigurationAllowableActionsBuilder` to be modified, you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.ALLOWABLE_ACTIONS_ADJUSTED` to `true` on the `EntityConfiguration`.

###### Skipping auto-configuration of an entity entirely

If you want to skip the entire auto-configuration of an `EntityConfiguration` you should set the attribute `WebCmsEntityAttributes.MultiDomainConfiguration.FINISHED` to `true` on that `EntityConfiguration`.

This will ensure no processing is done on the `EntityConfiguration` or any of its registered associations.

###### Setting a custom property representing the WebCmsDomain

If you want to activate \(partial\) multi-domain auto-configuration for entities not implementing `WebCmsDomainBound`, you can specify an explicit property that links to the `WebCmsDomain` by setting `WebCmsEntityAttributes.DOMAIN_PROPERTY` on the `EntityConfiguration`.

**An example:**

`WebCmsUrl` does not implement `WebCmsDomainBound`.  But a `WebCmsUrl` is linked to a `WebCmsEndpoint` that does  implement `WebCmsDomainBound`, so an URL is also bound implicitly.  To auto-configure the domain-based filtering for `WebCmsUrl`: set `WebCmsEntityAttributes.DOMAIN_PROPERTY` to **endpoint.domain**.

#### Management per domain options

This section explains some additional configuration options related to a management per domain setup.

##### Domain selector menu

In management per domain configuration, the administration ui will show a domain selector menu if the user can access more than one domain.  If no-domain is allowed according to the configuration and the user has the ability to manage domains themselves, a _shared settings_ option will be added.

Customizing the default labels can be done through the following message codes:

* `webCmsModule.menu.domainNav.switchDomain`: label for the selector itself \(default: _Switch domain_\)
* `webCmsModule.menu.domainNav.noDomain`: label for the shared settings \(default _Shared settings_\)

##### Adding a shared item to every domain

A non-domain-bound entity is by default only available on the shared settings.  You can add a shared entity to every domain by setting the attribute `WebCmsEntityAttributes.ALLOW_PER_DOMAIN` to true on the corresponding `EntityConfiguration`.

###### Example sharing WebCmsImage asset across all domains and making them selectable from all domains

```java
@Bean
public WebCmsMultiDomainConfiguration multiDomainConfiguration() {
    return WebCmsMultiDomainConfiguration.managementPerEntity()
                                         .domainIgnoredTypes( WebCmsImage.class )
                                         .build();
}

/**
 * Ensure that WebCmsImage will be shown on every domain,
 * even though it is not domain-bound.
 */
@Configuration
class AdminUiConfiguration implements EntityConfigurer
{
    @Override
    public void configure( EntitiesConfigurationBuilder entities ) {
        entities.withType( WebCmsImage.class )
            .attribute( WebCmsEntityAttributes.ALLOW_PER_DOMAIN, true );
    }
}
```

### Domain metadata {#domain-metadata}

`WebCmsDomain` is a simple entity with support for an infinite number of String based attributes.  Because this is usually not very efficient to work with, you can implement a custom metadata class that wraps around a `WebCmsDomain` providing strong-typed access to domain-related configuration properties.

The default metadata implementation is `WebCmsSiteConfiguration`.

Metadata will get created as beans and can wire additional beans or services.  For example: the default `WebCmsSiteConfiguration` metadata uses the `WebCmsDataConversionService` to provide methods for strong-typed fetching of attributes.

##### WebCmsDomainAware

A metadata implementation does not require a link to the actual `WebCmsDomain` it is for, but implementing `WebCmsDomainAware` will ensure that the actual domain will be set when the metadata is being created.

##### WebCmsDomainUrlConfigurer

The domain metadata can also implement the `WebCmsDomainUrlConfigurer` interface.  This interface defines a URL prefix that should be applied to any link to an asset on that domain.  When you use the `WebCmsUriComponentsService` to generate links to asset, these will automatically apply the correct domain settings.

## Using domains

This section explains how you can use the domain concept directly in your controllers or business logic.

### Accessing the current domain

You can access the current domain or its metadata using one of the methods on the `WebCmsMultiDomainService`.  From a static context, you can directly use the `WebCmsDomainContextHolder` and `WebCmsDomainContext`.

Use the service wherever possible, as it will ensure correct behaviour in both a multi-domain and no-domain context.

### Mapping handler methods to domain

Most web cms mapping annotations allow you to specify the domain for which they apply.  If you want to create regular `@RequestMapping` handler methods for specific domains, you can add the `@WebCmsDomainMapping` annotation.

###### Example declaring a domain-specific handler method

```java
/**
 * Declare a handler method that should only execute on the WebCmsDomain with domain key 'my-domain'.
 */
@GetMapping("/my-handler")
@WebCmsDomainMapping("my-domain")
public String domainSpecificMethod() {
  ...
}
```

### Entity Query Language extensions

WebCmsModule adds some domain-related functions that can be used in EQL statements:

* `selectedDomain()`returns the current domain of the context
* `visibleDomains()` returns the domain from which entities can be selected, usually this is the current domain together with non-domain bound entities
* `accessibleDomains(ACTION, ACTION, ...)` returns the list of domains for which the user has any of the listed actions
  * action should be String representing a valid `AllowableAction`

The multi-domain auto-configuration will adjust views and selections only be specifying default EQL predicates to apply, containing these functions.

### Resolving the current domain

The current domain is resolved in 2 ways:

1. The `AbstractWebCmsDomainContextFilter` is used on every request to determine the initial domain.  The default implementation is the WebCmsSiteConfigurationFilter that will use the hostname of the request to lookup the matching WebCmsSiteConfiguration metadata and select the domain accordingly.
2. The `CookieWebCmsDomainContextResolver` is used by AdminWebModule to determine the actual domain configured in a cookie.  This will overrule any previously configured domain by the filter.

You can alter the resolving mechanism by creating your own `AbstractWebCmsDomainContextFilter` implementation and registering it on the `WebCmsMultiDomainConfiguration`.

## Using no-domain in a multi-domain setup

In a multi-domain setup items not attached to a specific domain are expected to be shared across all domains.  In the default configuration, type specifiers are not domain-bound but shared across all domains.  In the administration UI of per-domain management, you will only be able to modify types under Shared settings.

It's possible to define an item as domain-bound however, but at the same time making the domain optional.  This means the entity can be either shared across all domains, or attached to a specific domain.

The following default behaviour will be applied:

* if an entity is not domain-bound, it will always be looked for in the set of "no-domain" entities, regardless of any domain attached to the current context
* if an entity is domain-bound, it will be looked for in the entities attached to the domain of the current context \(this could be "no-domain"\)
* if an entity is domain-bound but the domain is optional, it will be looked for in the set of "no-domain" entities if no entity could be found in the set of entities attached to the domain of the current context
  * This allows you to use entities shared across all domains, but overrule them with domain-specific versions.  This can be especially useful in the case of type specifiers.

The available service beans all inspect the multi domain configuration of your application to determine which logic they should apply. This will usually be very transparent for the user \(developer\).

## Manually adding multi-domain support to your entities

The auto-configuration of multi-domain support attempts to setup your administration UI as good as possible.  Default auto-configuration changes for domain-bound entities are as follows:

* customize the `EntityFactory` on the `EntityConfiguration`
* default selection options for that type are set to match only the current domain
* list views are modified to only show the items attached to the current domain

If auto-configuration is insufficient, you will need to manually add multi-domain support to your entities.  The following beans are available to help you:

* the `WebCmsMultiDomainConfiguration` allows you to inquire the current configuration
* the `WebCmsMultiDomainService` provides access to all domains and there metadata, it also exposes the most frequently used `WebCmsMultiDomainConfiguration` data
* the `WebCmsMultiDomainAdminUiService` is available only if EntityModule is active and provides you with methods useful for filtering your administration UI

##### Repositories & services

* Repositories are usually used in the backend - especially if you want to support both multi-domain and no-domain configurations.  Repository methods always require you to explicitly specify the domain as well.
* Services usually use the current domain to interact with the repository, making them very easy to use in frontend business logic.

See [the appendix](/docs/repositories-and-services.md) for an overview of the available repositories and services.

## Importing domain configuration

`WebCmsDomain` is a `WebCmsObject` like most other entities provided by WebCmsModule.  It has full support for being imported using YAML \(or another format - see the section [Importing Data](/docs/chap-placeholder.adoc) for more information\).

###### Example importing a WebCmsDomain with WebCmsSiteConfiguration metadata

```yaml
domains:
  foreach.be:
    name: foreach.be
    description: Main Foreach website.
    active: true
    attributes:
      customString: some text
      customNumber: 123
      hostNames:
        - foreach.be
        - *.foreach.be
      sortIndex: 1
      cookieDomain: foreach.be
      defaultLocale: en_UK
      urlPrefix: "https://foreach.be/"
      alwaysPrefix: true
```

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

> **NOTE**
>
> In a multi-domain supported setup, all newly created `WebCmsDomainBound` objects will be imported under their currently scoped domain \(by default: no domain\). The domain of a specific entry can still be changed by using the _domain_ property.
>
> We strongly advise to scope entire imports for a specific domain in a multi-domain setup. If you do want to explicitly set the domain afterwards, we advise you to explicitly set it everywhere.



