# What's new in this version?

## 0.0.3-SNAPSHOT

* [multi-domain/multi-site support](/multi-domain-support.md) has been added
* it is now possible to filter the component types that can be created on a global level or added as members of a container
  * `componentRestricted` attribute on `WebCmsComponentType` to indicate a component cannot be added implicitly
  * `childComponentsRestricted` attribute on `WebCmsTypeSpecifier` to indicate only linked component types should be available for adding
  * see chapters in [Defining component types](/components/chap-web-components-defining-component-types.adoc) for more information
* component administration UI improvements
  * message codes can be used to [configure descriptions and labels for component types](/appendices/message-codes.md)
  * text components \(rich-text/markup\) can have a [custom profile configured](/components/chap-web-components-defining-component-types.adoc#component-profiles) - default implementations support TinyMCE and CodeMirror profiles
* `WebCmsAsset`s now have a sortIndex which by default is set to 1000.
* It is now possible to import \`[WebCmsUrls](/urls/chap-endpoint-url.adoc)\` and \`[Redirects](/chap-redirects.adoc)\`

## 0.0.2-SNAPSHOT

Initial public release available on [Maven central](http://search.maven.org).

