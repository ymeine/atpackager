Visitors.

# File system layout

* [`readme.md`](./readme.md): this current documentation file

Visitors:

* [`ATCompileTemplates.js`](./ATCompileTemplates.js): Compile AT templates
* [`ATDependencies.js`](./ATDependencies.js): Compute AT dependencies
* [`ATNormalizeSkin.js`](./ATNormalizeSkin.js): Normalize Skin
* [`ATRemoveDoc.js`](./ATRemoveDoc.js): remove AT documentation content
* [`ATUrlMap.js`](./ATUrlMap.js): AT URL Map
* [`CheckDependencies.js`](./CheckDependencies.js): Check dependencies
* [`CheckGlobals.js`](./CheckGlobals.js): Check global variables
* [`CheckPackaged.js`](./CheckPackaged.js): Check packaged
* [`CopyUnpackaged.js`](./CopyUnpackaged.js): Copy unpackaged
* [`Hash.js`](./Hash.js):
* [`ImportSourceFile.js`](./ImportSourceFile.js):
* [`ImportSourceFiles.js`](./ImportSourceFiles.js):
* [`JSMinify.js`](./JSMinify.js):
* [`JSStripBanner.js`](./JSStripBanner.js):
* [`Map.js`](./Map.js):
* [`TextReplace.js`](./TextReplace.js):



----

# Introduction: what is a visitor?

A visitor is a kind of hook, which implements some specific methods.

Visitors can be instantiated with a specific configuration, and then added to a packaging. Afterwards, the packaging will call the specific methods on all its visitors at different steps of its process.



# Interface of a visitor

Note: visitor methods don't have significant return value, this doesn't really make sense since the caller doesn't know which visitors are going to be actually called, how many, etc.

## Methods

### Compute dependencies

* Name: `computeDependencies`

Given a file, will return a wet of files to which it depends on.

### On init

* Name: `onInit`

Called when the packaging is initialized.

#### Parameters

1. `packaging`



### On write input file

* Name: `onWriteInputFile`

Called just before an input file is written.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__
	* The packaging containing the input file.
1. `outputFile`
	* interface: `Output File`
	* __required__
	* __in & out__
	* The output file (partly) built from the input file.
1. `inputFile`
	* interface: `Input File`
	* __required__
	* __in & out__
	* The input file about to be written.



### On after build

* Name:  `onAfterBuild`
*

Called right after the `build` method of the packaging has finished.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__
	* The packaging for which the build is finished.



### Others

* `onWriteJSOutputFile`
* `onBeforeOutputFileBuild`
* `onAfterOutputFileBuild`
* `onReachingBuildEnd`





----

Specific visitors





# Replace text

* Name: `TextReplace`

## Configuration

* `files`
	* interface: as expected by the prototype method `isMatch` of `Input File`
	* default: `['**/*']` (all files)
	* __in__
	* A set of patterns to filter the files o be processed.
* `replacements`
	* interface: `Array` of `Object` (see below for details about the replacement object)
	* default: `[]` (no replacement)
	* __in__
	* A list of replacements to be done.

The replacement object:

* `find`
	* interface: `String`
	* __required__
	* __in__
	* The pattern to find in the text and replace.
* `replace`
	* interface: `String`
	* __required__
	* __in__
	* The replacement text.

## Implemented methods

### `onWriteInputFile`

Modifies the content of the given input file only if the latter's name matches the patterns given in configuration (property `files`; for that it uses the method `isMatch` of the input file).

Then, the list of replacements is iterated over and used to replace the whole text content of the file.

For details:

* text content is get and set using respectively `getTextContent` and `setTextContent` on the input file
* replacements are done using the native `replace` method of `String`




# Map

* Name: `Map`

Creates a map of input/output files and writes it on the disk.

The keys of the map are path to input files of the packaging, while values are their associated output file

## Configuration

* `sourceFiles`
	* interface: as expected by the prototype method `isMatch` of `Input File`
	* default: `['**/*']` (all files)
	* __in__
	* Source files to take into account in the map.
* `outputFiles`
	* interface: as expected by the prototype method `isMatch` of `Input File`
	* default: `['**/*']` (all files)
	* __in__
	* Output files to take into account in the map.
* `mapFile`
	* interface: `String`
	* default: `'map.js'`
	* __in__
	* The output name of the map file.
* `mapFile`
	* interface: `String`
	* default: `null`
	* __in__
	* The encoding of the map file.
* `outputDirectory`
	* interface: `String`
	* default: `null` (uses the global directory instead)
	* __in__
	* The output directory of the map file.

## Implemented methods

### `onAfterBuild`

