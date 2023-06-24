# SAP Commerce Cloud Extension for code generation

## Goal

SAP Commerce Cloud (Hybris) project integration with external system

To simplify and unify code generation in...
Exclude code generation libs from the project, as they are not needed in the final solution.

## How to use

Supported generators:
- XSD
- Swagger
- JSON

### Sample usage:

```xml
<macrodef name="trainingcore_before_build">
    <sequential>
        <ycodegen_json extension="trainingcore"
                       schema="${ext.trainingcore.path}/resources/schema/some-api-response.json"
                       package="com.training.core.api.some.response"/>
    </sequential>
</macrodef>
```