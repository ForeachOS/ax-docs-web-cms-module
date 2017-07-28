## General information

### Artifact
```xml
	<dependencies>
		<dependency>
			<groupId>com.foreach.across.modules</groupId>
			<artifactId>{{module-artifact}}</artifactId>
			<version>{{module-version}}</version>
		</dependency>
	</dependencies>
```
{{ book.hibernate-jpa-module-url }}
### Module dependencies

Module |Type |Descriptione
-------|-----|-----------
{{ book.hibernate-jpa-module-url }}|required|Required for the JPA domain model.
{{ book.entity-module-url }}[EntityModule]|optional|In combination with `AdminWebModule` this enables the management forms in the administration UI.
{{ book.admin-web-module-url }}[AdminWebModule]|optional|In combination with `EntityModule` this enables the management forms in the administration UI.

WARNING: WebCmsModule requires the presence of a `UnitOfWorkFactory` for default data importing.
See the {{hibernate-jpa-unit-of-work-factory}}[AcrossHibernateJpaModule reference documentation] for how to set up a `UnitOfWorkFactory`.

### Module settings

All properties start with the #webCmsModule.# prefix.

Property |Type |Description |Default
---------|-----|------------|-------
urls.included-path-patterns|`String[]`|Paths to be included for `WebCmsUrl` mapping.| empty, all non-excluded paths will be considered.Specify as ANT matcher patterns, eg _/path/##_.|`[]`
urls.excluded-path-patterns
|`String[]`
| Paths to always be excluded from `WebCmsUrl` mapping.
If empty, only included paths will be considered. Specify as ANT matcher patterns, eg _/path/##_.|`[]`
default-data.assets.enabled||Enable default assets to be added: this includes component and asset types as well as some sample assets.|`true`
pages.default-page-type|`String`|Type key or object id of the default page type that should assigned to a new page.|_default_
pages.template-prefix|`String`|Prefix to be added to relative template names.
This corresponds usually with the base directory where the (Thymeleaf) templates are located.
If unspecified the default resources of any present `@AcrossApplication` will be used.|
pages.template-prefix-to-ignore|`String[]`|If a page template starts with any of these prefixes, it will be considered absolute and will not be prefixed.|`["th/"]`
pages.template-suffix-to-remove|`String`|If a page template ends with this suffix, the suffix will be removed.|_.html_
default-data.assets.enabled|`Boolean`Enable default assets to be installed: this includes component and asset types as well as some sample assets.|`true`
