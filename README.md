SAP Commerce Cloud Extension for code generation
=====================

**SAP Commerce Cloud** (aka. _Hybris_) project can be integrated with multiple external systems.

This Extension aims to simplify and unify process of DTO classes generation for any SAP Commerce Cloud project.<br>
It excludes libs used for code generation from the final SAP Commerce Cloud project distribution and hides all complex
generation logic behind the curtains.

**Supported generators**

| Ant Macro          | Generator                                                                                   |
|--------------------|---------------------------------------------------------------------------------------------|
| `ycodegen_json`    | [JSON](https://joelittlejohn.github.io/jsonschema2pojo/site/1.2.1/Jsonschema2PojoTask.html) |  
| `ycodegen_openapi` | [Open API](https://openapi-generator.tech)                                                  |                
| `ycodegen_xjc`     | [XML binding](https://eclipse-ee4j.github.io/jaxb-ri/)                                      |                
| `ycodegen_wsdl`    | [WSDL](https://jakarta.ee/specifications/xml-binding/)                                      |                
| `ycodegen_wadl`    | [WADL](https://mvnrepository.com/artifact/org.jvnet.ws.wadl)                                |                

Once this Extension is registered in the project, it will seamlessly integrate into the SAP Commerce Cloud build system
and generate DTO classes for each registered schema.

To ensure that build performance is not affected and DTO classes regenerated only first time or on modification this
Extension creates file (`codegen-<ext_name>.properties`) for tracking all generated schemas for each target extension (
where schema was registered for generation).<br>
That file will contain list of all schemas with corresponding Git hash to ensure re-generation of the DTOs only if
source file has been changed, in other words - if you pull the code and source file is changed code generation will be
re-triggered.

Invocation of the `ant clean` will lead to code generation during the build as well as removal of all generators
libraries under `codegen/lib/<generator>` directory.

**Important Note**: this _Extension_ will not add any 3rd-party `.jar` files to your final project distribution, it
means that if there is a need in specific imports (like `javax.xml.ws.*`, `org.jvnet.*`, etc.) those dependencies have
to be specified explicitly via `external-dependencies.xml` file.

---

Representation of the file to be generated during the build
![Generate](docs/generate.png?raw=true)

Representation of already generated file, it will be skipped during the build
![Skip](docs/skip.png?raw=true)

## How to register schema for DTO generation

- add _Extension_ specific `before_build` macrodef for your _Extension_'s `buildcallbacks.xml` file,
  like `trainingcore_before_build`
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

        <ycodegen_wsdl extension="trainingcore"
                       schema="${ext.trainingcore.path}/resources/schema/some-service.wsdl"
                       package="com.training.core.dto.<package name>"/>
    </sequential>
</macrodef>
```

### Generators

Attributes to be used with every `ycodegen_xxx` generator macro

| Attribute    | Default value     | Description                                                |
|--------------|-------------------|------------------------------------------------------------|
| extension    | -                 | Specify extension name where DTO classes will be generated |
| schema       | -                 | Specify location of the generator schema file              |
| package      | -                 | Specify target package for DTO classes                     |
| targetFolder | `gensrc`          | Target folder for generated DTO classes                    |
| version      | _see table below_ | Exact version of the generator library                     |

Besides that, each Code Generator supports additional attributes provided by generator's Ant Task. Explanation for each
such attribute can be found in official documentation for specific Ant Task.

Supported versions for each Code Generator

| Generator | Default version | Other supported versions                                                     |
|-----------|-----------------|------------------------------------------------------------------------------|
| JSON      | 1               | -                                                                            |
| Open API  | 6               | -                                                                            |
| XJC       | 2               | 3, 4 ([Jakarta](https://eclipse-ee4j.github.io/jaxb-ri/, without extensions) |
| WSDL      | 2               | 3, 4                                                                         |
| WADL      | 1               | -                                                                            |

#### XJC Generator

Second version of the generator is shipped with multiple [JAXB2-basics](https://github.com/highsource/jaxb2-basics)
extensions, which can be enabled via `arguments` attribute of the `ycodegen_xjc` macro.
