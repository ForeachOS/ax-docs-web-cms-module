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
