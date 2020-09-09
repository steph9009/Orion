# Orion
 an open-source framework for triage collection of relevant incident response and forensics artifacts from various operating systems

[![MIT](https://img.shields.io/badge/license-MIT-blue)](https://choosealicense.com/licenses/mit/)
[![Latest version](https://img.shields.io/badge/version-v0.2.0-blue)](https://github.com/anthonybm/goMass/releases/tag/v0.2.0-alpha)
[![](https://goreportcard.com/badge/github.com/anthonybm/Orion)](https://goreportcard.com/report/github.com/anthonybm/Orion)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fanthonybm%2FOrion.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fanthonybm%2FOrion?ref=badge_shield)
![status](https://img.shields.io/badge/status-alpha-red)

### tl;dr
* Compiles to a single binary that references a config file
* Builds/runs on **macOS** and **Windows** (theoretically Linux as well but not tested :shrug:)
* Currently in "alpha" - (*needs more testing and features*) - but plenty to use and work with now :) 
* That "plenty" includes various macOS modules and a comprehensive Windows example! 

## Purpose
Orion is an artifact triage tool framework that facilitates independent modules to run and collect whatever forensic artifact is specified. Orion allows contributers to add functionality to parse and triage unimplemented forensic artifacts in the form of "modules", which essentially are packages in Go that contain the required constructs and supplementary functionality to parse the artifacts. 

This ease of adding functionality in the form of a single file and few lines of change was inspired by other existing open source tools already written in Python. The benefit of Orion over others is that it compiles down to a single binary that utilizes a config file, rather than requiring various source and third-party code to be copied when you want to utilize the tool. 

### Usage 
This is an alpha - work in progress! Its at a stage now where I am ready to show others the work done and possible - **all existing modules are runnable, they will produce output :smile: Please read all documentation and review before running on your own system.** Of note: 
- At the moment you will have to build executables on your own system, they will be included in future releases
	- Take consideration that you may have to set the execution permissions of the binary on some systems
- The configs/ folder contains a mac and windows config sample, all present keys are required
- The modules listed in each are what exist at this time, comments will denote WIP/experimental work
- At this time, output format is restricted to CSV for modules and JSON for logging

```
usage: Orion [-h|--help] [--list] [-l|--log-level (none|info|debug|error)]
             -m|--mode (mac|windows) [-M|--no-multithread] [-f|--output-format
             (csv|json|sqlite|xlsx)] [-o|--output-dir "<value>"] -c|--config
             "<value>" [-T|--testing-mode] [-F|--forensic] [-t|--target
             "<value>"]

             Orion framework for triage of relevant incident response and
             forensics artifacts from various operating systems

Arguments:

  -h  --help            Print help information
      --list            List available modules.
  -l  --log-level       Set the logging level, or set it to none.. Default:
                        info
  -m  --mode            Set the mode for Orion, used for config parsing and
                        module selection.
  -M  --no-multithread  If flag is enabled, multithreading is disabled..
                        Default: false
  -f  --output-format   Set the output format file type.. Default: csv
  -o  --output-dir      Set the output directory for files generated by Orion..
                        Default: Output/
  -c  --config          Set the config path
  -T  --testing-mode    Enable testing mode for development purposes only..
                        Default: false
  -F  --forensic        Enable Forensic mode - safer artifact parsing where
                        applicable and can treat target path as Mounted
                        Volume/Mounted Evidence. Default: false
  -t  --target          Specify the root target path to reference artifacts
                        from - i.e. <target>/pathToPlist.plist. Default: /
```
> **Note:** Interrupting with SIGINT ```ctrl + c``` once will signal Orion to try to package modules before aborting
#### Testing usage example
	./Orion -m mac -f csv -o output -c configs/mac.toml -l debug -T
 Will run modules specified in the TOML config on macOS in testing mode with debug level output

#### Actual usage 
sudo ./Orion -m mac -f csv -o output -c path_to/mac.toml -l info

### Building
#### Pre-requisites
* [Go version 1.14](https://golang.org/dl/) installed and configured
* C compiler for cgo modules [such as this for Windows](https://jmeubank.github.io/tdm-gcc/download/)
* Access to macOS for building Mac binary
* Access to Windows for building Windows binary

1) Fork the Orion repository. Follow the [GitHub Help instructions](https://help.github.com/articles/fork-a-repo/) on how to fork a repo.
2) Clone it to your local machine and navigate to the directory where you've cloned the source code
4) ```go build``` will generate an Orion binary which you can use along with a valid config file 

Orion currently has functionality to
 - Create and integrate modules for macOS (many written) and Windows (one example file system walk written)
 - Log errors, debug, warning, and input statements
 - Output logs in JSON format
 - Output for modules in CSV format
 - Tested on OSX 10.15.5 and Windows 10
 
## But how does it work?
Glad you asked :wink:
* The Orion binary requires a few command line arguments to determine how it will run, such as use of a specifically configured [TOML](https://github.com/toml-lang/toml) config file that lists modules to run and configurations for specifc modules.
```
...
modules = [ 
   "MacSampleModule",
   ...
]
...
# other module specific parameters 
...
```
* Orion reads the command line arguments and specific config file to determine what to run. Modules must be registered in the typeRegistry (ex. `registerType((*macsample.MacSampleModule)(nil))`) in order to run
* Orion will execute each module found as its own [goroutine](https://tour.golang.org/concurrency/1) by calling its `Start()` function (within Start, you specify the module structure) 
* Each module should write output to a file with a name constructed by `orionRuntime + "_" + module + "." + outputtype`
* If a non-fatal module error occurs along the way, Orion will log it 

## Roadmap
 - Testing :) 
 - Ensure documentation is sufficient
 - Graceful exit on SIGINT
 - More modules for macOS
 - Sign for macOS? 
 - More modules for Windows
 - Support Linux module writing
 - Support no-logging mode
 - Support for module output in other formats than CSV
 - Support for tarballing module output
 - Support for uploading module output 

## Contributing
Thank you for being interested in contributing to Orion! 

Please see [CONTRIBUTING.md](https://github.com/anthonybm/Orion/blob/master/CONTRIBUTING.md) for all of the details. 

## License 
This project is licensed under the terms of the MIT license. See [LICENSE](https://github.com/anthonybm/Orion/blob/master/LICENSE) for details.

## Code of Conduct
This project adheres to the [Contributor Covenant code of conduct](https://github.com/anthonybm/Orion/blob/master/CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

See licenses/ for credits to other projects I referenced and took inspiration from :smile:

## Why Orion?
"represented by the figure of a hunter with belt and sword"
* We're threat hunters :wink:
* The framework and modules are the tools and sword to go hunting :smile:
