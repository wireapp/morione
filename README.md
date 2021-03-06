![Pisa gioco del ponte](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/Pisa_GiocoPonte_1935.jpg/800px-Pisa_GiocoPonte_1935.jpg)
# Morione [![Build Status](https://travis-ci.org/marcoconti83/morione.svg?branch=master)](https://travis-ci.org/marcoconti83/morione)

Morione is a Swift subprocess execution library intended to uses in Swift scripts, inspired by [Python subprocess](https://docs.python.org/2/library/subprocess.html). It allows scripts to spawn subprocesses and capture the output and return status. While designed with Swift scripting in mind, it can also be used in other scenarios (e.g. OSX apps).

See also [Targone](https://github.com/marcoconti83/targone/), A Swift command line argument parser and automatic usage description generator inspired by Python Argparse.

## API design

Morione public API is designed keeping in mind ease of use within a script.

To achieve this, errors in executing subprocess are printed on screen instead of throwing errors, not to make the script too cumbersome with `try`s.

The API is documented in the code and tests.

There is a full-fledged API version (returning Optionals) and a compact API version. The compact API will make a lot of assumption and just assert if any of these assumption is not fulfilled. This is intended to be used in scripts where it's desirable to abort the script with an automatically generated error message in case of error. We are aware that this makes it hard to write unit tests, but it contributes to create simple scripts that are concise and to the point. Compare `Subprocess.output("/bin/df", "-h", "-l")` (compact API) with `Subprocess("/bin/df", "-h", "-l").execute()!.output`.


A simple example Swift script integration can be found in the [Example](https://github.com/marcoconti83/morione/tree/master/Examples) folder.

## Examples

To print the last line of the output of `/bin/df -h -l` in your script, use:

```
let diskFree = Subprocess.output("/bin/df", "-h", "-l")
print lines.last
```

# How to Use

## API 

- Execute a command, abort current process on failure:

```Subprocess.runOrDie("/bin/rm", "shopping-list.txt")```

- Execute a command, get the termination status:

```let status = Subprocess.run("/usr/bin/grep","bread","shopping-list.txt")```

- Execute a command, get the output:

```let output = Subprocess.run("/bin/cat","shopping-list.txt")```

- Execute a command, get the output as an array of strings, one element per output line:

```let lines = Subprocess.run("/bin/ls","folder")```

- Execute a command, get the output, the error output on `stdErr` and the termination status:

```
guard let result = Subprocess("do.sh").execute(true) else { ... } // will fail if "do.sh" does not exist
let status = result.status
let error = result.error
let output = result.output
```

- Pipe commands, get the final output:

```
let pipeline = Subprocess("/bin/ls","-l","folder") | Subprocess("/usr/bin/grep", "file-") | Subprocess("/usr/bin/sort","-r")
let output = pipeline.output()
```

## How to integrate in your script/application

Just add ```import Morione``` to your script

In order to be able to import it, you need to have the `Morione.framework` in the Swift search path. You can achieve this by compiling it yourself or downloading a binary version from a release. You need to invoke Swift with the `-F` argument, pointing to the folder where Morione is stored.

### Carthage integration
Morione can be downloaded locally with [Carthage](https://github.com/Carthage/Carthage). 

Just add 

```github "marcoconti83/morione"```

to your `Cartfile`. After running

```carthage update```

you would be able to run any swift file from the folder where you run carthage, with:

```swift -F Carthage/Build/Mac```

The `-F` flag can also be included in the [shebang](https://en.wikipedia.org/wiki/Shebang_%28Unix%29) line of the script, so that you can just invoke the script directly (e.g. ```$> do.swift```). This is the approach used in the [examples](https://github.com/marcoconti83/morione/tree/master/Examples) included with this project.

### Without Carthage
You can download the framework binary from the GitHub [latest release](https://github.com/marcoconti83/morione/releases/latest)


