## About this repository

This repository contains the files which are required for CloudNet to run the updater. In this file everything is
explained about how the updater actually works.

### Files

This repository contains (in each branch) the exact same file tree:

- `checksums.properties`: contains the checksums for all crucial files which get downloaded by the updater. Before
  downloading, the updater checks if the checksum in this repository and locally are matching. If so, nothing gets
  downloaded (aka: no update available). If not the updater will download each file of which the checksum isn't
  matching. All checksums in the file are in the [SHA3-256](https://en.wikipedia.org/wiki/SHA-3) format.
- `launcher.jar`: The latest build output of the launcher file. This file will be used to update the launcher using
  the `launcher-patcher`.
- `launcher-patcher.jar`: The build output of the latest launcher patcher. If a launcher update is available the
  launcher patcher will be used when stopping the node to update the launcher itself and applies patches (if required)
  to for example the launcher.cnl file.
- `node.jar`: The latest build output of the CloudNet node. This file will contain the updated wrapper as well which is
  the reason why this repository doesn't contain the wrapper file.
- `modules.json`: This file contains all officially supported modules which can get directly installed
  using `module install` in the node. (For more information see: [modules.json](#modulesjson))
- `modules/`: This directory contains all modules which are maintained by CloudNet. Outside modules are not here but
  listed in the module.json file if the author did submit it to us.

### modules.json

The file contains all modules and will be updated every time the launcher starts. The original file is located in
our [main repository](https://github.com/CloudNetService/CloudNet-v3) and contains all outside modules. The modules
which are maintained by CloudNet will be added when generating the updater files. The file has the following structure:

```json5
{
  "entries": [
    // All entries go here
  ]
}
```

An entry **always** has the following properties:

- `name`: The unique name of the module (must match the name in the module description file).
- `version`: The current version of the module.
- `sha3256`: The [SHA3-256](https://en.wikipedia.org/wiki/SHA-3) sum of the module file.
- `description`: A short description what the module does for you.
- `url`: The download url of the module. It **must** be a download from GitHub, preferably from a release. Internal
  modules are using the placeholders `%updateRepo%` (the current updater repo used by the launcher)
  and `%updateBranch%` (the current branch the updater is pulling the updates from). All other module download urls must
  link directly to the module jar file.
- `maintainers`: A collection of names who are the maintainers of the module. The names **must** match the names used on
  GitHub as these people are the only ones who will be able to publish updates for the module through our repository.
- `releaseNotes`: A (string) collection containing the release notes of the latest version.
- `dependingModules`: A collection of modules which are required in order to run the module. All modules **must** be
  available through the module file as well.

### How all these files get here

The updater files will be generated by a custom gradle task (`genUpdaterInformation`) into a git-excluded directory
called `.launchermeta` (Please note that the updater task will not run by default, you must specifically add it to the
command line to run it. The recommended build command
is: `./gradlew build checkLicenses test shadowJar genUpdaterInformation --stacktrace`). GitHub actions will then publish
the updater files into this repository using
the [publish-subdir-action](https://github.com/s0/git-publish-subdir-action).

### How to test your changes to modules and cloudnet without getting the files overridden

It's as simple as that: enable dev mode. You can either do this by going into your launcher.cnl and enabling it there,
or by appending `--dev` to the command line (for example: `java -jar launcher.jar --dev`). In order for this to work you
need to place a file called `cloudnet.jar` in the same directory your launcher is in - it will be used as the fallback
node version. All updates will be skipped.

:warning: **USE THIS OPTION WITH CAUTION!** Updates might contain security fixes! It is not recommended that you disable
them! :warning:

### Help

If you need to help or you have further questions about this repository (or how it works, how you can get your module
into here etc.) feel free to ask on our [Discord Server](https://discord.cloudnetservice.eu/). Happy updating!
