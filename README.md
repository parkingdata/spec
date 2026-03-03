# API Specification for APDS Version 4.1

> **IMPORTANT**
> This site provides you with the official API specification for _APDS version 4.1_ in the machine-readable Open API format.  
> The associated specification documents can be requested from the _APDS_ via the [Alliance for Parking Data Standards Website](https://www.allianceforparkingdatastandards.org).
> Please note that - other than in previous versions of the standard, the API specification now forms an integral part of the standard itself.

## About APDS
### APDS: Context, Motivation
In 2018, the [International Parking &amp; Mobility Institute (**IPMI**)](https://ipmi.parking-mobility.org), the [British Parking Association (**BPA**)](https://www.britishparking.co.uk), and the [European Parking Association (**EPA**)](https://www.europeanparking.eu) formed a not-for-profit organization with the mission to develop, promote, manage, and maintain a uniform global standard that will allow organizations to share parking data across platforms worldwide: **APDS**, the [Alliance for Parking Data Standards](https://www.allianceforparkingdatastandards.org). 

## About this Repository
### APDS Specification
_APDS_ has developed the global parking data standard in form of a comprehensive (UML) model. This model is the source for the _APDS Specifications_ (overview, information model, data model, use cases). You can request the specification documents on the [APDS website](https://www.allianceforparkingdatastandards.org) free of charge.

### APDS API
The _APDS_ working group has also created a comprehensive representation of the APDS data model in form of an **API**. This interface is documented using the [OpenAPI Specification Standard](https://www.openapis.org) originally initiated by [Swagger](https://swagger.io) and then taken over by the **Open APIs** initiative. With _APDS version 4.1_, the API is part of the standard and no longer just a recommendation.


##### General Notes
The API is based on the HTTP transport protocol and follows the principles of REST (**Re**presentational **S**tate **T**ransfer). The standard HTTP verbs and error codes are used where applicable, and data is organized around resources that are identified and addressed by standard URLs. 

The API uses path-based versioning which allows for different functionality in the future, under different endpoints (the convention is that the first component/prefix in the URL path communicates the API version, e.g. 'v4'). 

* Clients that connect to a certain endpoint can expect to not come across breaking changes, only potential additions (tolerant reader pattern).
* Major (potentially breaking) changes will be published under differently versioned endpoints.
 
Theoretically, resources might be offered in different representations. However, **JSON** currently is the only supported format. 

Client software shall be prepared to receive JSON responses with different levels of verbosity (depending on the available data). Typically, attributes with no assigned value will be omitted. The client shall however also be capable of handling null values. In addition, unknown (new) attributes shall simply be ignored.

All requests are to be sent via HTTP**S**, regardless of the environment. A conformant platform implementation will not respond to insecure API calls using plain HTTP. 

All requests are designed to be idempotent. That is, clients may repeat them without causing side effects.

* The objective of this approach is to simplify the synchronisation strategy between clients, in case of disconnections or some other spurious errors during processing.
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
* update (PATCH/PUT on URL that contains the unique id of the requested resource)
* delete (DELETE on a URL that contains the unique id of the requested resource)

Besides this universal pattern, additional parameters might be applicable based on the particular resource type and operation (see details below).

##### Errors
Error conditions are signalized using standard HTTP status codes. 

When an error occurs, the platform will return a response body that includes the error type and a message with potential details.

In general, client software shall expect 2XX codes for successful operations and 4XX codes for common error conditions.

5XX errors will be returned in extraordinary situations (typically server-sides failures). 

##### Authentication and Authorization
Authentication can vary by implementation. The previous version of the APDS API assumed that authentication is performed using a custom HTTP header containing a pre-shared secret (token), issued by the platform operator per client. The name of the custom header is **X-MCC-Access-Token**, the token will typically be a UUID-formatted identifier. Both, a missing authentication header, as well as an authentication header bearing an unknown access token, will lead to HTTP status **401 (Unauthorized)** to be returned. The new version recommends OAUTH2-based authentication.

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
The APDS API team recommends implementers to consider methods of protecting their platform like e.g. throttling. Should a client (intentionally or accidentally) exceed the maximum number of requests per minute, it will receive a HTTP status of **429 (Too Many Requests)**.


<hr/>

## Questions, Suggestions
_APDS_ is committed to support all adopters. In case you have questions or even suggestions for changes, feel free to create a corresponding issue in this repository.
Alternatively, you can send an email to the [Technical Comments Email Address](mailto:technical-comments@allianceforparkingdatastandards.org).

## Tell us about your Project
Obviously, _APDS_ is more than keen to learn how the standard is being applied by its users. So, please drop us a comment with some initial information about the particular APDS-related project you are working on.

## License
The _APDS API_ specification is provided to you under the [MIT License](./LICENSE). You can find the plain text version of this license in this repository: [./LICENSE](./LICENSE).

> **IMPORTANT**
> The _APDS API_ specification was originally provided under the [GPLv3](https://www.gnu.org/licenses/quick-guide-gplv3.html) license. _APDS_ then decided to **retroactively** switch to the more permissive MIT license, effective 15th of June, 2022.
<hr/>


<div style="color:lightgray">
&copy; 2026 Alliance for Parking Data Standards
</div>



