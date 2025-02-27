# The digilib IIIF API

## IIIF Image API

The Scaler servlet provides not only its native [Scaler API](scaler-api.html) but also an API 
compliant to the standards of the International Image Interoperability Framework http://iiif.io.

As of version 2.3.7 digilib supports the [IIIF Image API version 2](http://iiif.io/api/image/2.1/) 
at [compliance level 2](http://iiif.io/api/image/2.0/compliance.html). You can switch between API 
version 1.1 and 2.0 support with the `iiif-api-version` parameter in [digilib-config](digilib-config.html),  

IIIF Image API URLs for an image request have the form:

    http[s]://{server}/{digilib-webapp}/Scaler/{iiif-prefix}/{identifier}/{region}/{size}/{rotation}/{quality}.{format} 

where `digilib-webapp` is the name of the digilib web application in the servlet container. 

The value of `iiif-prefix` is defined by the `iiif-prefix` parameter in [digilib-config](digilib-config.html). 
The default value is "IIIF".

The `identifier` part of the URL must not contain slashes. Since the identifier is mapped to the digilib 
fn-parameter, which is a filesystem path that likely contains slashes separating subdirectories, all 
occurrences of a slash have to be replaced by the value of the `iiif-slash-replacement` parameter in 
[digilib-config](digilib-config.html). The default value of the replacement string is "!", so the fn-path
 "books/book1/page0002" becomes the identifier "books!book1!page0002".

For a definition of the other parameters `region`, `size`, `rotation`, `quality`, and `format` please 
see the [IIIF Image API docs](http://iiif.io/api/image/2.0/).

A IIIF Image API image request URL could look like:

    http://www.example.org/digilib/Scaler/IIIF/books!book1!page0002/full/!150,75/0/default.jpg

An info request URL for the same image looks like: 

    http://www.example.org/digilib/Scaler/IIIF/books!book1!page0002/info.json

## IIIF Presentation API

As of version 2.5.3 digilib provides the optional Manifester servlet that generates simple 
[IIIF Presentation API version 2](http://iiif.io/api/presentation/2.1) manifests that can 
be used with any [IIIF viewer](http://iiif.io/apps-demos/#image-viewing-clients) to navigate 
a directory full of images with the functions of a book-reader or light-table.

To build digilib with the Manifester servlet you need to add the `iiif-presentation` profile to your Maven build:

    mvn -Piiif-presentation package

(or use the digilib-webapp-X.X.X-srv3p.war file from the website)

The Manifester servlet URLs have the form:

    http[s]://{server}/{digilib-webapp}/Manifester/{iiif-prefix}/{identifier}

So you can get a manifest for all images in the directory `/books/book1` with a URL like:

    http://www.example.org/digilib/Manifester/IIIF/books!book1

To try out a viewer on your manifest you can go to the website of the Universal Viewer 
[http://universalviewer.io/](http://universalviewer.io/) and enter the URL of your manifest 
in the "view a manifest" box on the page. This will work even with a local digilib 
installation since the Javascript in your Browser reads and interprets the manifest.

The minimal information in the manifest can be enhanced with additional metadata or the replaced 
by a custom manifest. If the servlet finds a file with the name

    manifest.json

in a directory then the contents of that file are sent instead of an auto-generated manifest.
This works also in directories with no images so you could put a file with 
[collection](http://iiif.io/api/presentation/2.1/#collection) information in a higher-level directory.

If the servlet finds a file with the name

    manifest-meta.json
    
in a directory with images then the contents of that file are added to the top-level manifest
(`@context`, `@type`, `@id`, `sequences` are ignored). You can use this to add real bibliographical
information to the manifest.

The configuration parameter `iiif-manifest-page-label` determines the format of the label of each image:
`filename` uses the image file name (default, sans extension), `index` uses the index (counting from 1).


