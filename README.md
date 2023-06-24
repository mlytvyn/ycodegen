SAP Commerce Cloud Extension for code generation
=====================

**SAP Commerce Cloud** (aka. _Hybris_) project can be integrated with multiple external systems.

This Extension aims to simplify and unify process of DTO classes generation for any SAP Commerce Cloud project.<br>
It excludes libs used for code generation from the final SAP Commerce Cloud project distribution and hides all complex
generation logic behind the curtains.

**Supported generators**

- [JSON](https://github.com/joelittlejohn/jsonschema2pojo/wiki/Getting-Started#the-ant-task) (`.json`)
- [Open API](https://openapi-generator.tech) (`.yml`)
- [XML binding](https://eclipse-ee4j.github.io/jaxb-ri/) (`.xsd`)
- [WADL](https://mvnrepository.com/artifact/org.jvnet.ws.wadl) (`.wadl`)

Code generation extension creates extension specific properties file `codegen-<ext_name>.properties`.<br>
It will contain list of all generated files with corresponding Git hash to ensure code re-generation if source file has
been
changed, in other words - if you pull the code and source file is changed code generation will be re-triggered.

Code generation will also be re-triggered on `ant clean`.

---

Representation of the file to be generated during the build
![Ribbons](docs/generate.png?raw=true)

Representation of already generated file, it will be skipped during the build
![Ribbons](docs/skip.png?raw=true)

## How to use

- add _Extension_ specific `before_build` macrodef for your _Extension_'s `buildcallbacks.xml` file.
- define generator specific macro usage, specify mandatory / optional attributes.
- to generate DTO classes it is enough to execute build from the _Platform_ or specific _Extension_

```shell
ant build
```

- to re-generate DTO classes it is enough to execute clean and build from the _Platform_ or specific _Extension_

```shell
ant clean build
```

Sample configuration

```xml

<macrodef name="trainingcore_before_build">
    <sequential>
        <ycodegen_json extension="trainingcore"
                       schema="${ext.trainingcore.path}/resources/schema/some-api-response.json"
                       package="com.training.core.dto.<package name>"/>

        <ycodegen_xjc extension="trainingcore"
                      schema="${ext.trainingcore.path}/resources/schema/some-service.xsd"
                      package="com.training.core.dto.<package name>"/>

        <ycodegen_openapi extension="trainingcore"
                          schema="${ext.trainingcore.path}/resources/schema/openapi.yml"
                          package="com.training.core.dto.<package name>"
                          reservedWordsMapping="3dsecure=threeDSecure"/>
    </sequential>
</macrodef>
```

### Generators

Custom mandatory attributes to be used with every `ycodegen_xxx` generator

| Attribute    | Default value | Description                                                |
|--------------|---------------|------------------------------------------------------------|
| extension    | -             | Specify extension name where DTO classes will be generated |
| schema       | -             | Specify location of the generator schema file              |
| package      | -             | Specify target package for DTO classes                     |
| targetFolder | `gensrc`      | Target folder for generated DTO classes                    |
