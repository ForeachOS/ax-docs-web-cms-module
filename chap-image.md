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

WebCmsImageConnector

## Rendering images

refer to the [thymeleaf dialect](/thymeleaf-dialect.adoc) \( \)

## Importing images

Any property of WebCmsImage can either refer the object id \(starting with wcm:asset:image\) or be a resource location representing the image.  If the latter, an object id will be generated and if no WebCmsImage with that object id can be found, the resource file will be uploaded.

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





