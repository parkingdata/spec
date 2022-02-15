# API Specification for APDS

> **IMPORTANT**
> This specification is a working **DRAFT**. 
> It is in the process of being updated to reflect the latest version of the APDS Data Model and the emerging ISO 5206-1.  

## About
### APDS: Context, Motivation
A few years back, the [International Parking &amp; Mobility Institue (**IPMI**)](https://ipmi.parking-mobility.org), the [British Parking Association (**BPA**)](https://www.britishparking.co.uk), and the [European Parking Association (**EPA**)](https://www.europeanparking.eu) formed a not-for-profit organization with the mission to develop, promote, manage, and maintain a uniform global standard that will allow organizations to share parking data across platforms worldwide: **APDS**, the [Alliance for Parking Data Standards](https://www.allianceforparkingdatastandards.org). 

### About this Repository
#### APDS Specification
_APDS_ has developed the global parking data standard in form of a comprehensive (UML) model. This model is the source for the _APDS Specifications_ (overview, information model, data model, use cases). You can request the specification documents on the [APDS website](https://www.allianceforparkingdatastandards.org) free of charge.

#### APDS API
The _APDS_ working group has also created a comprehensive representation of the APDS data model in form of an **API**. This interface is documented using the [OpenAPI Specification Standard](https://www.openapis.org) originally initiated by [Swagger](https://swagger.io) and then taken over by the **Open APIs** initiative. Depending on the particular application, there might be other ways to describe the model and a derived interface. But _APDS_ figured it would be a good starting point for adopters to have something readily available to base their work on. This repository contains said **APS OpenAPI Specification**.

#### Specification Structure
The actual specification has been documented in a modular way. The main document can be found [here](api/reference/APDS_API.yaml). It provides header information, and a directory of interface endpoints and expected payloads/responses. The actual model behind it is specified in form of individual _OpenAPI_ sub-specs, one per data type. It can be found [here](api/models/).

__Note:__ depending on the OpenAPI tool chain of your choice, working off the modular specification version may show some processing issues. We hence have also created a **bundled version** that compiles all components into one single file. It is a full equivalent of the modular specification version. You can find it [here](api/reference/APDS_API_bundled.json). This bundled representation has been created using [_swagger-cli_](https://github.com/APIDevTools/swagger-cli).

## Questions, Suggestions
_APDS_ is commited to support all adopters. In case you have questions or even suggestions for changes, head over to the **APDS Forum** where you can join the discussion with parking, transportation and mobility professionals: [Forum](https://www.allianceforparkingdatastandards.org/forum).

## Tell us about your Project
Obviously, _APDS_ is more than keen to learn how the standard is being applied by its users. So, please drop us a comment with some initial information about the particular APDS-related project you are working on.

## License
The _APDS API_ specification is provided under the [GNU General Public License Version 3](https://www.gnu.org/licenses/quick-guide-gplv3.html). You can find the plain text version of this license in this repository: [./LICENSE](./LICENSE).

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


<br/>

<div style="color:lightgray">
&copy; 2022 Alliance for Parking Data Standards
</div>

