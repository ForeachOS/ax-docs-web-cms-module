# WebCmsModule reference documentation


This repository has a submodule: [ax-docs-shared](https://github.com/ForeachOS/ax-docs-shared.git).

To download the submodule use the following commands:

```
git submodule init
git submodule update
```

If you happen to be on the wrong submodule branch, use the `--remote` option on the update command. 

To build the html files, execute the following command:
```
[Windows]
gradlew asciidoctor
[Unix]
./gradlew asciidoctor
```

To build the distribution zip file, execute the following command:
```
[Windows]
gradlew docsZip
[Unix]
./gradlew docsZip
```

###### Version

[{{ book.moduleVersion }}](/docs/whats-new.md)

###### Authors

Arne Vandamme, Raf Ceuls, Steven Gentens

## About

WebCmsModule aims to provide some core Web CMS features for your Across application out-of-the-box.  
Building a simple website is very easy and requires very little development.

Features provided by WebCmsModule include:

* an extensible [Domain Model](/docs/chap-domain-model.adoc) with common asset types like [pages](/docs/pages/chap-web-page.adoc), [articles](/docs/publication/chap-publication-model.adoc) and [redirects](/docs/chap-redirects.adoc)
* a way to manage the [URLs for these assets](/docs/urls/chap-endpoint-url.adoc)
* a [powerful component model](/docs/components/chap-web-components.adoc#overview) for building templates and managing dynamic content
* an easy way to [import data using YAML files](/docs/importing/chap-importing-data.adoc#importing-data-yaml)

A fully functional administration UI is available using AdminWebModule and EntityModule.

Module website: {{ book.moduleUrl }}

