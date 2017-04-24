# The API Console application
An API Console as a standalone application

An API console for RAML (Restful Api Modeling Language) documents. The RAML Console allows browsing of API documentation and in-browser testing of API methods.

---

Unlike the `api-console` element, this application can accept the URL query
parameter (`api`) which points to the RAML file hosted on a remote machine.
The application will download the RAML file and it's dependencies and then it will run RAML to JavaScript parser and raml JavaScript enhancer.

When this task are performed the documentation will be displayed as in the
`api-console` element.


## Using the console
The API Console can be used either as a HTML element (Web Components) or as a standalone application.

**To use the API console as a HTML element** refer to the [api-console element](https://github.com/mulesoft/api-console/tree/release/4.0.0) repository for detailed usage.

Otherwise use following directions.

### Prerequisites
To run the console, you'll need the following:
- [Node JS](http://nodejs.org/)

1. Clone the api-console-app

```
git clone https://github.com/advanced-rest-client/api-console-app.git
```
```
cd api-console-app
```
2. Install dependencies
(install node.js if not already installed)
```
npm install && bower install
```
3. Install `polymer` tools
```
npm install -g polymer-cli
```
4. Run the console
```
polymer serve --open -p 8080
```

When the website opens, it will load a default RAML file that is hard coded in the `index.html` as an attribute for the `api-console-app` elerment:

```html
<api-console-app src="https://cdn.rawgit.com/advanced-rest-client/drive-raml-api-v2/ab60ed28/api.raml"></api-console-app>
```

You can change it to different file or you can add the `api` query parameter that points to a RAML file.
```
http://localhost:8080/?api=https://cdn.rawgit.com/advanced-rest-client/drive-raml-api-v2/ab60ed28/api.raml
```

## Building the application

To build a working API Console application use following command:

```
polymer build
```

This will generate two versions of the API Console application. Both in the `build/` directory.

- The **bundled** version - All fragments are bundled together to reduce the number of file requests. Optimal for sending to clients or serving from servers that are not HTTP/2 compatible.
- The **unbundled** vaersion - Fragments are unbundled. Optimal for HTTP/2-compatible servers and clients.

The API console contains two fragments. One - main - is the critical resources:
the parser, RAML JavaScript enhancer and the documentation viewer.
Second fragment is the HTTP request and response panels that will be downloaded
when the user request them for the first time.


## Deep linking
The application supports deep linking. It means that you can link to the documentation to specific resource / method / type documentation.

To build an URL to a specific fragment of the documentation you have to add
a JSON path to the resource you want to link to.
JSON path is a string representing a path to the object.
For example if you want to link to the first method in the first resource in the RAML structure the path will look like the following:
```
resources.0.methods.0
```
Because some servers don't like dots ('.') in the path the app uses dashes instead of dots:
```
resources-0-methods-0
```
Now, to apply it to the URL you have to add it to the URL path segment, after the `/docs` path prefix. The final URL will be:
```
http://localhost:8080/docs/resources-0-methods-0
```
This URL will always point to the first method in the first resource in the RAML structure.

#### Supported paths

To better understand paths consider following RAML example
```yaml
#%RAML 1.0
title: Google Drive

types:
  type1: ...
  type2: ...
documentation:
  - title: Doc
    content: Documentation
/files:
  post: ...
  get: ...
  /{fileId}:
    path: ...
    get: ...
/apps:
  post: ...
  get: ...
```

##### /docs/summary
It is a default URL after loading the RAML and if the path is not set.
It displays summary information about the RAML docuemnt.

##### /docs/resources

Links to the resource summary documentation. You have to add a resource index in the RAML structure according to regexp:
```
(resource-(\d+)-?)+
```
Examples:
- `resources-1` - second resource in the RAML structure, the `/apps` in example
- `resources-0-resources-0` - First sub-resource of the first resource, the `/files/{fileId}` in the example

##### /docs/resources-methods
Links to the method documentation.
```
(resource-(\d+)-?)-(method(\d+)-?)
```
Examples:
- `resources-0-resources-0-methods-0` - First method in the first sub-resource of the first resource, the `PATCH /files/{fileId}` in the example
- `resources-1-methods-1` - Second method of the second resource, the `GET /apps` in the example


##### /docs/types
Links to the type documentation.
```
type-(\d+)
```
Examples:
- `types-0` - First type in RAML structure, the `type1` in the example
- `types-1` - Second type in RAML structure, the `type2` in the example


##### /docs/documentation
Links to the documentation object in the RAML structure
```
documentation-(\d+)
```
Examples:
- `documentation-0` - First element in the documentation list, the `Doc` documentation in the example.

### Configuration

### Via query parameters

#### The `api` parameter
The URL to the RAML file.

Other parameters might be available in the future.
