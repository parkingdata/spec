# API Specification for APDS

> **IMPORTANT**
> This site shares preferred methods and formats for sharing parking and mobility related data using the APDS data specification.
> We encourage the use of these preferred methods. If your organization introduces new API methods, we request you share them with APDS so we can review and, when appropriate, include them as a preferred method.

## About APDS
### APDS: Context, Motivation
A few years back, the [International Parking &amp; Mobility Institute (**IPMI**)](https://ipmi.parking-mobility.org), the [British Parking Association (**BPA**)](https://www.britishparking.co.uk), and the [European Parking Association (**EPA**)](https://www.europeanparking.eu) formed a not-for-profit organization with the mission to develop, promote, manage, and maintain a uniform global standard that will allow organizations to share parking data across platforms worldwide: **APDS**, the [Alliance for Parking Data Standards](https://www.allianceforparkingdatastandards.org). 

## About this Repository
### APDS Specification
_APDS_ has developed the global parking data standard in form of a comprehensive (UML) model. This model is the source for the _APDS Specifications_ (overview, information model, data model, use cases). You can request the specification documents on the [APDS website](https://www.allianceforparkingdatastandards.org) free of charge.

### APDS API
The _APDS_ working group has also created a comprehensive representation of the APDS data model in form of an **API**. This interface is documented using the [OpenAPI Specification Standard](https://www.openapis.org) originally initiated by [Swagger](https://swagger.io) and then taken over by the **Open APIs** initiative. Depending on the particular application, there might be other ways to describe the model and a derived interface. But _APDS_ figured it would be a good starting point for adopters to have something readily available to base their work on. This repository contains said **APS OpenAPI Specification**.

#### Specification Structure
The actual specification has been documented in a modular way. The main document can be found [here](api/reference/APDS_API.yaml). It provides header information, and a directory of interface endpoints and expected payloads/responses. The actual model behind it is specified in form of individual _OpenAPI_ sub-specs, one per data type. It can be found [here](api/models/).

#### Additional Comments and Recommendations
Based on our experience with one of the first reference implementations, the _APDS_ working group has compiled a set of comments and recommendations for organizations who are planning to implement an APDS-conformant backend system themselves.

##### General Notes
The API is based on the HTTP transport protocol and follows the principles of REST (**Re**presentational **S**tate **T**ransfer). The standard HTTP verbs and error codes are used where applicable, and data is organized around resources that are identified and addressed by standard URLs. 

The API uses path-based versioning which allows for different functionality in the future, under different endpoints (the convention is that the first component/prefix in the URL path communicates the API version, e.g. 'v1'). 

* Clients that connect to a certain endpoint can expect to not come across breaking changes, only potential additions (tolerant reader pattern).
* Major (potentially breaking) changes will be published under differently versioned endpoints.
 
Theoretically, resources might be offered in different representations. However, **JSON** currently is the only supported format. 

Client software shall be prepared to receive JSON responses with different levels of verbosity (depending on the available data). Typically, attributes with no assigned value will be omitted. The client shall however also be capable of handling null values. In addition, unknown (new) attributes shall simply be ignored.

All requests are to be sent via HTTP**S**, regardless of the environment. A conformant platform implementation will not respond to insecure API calls using plain HTTP. 

All requests are designed to be idempotent. That is, clients may repeat them without causing side effects.

* The obective of this approach is to simplify the synchronisation strategy between clients, in case of disconnections or some other spurious errors during processing.
* In summary: if the APDS-conformant platform implementation's response to a given requests is not known for any reason, the request can be repeated.
* Please make sure to read the "Identifiers & Resource Versioning" section and make sure to adhere to it.

This APDS API specification is meant to facilitate the process of creating an APDS-conformant data store, with capabilities to search, retrieve and manage data resources by interested clients.
* The specification does not describe business logic, calculation algorithms, clean-up strategies for orphaned/obsolete data, etc.. This is assumed to be defined at the discretion of the platform implementer in the context of their particular solution.
* When using the APDS API as the template for an implementation, they key gain is interoperability between different systems using the system.

##### API Structure
There are five kinds of operations for each resource type:
* list (GET on the top-level URL for the resource)
* create (POST on the top-level URL for the resource)
* read (GET on a URL that contains the unique id of the requested resource)
* update (PATCH/PUT on ZRK that contains the unique id of the requested resource)
* delete (DELETE on a URL that contains the unique id of the requested resource)

Besides this universal pattern, additional parameters might be applicable based on the particular resource type and operation (see details below).

##### Errors
Error conditions are signalized using standard HTTP status codes. 

When an error occurs, the platform will return a response body that includes the error type and a message with potential details.

In general, client software shall expect 2XX codes for successful operations and 4XX codes for common error conditions.

5XX errors will be returned in extraordinary situations (typically server-sides failures). 

##### Authentication and Authorization
The current version of the APDS API assumes that authentication is performed using a custom HTTP header containing a pre-shared secret (token), issued by the platform operator per client. The name of the custom header is **X-MCC-Access-Token**, the token will typically be a UUID-formatted identifier. Both, a missing authentication header, as well as an authentication header bearing an unknown access token, will lead to HTTP status **401 (Unauthorized)** to be returned.

Future versions of the API specification might offer alternative approaches.

Roles and more fine-grained permissions are assumed to be defined and enforced at the discretion of the platform implementer. If a client application has successfully authenticated a platform implementation but is lacking appropriate privileges for the requested operation and resource, a HTTP status code of **403 (Forbidden)** will be returned. 

##### Identifiers, Resource Versioning
All major resources are assigned a unique identifier and a version number (positive, incrementing integer). 
The current specification presumes that clients creating/updating/deleting resources on an APDS-conformant platform will be responsible to manage said unique ids and version information.

While the APDS implementation is not the one assigning identifiers, it signals duplicate ids in form of a HTTP **409 (Conflict)** response code. The APDS team recommends the use of UUIDs in order to limit the risk of id collisions.

##### Time
All timestamps (i.e. actual instants in time) are represented using the **UTC time zone**.

All _relative_ time and date attributes (e.g. simple dates, times of the day, days of the week, days of the month) or date-times without time zone should be interpreted to be in the Place's time zone. A particula place's time zone is shared as an attribute at the top of the hierarchy (Campus level), using IANA format. The client software is responsible for using the data provided and ensure the right time zone is used or displayed to the user, when applicable.

##### Resource Deletions
The APDS team recommends implementers to only perform _logical delete_ operations. Instead of permanently erasing data, it should rather be moved into an archive database. 
The APDS API also suggests a way of communicating resource deletions (in the "listing" endpoints). This approach facilitates keeping synchronized copies of an APDS-conformant platform database in other places/systems.

##### Pagination
Per the APDS API specification, all responses for "listing resources" are paginated and include meta data about the pagination state:
* offset: index of the first item return on the current page
* page size: maximum number of items per page to be returned
* total: total number of items across all pages
* reference instant: indicates when the result set was initially generated

##### Throttling
The APDS API team recommends implementers to consider methods of protecting their platform like e.g. throttling. Should a client (intentially or accidentally) exceed the maximum number of requests per minute, it will receive a HTTP status of **429 (Too Many Requests)**.


<hr/>

__Note:__ depending on the OpenAPI tool chain of your choice, working off the modular specification version may show some processing issues. We hence have also created a **bundled version** that compiles all components into one single file. It is a full equivalent of the modular specification version. You can find it [here](api/reference/APDS_API_bundled.json). This bundled representation has been created using [_swagger-cli_](https://github.com/APIDevTools/swagger-cli). As some tools struggle with relative paths and circular references, we have also added a fully-dereferenced bundled version which you can find [here](api/reference/APDS_API_dereferenced_bundled.json).

## Questions, Suggestions
_APDS_ is commited to support all adopters. In case you have questions or even suggestions for changes, head over to the **APDS Forum** where you can join the discussion with parking, transportation and mobility professionals: [Forum](https://www.allianceforparkingdatastandards.org/forum).

Alternatively, you can send an email to the [Technical Comments Email Address](mailto:technical-comments@allianceforparkingdatastandards.org).

## Tell us about your Project
Obviously, _APDS_ is more than keen to learn how the standard is being applied by its users. So, please drop us a comment with some initial information about the particular APDS-related project you are working on.

## License
The _APDS API_ specification is provided to you under the [MIT License](./LICENSE). You can find the plain text version of this license in this repository: [./LICENSE](./LICENSE).

> **IMPORTANT**
> The _APDS API_ specification was originally provided under the [GPLv3](https://www.gnu.org/licenses/quick-guide-gplv3.html) license. _APDS_ has decided to **retroactively** switch to the more permissive MIT license, effective 15th of June, 2022.
<hr/>

## Validation
This OpenAPI specification has been validated for confirmity using a set of different tools.

### Swagger CLI
```
$ swagger-cli validate api/reference/APDS_API.yaml

api/reference/APDS_API.yaml is valid.

```
More information on the Swagger CLI can be found here: [https://github.com/APIDevTools/swagger-cli](https://github.com/APIDevTools/swagger-cli)

### IBM lint-openapi
```
$ lint-openapi -e api/reference/APDS_API.yaml

api/reference/APDS_API.yaml passed the validator.

```
More information on IBM's OpenAPI linter can be found here: [https://github.com/IBM/openapi-validator](https://github.com/IBM/openapi-validator)

### OpenAPI Generator
When using openapi-generator, you should use the _bundled_ version of the API specification.
```
$ openapi-generator validate -i api/reference/APDS_API_bundled.json 

No validation issues detected.

```
More information on openapi-generator can be found here: [https://openapi-generator.tech](https://openapi-generator.tech)

### Swagger Editor
The Swagger-provided editor successfully validates the fully-dereferenced bundled version ([api/reference/APDS\_API\_dereferenced\_bundled.json](api/reference/APDS_API_dereferenced_bundled.json)).

More information on Swagger Editor can be found here: [https://editor.swagger.io](https://editor.swagger.io)

<br/>

<div style="color:lightgray">
&copy; 2022 Alliance for Parking Data Standards
</div>

