# Plugin System

PowerTunnel provides a simple plugin system: during the proxy server bootstrap it injects every jarfile found in the plugins directory into the JVM and initializes plugin's main class written in the plugin manifest.

Each PowerTunnel plugin is a regular `.jar` file which contains a manifest, and, optionally, preferences schema and locale files.

## Plugin Manifest

Plugin manifest is a file named `plugin.ini`, which contains basic information about the plugin: unique identifier, name, version and entry point (main class).

### Required fields

* `id` - unique identifier of the plugin  
Filename of the default plugin configuration file is the same as plugin ID.
* `version` - plugin version display name
* `versionCode` - plugin version code
* `name` - plugin display name
* `mainClass` - plugin entry point
* `targetSdkVersion` - minimum required SDK to work

### Optional fields

* `description` - plugin description
* `author` - plugin author
* `homepage` - plugin homepage (e.g. GitHub repository if it's open-source)