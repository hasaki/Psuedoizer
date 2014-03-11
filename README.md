#Psuedoizer

Psuedoizer - Psuedointernationalization Utility

## Psuedoizer Extension

The Psuedoizer Extension integrates into Visual Studio 2012 to provide developers with a fairly hands-free method of generating psuedo-ized resource files.

For projects that have the extension's behavior enabled, it will generate a psuedo-ized resources file for all resources it can detect as being the "default" resource file, that is the file that contains the resources that will be used when a lookup fails in a cultural-specific resource file.

### Enabling the Psuedoizer Extension

Since the Psuedoizer Extension will create files and modify your project, I figured it was best that using Psuedoizer be an "opt-in" experience.  Included with the extension is a new Item Template, the Psuedoizer Configuration file.  This file contains the bare bones configuration options for your project as well as enabling the functionality.

```
<?xml version="1.0" encoding="utf-8"?>
<psuedoizer>
    <enabled>true</enabled>
    <language>xx</language>
    <ignoreBlankValues>false</ignoreBlankValues>
</psuedoizer>
```

#### Configuration

##### Enabled

You'll notice that it also includes an `enabled` flag, this allows you to also at any point temporarily disable the use of the extension.

*Default value: true*

##### Language

This is the language to output the translations for.  Its possible that this is something you'll have to modify for each project you work on.  It should be set to the 2 or 4 character code for the psuedo-translation you want to test during development.  It must be a valid culture code.

##### Ignore Blank Values

This options tells the Pseudoizer to skip over any blank elements in the original sources.

*Default value: false*

### How it works

Implemented using the Visual Studio 2012 SDK, this extension hooks into the (apparantly) internal build starting event to begin looking at all projects in the solution to find files to psuedo-translate.  Using the SDK it also provides a new template file to help create the configuration file needed to enable processing of a project.

Once a build has started it will do several things:

* Go through all projects looking for ones that are enabled
	* These projects have the configuration file added to them and do not explicitly disable processing
* Take the root directory of the project and look for all `*.resx` files
	* If the file is in the project and a psuedoizer candidate (see below)
		* Psuedoize the file and add it to the project


#### What determines whether a resource file is a candidate for psuedo-ization?

Right now the extension looks at three things.

1. The file extension is .resx
2. What the Build Type property is set to, it should be set to `Embedded Resource`
3. What the Custom Tool property is set to, it will either be `PublicResXFileCodeGenerator` or `ResXFileCodeGenerator`

Ideally we could look at the file name to determine whether it should be the root.  But that is a feature for a future version.

### What still needs to be done?

It would be awesome to have the following features added:

* The generated file is added as a dependency to the root file, however I don't think it does any sort of tracking/cleaning up so if you rename the root file the generated one will be left straggling behind.
* Add support for building the psuedo-translated file on command rather than always requiring a build of the solution
* MSBuild support?
* Better method of determining what is the root localization file (i.e. support Windows Forms)

# Credits

* John Robbins's [MSDN Bugslayer article](http://msdn.microsoft.com/en-us/magazine/cc163991.aspx) is responsible for the core part of the psuedoizer functionality
* @shanselman udpated it to include a command line utility, and to be aware of HTML markup and string.format so that it won't mangle characters inside `<>` or `{}` 
* Tim Larson added support for Psuedointernationalizing blank resource entries
* @jackwoodward added a T4 Template for Visual Studio
* @hasaki wrote the Visual Studio extension

# License

No license was found in @shanselman's repository so its anyone's guess as to what the core Psuedoizer code should be considered.

@hasaki has attached the [Microsoft Public License](http://opensource.org/licenses/MS-PL) to the Visual Studio extension, not including the Psuedoizer-specific portion (that is, the code found in the Psuedo.Globalization namespace).  This license was chosen because his work on customizing the [Web Essentials 2012](https://github.com/hasaki/WebEssentials2012) extension may have influenced his code and that project uses this license.