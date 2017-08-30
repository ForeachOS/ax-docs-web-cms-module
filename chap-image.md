# Image model {#image-model}

jsdqkmlf jdslkjfsd lkmjqsd k

## Connecting to an image repository

### Cloudinary connector

##### pom.xml

```xml
<dependency>
    <groupId>com.cloudinary</groupId>
    <artifactId>cloudinary-http44</artifactId>
    <version>1.14.0</version>
</dependency>
```

##### application.yml

```yaml
webCmsModule:
  images:
    cloudinary:
      cloudName: yourCloudName
      apiKey: 123456789
      apiSecret: secret
```

### ImageServer connector

##### application.yml

```yaml
webCmsModule:
  images:
    imageServer:
      url: "http://remote.imageserver.domain/resources/images"
      hashToken: optionalHashToken
      accessToken: secureApiAccessToken
```

If not using a **hashToken**, retrieving the resolution **0x0** should be allowed in order to retrieve the original image.

### Using a custom connector

You can easily WebCmsImageConnector

## Rendering images

refer to the [thymeleaf dialect](/thymeleaf-dialect.adoc) \( \)

## Importing images

You can easily import images by providing a resource location to the actual image file.

Any property of `WebCmsImage` can either refer the **objectId** \(starting with _wcm:asset:image_\) or be a resource location representing the image file.  If the latter, an object id will be generated and if no `WebCmsImage` with that object id can be found, the resource file will be uploaded.

Only if the resource location changes between import runs would a new image be uploaded, otherwise it is perfectly safe to re-run the import.

> The ability to import images depends on the presence of the `WebCmsImageConnector` bean.  Because it is possible that a `WebCmsImageConnector` is only initialized late during the application bootstrap, data installers are best set to execute in the `AfterContextBootstrap` phase.

##### Example image import yaml

```yaml
assets:
  component:
    fixed-section:
      title: Fixed section
      componentType: section
      wcm:components:
        image:
          image: "file:./src/main/test-data/test-image.jpg"
    sample-image:
      title: Sample image of a deer
      componentType: image
      image: "classpath:installers/test-data/deer.jpg"
    sample-image-from-url:
      title: Sample image fetched from URL
      componentType: image
      image: "http://images.freeimages.com/images/large-previews/afa/black-jaguar-1402097.jpg"
```

> All Spring resource locations are supported.  Take into account that URL resources using HTTPS will only work if the executing Java runtime has the required SSL certificates to connect to the remote host.



