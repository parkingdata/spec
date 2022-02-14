# Draft UML data model and API specifications for APDS

**IMPORTANT**: This specification is a working **DRAFT**. It is in the process of being updated to reflect the latest version of the APDS Data Model and the emerging ISO 5206-1.  


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
<div>
<pre>
<code>
<span style="color:lightgray">$ </span>openapi-generator validate -i api/reference/APDS_API_bundled.json 

<span style="color:green">No validation issues detected.</span>

</code>
</pre>
</div>

More information on openapi-generator can be found here: [https://openapi-generator.tech](https://openapi-generator.tech)


