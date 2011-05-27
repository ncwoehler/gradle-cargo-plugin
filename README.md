# Gradle Cargo plugin

The plugin provides deployment capabilities for web applications to local and remote containers in any given
Gradle build by leveraging the [Cargo Ant tasks](http://cargo.codehaus.org/Ant+support). It extends the [War plugin](http://www.gradle.org/war_plugin.html).

## Usage

To use the Cargo plugin, include in your build script:

    apply plugin: 'cargo'

The plugin JAR needs to be defined in the classpath of your build script. You can either get the plugin from the GitHub download
section or upload it to your local repository. To define the Cargo dependencies please use the `cargo` configuration name
in your `dependencies` closure. Remote deployment functionality will only work with a Cargo version >= 1.1.0 due to a bug
in the library. Please see [CARGO-962](https://jira.codehaus.org/browse/CARGO-962) for more information.

    buildscript {
        repositories {
            add(new org.apache.ivy.plugins.resolver.URLResolver()) {
                name = 'GitHub'
                addArtifactPattern 'http://cloud.github.com/downloads/bmuschko/gradle-cargo-plugin/[module]-[revision].[ext]'
            }
            mavenCentral()
        }

        dependencies {
            classpath ':gradle-cargo-plugin:0.1'
        }
    }

    dependencies {
        def cargoVersion = '1.1.0'
        cargo "org.codehaus.cargo:cargo-core-uberjar:$cargoVersion",
              "org.codehaus.cargo:cargo-ant:$cargoVersion"
    }

## Tasks

The Cargo plugin defines the following tasks:

* `cargoDeployRemote`: Deploys web application to remote container.
* `cargoUndeployRemote`: Undeploys a web application from remote container.
* `cargoRedeployRemote`: Redeploys web application to remote container.
* `cargoStartLocal`: Starts the local container and deploys web application to it.
* `cargoStopLocal`: Stops local container.

## Project layout

The Cargo plugin uses the same layout as the War plugin.

## Convention properties

The Cargo plugin defines the following convention properties in the `cargo` closure::

* `containerId`: The container ID you are targeting. Please see the [list of supported containers](http://cargo.codehaus.org/Home) on the Cargo website.
* `port`: The TCP port the container responds on (defaults to 8080).
* `context`: The URL context the container is handling your web application on (defaults to WAR name).
* `wait`: Specifies whether the container should run in the background. When false, this task blocks until the container is stopped (defaults to false).

Within `cargo` you can define properties for remote containers in a closure named `remote`:

* `hostname`: The hostname of the remote container.
* `username`: The username credential for the remote container (optional).
* `password`: The password credential for the remote container (optional).

Within `cargo` you can define properties for local containers in a closure named `local`:

* `logLevel`: The log level to run the container with (optional). The valid levels are `low`, `medium` and `high`.
* `homeDir`: The home directory of your local container.

### Example

    cargo {
        containerId = 'tomcat6x'
        port = 9090
        context = 'myawesomewebapp'

        remote {
            hostname = 'cloud.internal.it'
            username = 'superuser'
            password = 'secretpwd'
        }

        local {
            homeDir = file('/home/user/dev/tools/apache-tomcat-6.0.32')
        }
    }

## Project properties

The convention properties can be overridden by project properties via `gradle.properties` or `-P` command line parameter:

* `cargo.container.id`: Overrides the convention property `containerId`.
* `cargo.port`: Overrides the convention property `port`.
* `cargo.context`: Overrides the convention property `context`.
* `cargo.wait`: Overrides the convention property `wait`.
* `cargo.hostname`: Overrides the convention property `hostname`.
* `cargo.username`: Overrides the convention property `username`.
* `cargo.password`: Overrides the convention property `password`.
* `cargo.log.level`: Overrides the convention property `logLevel`.
* `cargo.home.dir`: Overrides the convention property `homeDir`.