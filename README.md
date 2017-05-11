Paremus System Archetype
========================

This is a Maven archetype for system definitions to run on Paremus Service Fabric. It builds on the project structure used the [Effective OSGi archetypes](https://github.com/njbartlett/eosgi-maven).

Prerequisites
-------------

Before using this archetype: 

1. [Install the Effective OSGi archetypes](https://github.com/njbartlett/eosgi-maven#installation)
2. Work through [instructions](https://github.com/njbartlett/eosgi-maven/blob/master/archetypes/README.md#usage) to use those archetypes to build a basic OSGi bundle and application.

Installation
------------

As this archetype is not yet released into Maven Central, it is necessary to clone from GitHub and install into your local Maven repository before use:

    git clone https://github.com/paremus/maven-archetypes.git paremus-maven-archetypes
    cd paremus-maven-archetypes
    mvn install

Usage
-----

**The following command(s) should be run from the `example-parent` directory that was created by following the [Effective OSGi archetype instructions](https://github.com/njbartlett/eosgi-maven/blob/master/archetypes/README.md#usage)**.

First generate a module for the Fabric System:

    mvn archetype:generate \
    -DarchetypeGroupId=com.paremus.fabric \
    -DarchetypeVersion=0.0.1-SNAPSHOT \
    -DarchetypeArtifactId=paremus-system-archetype

Use the same group ID as was used in previous steps. Specify `example-system` for the artifact ID. Accept the defaults for version and Java package.

Edit the file `example-system/src/main/resources/example-app.bndrun`. The `-runrequires` line lists required bundles for a single Part, i.e. an artifact that runs together on a Fibre. Add a requirement for the plain OSGi bundle that you created in the Effective OSGi archetype steps (assuming this was `org.example.hello`):

    -runrequires: osgi.identity; filter:='(osgi.identity=org.example.hello)'

Build the project with `mvn package`; note that this is **expected to fail!** The contents of `-runrequires` is resolved against the repository defined in the `_index` module in order to validate that the System Part can be deployed. The output of the build will list the expected bundles including transitive dependencies, which should be copied to the `-runbundles` section of `example-app.bndrun`. I.e. it should now look like this:

    -runbundles: \
        org.apache.felix.scr;version='[2.0.10,2.0.11)',\
        org.example.hello;version='[1.0.0,1.0.1)'

Rebuild with `mvn package`. Now you will find a completed system document as `example-system/target/system.xml`.

**N.B.:** The above validation step is **optional**, however it provides a useful check that the System Part is resolvable from the repository index before attempting to deploy it. To disable the check, edit `example-system/pom.xml` and find the configuration section for the `bnd-resolver-maven-plugin`. Change `<failOnChanges>true</failOnChanges>` to `<failOnChanges>false</failOnChanges>`.

## Deploying to a Local Fabric

The system.xml as built contains a reference to an index file that is on the local filesystem. That is, the `repopath` attribute will contain a URL of the form `file:/Users/me/example-parent/_index/target/index.xml`. This system document can therefore only be deployed to a Fabric that is running entirely on the local machine, because the repository URL is only valid there.

To deploy this system, enter a URL to `system.xml` into the Systems tabs of Paremus Entire. For example:

    file:/Users/me/example-parent/example-system/target/system.xml

## Deploying to a Remote Fabric

TODO