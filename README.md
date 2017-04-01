# Pyang plugin for Swagger

Most of the code has been taken from the [Pyang-COP](https://github.com/ict-strauss/COP/tree/master/pyang_plugins) repository and modified to fit our requirements)

[Pyang](https://code.google.com/p/pyang/) is an extensible YANG validator and converter written in python.

It can be used to validate YANG modules for correctness, to transform YANG modules into other formats, and to generate code from the modules. We have written a pyang plugin to obtain the RESTCONF API from a yang model.

The RESTCONF API of the YANG model is interpreted with [Swagger](http://swagger.io/), which is a powerful framework for API description. This framework will be used to generate a Stub server for the YANG module.


##Install pyang

Download pyang [here](https://code.google.com/p/pyang/wiki/Downloads?tm=2) (tested with version 1.5).
Extract the archive to a folder of you choice.
Install pyang  by running the following command inside that folder:

```
sudo python setup.py install
```

### Copy the swagger plugin to pyang's plugin directory:

```
sudo cp pyang_plugins/swagger.py /usr/local/lib/python2.7/dist-packages/{pyang_directory}/plugins/
```

## Run pyang swagger plugin

```
pyang -f swagger -p modules modules/config-bridge.yang -o config-bridge-swagger.json

      --use the option '-p' to specify the path of the yang models for import purposes.
```

### Have a look at the JSON output with the Swagger editor

[Swagger editor](http://editor.swagger.io/#/)


## To build a JAX-RS server stub using Swagger-codegen

We will use the swagger code generator. The obtained swagger files from our pyang plugin are in swagger v2.0. To generate code from this swagger file version we use the current stable version of [swagger-codegen (2.1.3)](https://github.com/swagger-api/swagger-codegen/tree/v2.1.3):

### Prerequisites

You need the following installed and available in your $PATH:

[Java 7](http://java.oracle.com/)

[Apache maven 3.0.3 or greater](http://maven.apache.org/)

### Installation and code generation

Download and build swagger-codegen

```
git clone https://github.com/swagger-api/swagger-codegen.git
cd swagger-codegen/
mvn package
```
Copy and paste your already generated swagger.json model/s into the swagger-codegen folder:

```
cp ../source_cop_files_folder/*.json .
```

###To build a server stub using JAX-RS:

```
java -jar modules/swagger-codegen-cli/target/swagger-codegen-cli.jar generate \
-i service-call.json \
-l jaxrs \
-o samples/service-call/
```

Run the generated server:

```
cd samples/service-call/
mvn jetty:run
```

After starting the server you can try some examples using CURL or opening the following link:
```
http://localhost:8080/restconf/config/calls

http://localhost:8080/restconf/config/calls/call/1/aEnd

http://localhost:8080/restconf/config/calls/call/1/connections/conn_3
```

If everything worked, you will see this reply:
```
{"code":4,"type":"ok","message":"magic!"}
```


License
-------

Copyright 2015 CTTC.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at [apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
