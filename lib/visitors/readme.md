Built-in visitors.

# File system layout

* [`readme.md`](./readme.md): this current documentation file

Visitors:

* [`ImportSourceFile.js`](./ImportSourceFile.js): [Import a source file](#import-source-files)
* [`ImportSourceFiles.js`](./ImportSourceFiles.js): [Import a set of source files](#import-source-files)
* [`Hash.js`](./Hash.js): [Insert a hash into the output file name](#insert-a-hash-into-the-output-file-name)
* [`CheckPackaged.js`](./CheckPackaged.js): [Check that all files have been packaged](#check-that-all-files-have-been-packaged)
* [`CopyUnpackaged.js`](./CopyUnpackaged.js): [Copy unpackaged files](#copy-unpackaged-files)
* [`TextReplace.js`](./TextReplace.js): [Replace text in files](#replace-text)
* [`Map.js`](./Map.js): [Map](#map)

JavaScript specific visitors:

* [`JSMinify.js`](./JSMinify.js): [Minify JavaScript files](#minify-a-javascript-file)
* [`CheckDependencies.js`](./CheckDependencies.js): [Check/add dependencies](#check-add-dependencies)
* [`CheckGlobals.js`](./CheckGlobals.js): [Check global variables use](#check-global-variables-use)
* [`JSStripBanner.js`](./JSStripBanner.js): [Remove comment banners in JavaScript files](#remove-js-banner)

Aria Templates specific visitors:

* [`ATCompileTemplates.js`](./ATCompileTemplates.js): [Compile Aria Templates templates](#compile-aria-templates-templates)
* [`ATDependencies.js`](./ATDependencies.js): [Compute Aria Templates dependencies](#compute-aria-templates-dependencies)
* [`ATRemoveDoc.js`](./ATRemoveDoc.js): [Remove Aria Templates documentation data](#remove-aria-templates-documentation-data)
* [`ATUrlMap.js`](./ATUrlMap.js): [AT URL Map](#at-url-map)
* [`ATNormalizeSkin.js`](./ATNormalizeSkin.js): [Normalize Aria Templates skin](#normalize-aria-templates-skin)



----

# Introduction: what is a visitor?

A visitor is a kind of hook, which implements some specific methods.

Visitors can be instantiated with a specific configuration, and then added to a packaging. Afterwards, the packaging will call the specific methods on all its visitors at different steps of its process.

It implements the concept of _events_:

* events names are implemented methods' names
* adding a visitor is like registering to an event, with implemented methods indicating which events are subscribed
* calling all visitors with a specific method is like emitting the corresponding event




# Some concepts

_Before going further into details, here are a few concepts to know, in order to understand some features, and also to avoid repeating things in the documentation, making it less digestible._

All visitors expect a configuration object as the unique argument of their constructors; they are described in this documentation for each specific visitors. There are several things to say, at least __concerning built-in visitors__ (those described here).

First, the configuration object is not altered, its properties are simply used.

Then, most of visitors accept one or more patterns to filter files that they should process. This uses the concept of glob patterns, which are used with the method `isMatch` of `Source File` or `Ouput File` (depending on the actual type of received file), which anyway works the same in both cases.

Visitors processing single files will not do anything with a file that doesn't match the specified pattern. If no pattern was given, the usual default one is chosen so that it takes into account all files (`*/**`), or all files that are relevant (for instance, for visitors processing JavaScript files, the default pattern will filter extension, keeping `.js` ones).

Therefore, __unless specified otherwise__, the above applies for configuration objects.








# Interface of a visitor

Note: visitor methods don't have significant return value, this doesn't really make sense since the caller doesn't know which visitors are going to be actually called, how many, etc.

## Methods



### On initialization of the packaging

* Name: `onInit`

Called when the packaging is initialized.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__



### Before writing an input file

* Name: `onWriteInputFile`

Called just before an input file is written.

You can use it to alter its content before it is written.

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
	* interface: `Source File`
	* __required__
	* __in & out__
	* The input file about to be written.



### After the build of the packaging has finished

* Name:  `onAfterBuild`

Called right after the `build` method of the packaging has finished.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__
	* The packaging for which the build is finished.



### After an output file has been built

* Name: `onAfterOutputFileBuild`

Called right after the content of an output file has been built.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__
	* The packaging containing the output file.
1. `outputFile`
	* interface: `Output File`
	* __required__
	* __in & out__
	* The output file which has just been built.



### Before writing a JavaScript output file

* Name: `onWriteJSOutputFile`

Called right before the content of a JavaScript file is going to be written.

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
1. `toBeWritten`
	* interface: [`Object`](http://devdocs.io/javascript/global_objects/object) with specific properties (see below)
	* __required__
	* __in & out__
	* An object containing the JavaScrip content to be written.

The `toBeWritten` object contains two properties:

* `content`
	* interface: UglifyJS AST
	* __required__
	* __in & out__
	* The AST representing the JavaScript content of the file.
* `options`
	* interface: [`Object`](http://devdocs.io/javascript/global_objects/object)
	* __required__
	* __in & out__
	* Output options.


### When reaching the end of the build

* Name: `onReachingBuildEnd`

Called when the build of the packaging doesn't have anymore file to build.

It's about to be finished, but this hook is the occasion to add new files to be processed.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__
	* The packaging being built.




### Before an output file is built

* Name: `onBeforeOutputFileBuild`

Called just before an output file is going to be built.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__
	* The packaging containing the output file.
1. `outputFile`
	* interface: `Output File`
	* __required__
	* __in & out__
	* The output file about to be built.



### When computing the dependencies

* Name: `computeDependencies`

Called to compute the dependencies of an input file. It should add the found dependencies to the given packaging.

#### Parameters

1. `packaging`
	* interface: `Packaging`
	* __required__
	* __in & out__
	* The packaging containing the input file.
1. `inputFile`
	* interface: `Source File`
	* __required__
	* __in & out__
	* The input file to process.





----

General purpose specific visitors.

__Unless specified otherwise__, the default value for files filters includes all files: `[**/*]`.

----





# Import a source file

* Name: `ImportSourceFile`

Adds a source file to the packaging, regarding the given configuration.

## Configuration

* `sourceFile`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* The path of the file to import.
* `targetBaseLogicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* The directory path to set for the input file (purely logical, not the actual one on the storage device).

## Implemented methods

### `onInit`

The given `sourceFile` path is resolved using standard Node.js module `path`'s `resolve` method.

Then, a new `Source File` instance is created with given `targetBaseLogicalPath` as `logicalPath`, and added to the packaging.

The previous resolved path is used only for being able to actually load the content of the file: the built-in content provider `fileLoader` is set as the file's content provider and the latter's load path is set to the resolved path.





# Import source files

* Name: `ImportSourceFiles`

Adds source files to the packaging, regarding the given configuration.

## Configuration

### Files filtering

* `sourceFiles`
	* interface: as expected by the method [`grunt.file.expand`](http://gruntjs.com/api/grunt.file#grunt.file.expand)'s `patterns` parameter
	* default: `['**/*']`
	* A set of patterns to filter the files to be imported.

### Import configuration

* `sourceDirectory`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: `""` (empty)
	* The directory from which to import the files.
* `targetBaseLogicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: `""` (empty)
	* The directory path to set for the input files (purely logical, not the actual one on the storage device).

## Implemented methods

### `onInit`

File are imported from the given `sourceDirectory` and filtered with the given `patterns`, using [`grunt.file.expand`](http://gruntjs.com/api/grunt.file#grunt.file.expand).

Then, for each found file, a new `Source File` instance is created, with `targetBaseLogicalPath` as `logicalPath`, and added to the packaging. In order to remain able to load the content of the file, the built-in content provider `fileLoader` is set as their content provider, and their load path is configured through it.





# Insert a hash into the output file name

* Name: `Hash`

Inserts a hash computed with given options from the output file's content into its file name, regarding the given pattern.

## Configuration

### Files filtering

* `files`: a set of patterns to filter the files to be processed.

### Hash configuration

* `hash`
	* interface: [`String`] or as expected by Node.js module [`crypto`](http://devdocs.io/node/crypto)(http://devdocs.io/javascript/global_objects/string)
	* default: `md5`
	* The hash method to use (see below for more information about the possible ones).
* `pattern`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: `"[name]-[hash][extension]"`
	* The new output file pattern (see below for more information about its format).

#### The hash methods

The standard Node.js module [`crypto`](http://devdocs.io/node/crypto) is used to compute the hash, therefore the value of the property `hash` can be anything that it accepts.

However, an additional hash method is supported: `"murmur3"`. For that, it uses the npm package [`murmurhash-js`](https://github.com/garycourt/murmurhash-js).

#### The pattern format

The pattern defines the new basename of the file (which means, its whole folder path apart).

In order to build the new one, you have three variables available:

* `name`: the basename of the file without its extension
* `hash`: the value of the computed hash (this one should be used, this is the purpose of the visitor)
* `extension`: the extension of the file, dot included

In the pattern string, you can use those variables by surrounding them with brackets. There is nothing more specific when processing the pattern, no escaping, etc. Also, if you put an unsupported variable name between brackets, it won't be processed, the corresponding string including its brackets will be taken as is.

## Implemented methods

### `onAfterOutputFileBuild`

Replaces the basename of the given output file, following the given or default pattern, normally including the hash value.

See above for a main description about the hash method and the pattern processing. Below are given more details.

The hash is computed either by the standard [`crypto`](http://devdocs.io/node/crypto) module of Node.js or by [`murmurhash-js`](https://github.com/garycourt/murmurhash-js).

In the first case, the method [`createHash`](http://devdocs.io/node/crypto#crypto_crypto_createhash_algorithm) is used, and the value of the configuration property `hash` is passed as is.

The second case occurs if the value of this property resolves to `"murmur3"`, __and has precedence__ (so if ever one day this is supported by Node.js, the npm package will still take care of it). In this case, the method `murmur3` is used.

In both cases, the content of the file is given as is to compute the hash. This content is read using the utility [`grunt .file.read`](http://gruntjs.com/api/grunt.file#grunt.file.read), with an `encoding` set to [`null`](http://devdocs.io/javascript/global_objects/null): raw data is used, to be sure to have unique hashes regarding the actual content (binary) of the file.





# Check that all files have been packaged

* Name: `CheckPackaged`

Checks that no file present as source file of the packaging was left unprocessed.

## Configuration

### Files filtering

* `files`: A set of patterns to filter the files to be checked.

## Implemented methods

### `onAfterBuild`

Will log an error with grunt ([`grunt.log.error`](http://gruntjs.com/api/grunt.log#grunt.log.error-grunt.verbose.error)) for each source file contained in the packaging, and which has no associated output file: that means it wasn't used at all even though it was configured to be part of the packaging.





# Copy unpackaged files

* Name: `CopyUnpackaged`

Copy files present in the packaging but not used to build any output file.

## Configuration

### Files filtering

* `files`: A set of patterns to filter the files to be copied.

### Copy configuration

* `builder`
	* interface: a builder configuration
	* default: `{type: 'Copy'}` (the builder `Copy` without specific configuration)
	* The builder configuration used to retrieved a builder to use to copy the files.
* `renameFunction`
	* interface: `Function`
	* default: the identity function (it returns its first given argument, as is)
	* A function used to rename the copied file.

## Implemented methods

### `onReachingBuildEnd`

Will copy all the source files that the packaging contains, which were not used already to build other files, and which match the given `files` pattern.

The file content is copied using the builder retrieved/created from the given `builder` configuration.

What the visitor does however is only to create new `Output File` instances, add them to the packaging, and configure each with the above mentioned builder. The name of the output files are taken from the names of the input files, possibly renamed using the given `renameFunction`.





# Replace text

* Name: `TextReplace`

## Configuration

### Files filtering

* `files`: A set of patterns to filter the files to be processed.

### Replacement configuration

* `replacements`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array) of [`Object`](http://devdocs.io/javascript/global_objects/object) (see below for details about the replacement object)
	* default: `[]` (no replacement)
	* A list of replacements to be done.

The replacement object:

* `find`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* The pattern to find in the text and replace.
* `replace`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* The replacement text.

## Implemented methods

### `onWriteInputFile`

Modifies the content of the given input file only if the latter's name matches the patterns given in configuration (property `files`; for that it uses the method `isMatch` of the input file).

Then, the list of replacements is iterated over and used to replace the whole text content of the file.

For details:

* text content is get and set using respectively `getTextContent` and `setTextContent` on the input file
* replacements are done using the native `replace` method of [`String`](http://devdocs.io/javascript/global_objects/string)





# Map

* Name: `Map`

Creates a map of input/output files and writes it on the disk.

The keys of the map are path to input files of the packaging, while values are their associated output file

## Configuration

### Files filtering

* `sourceFiles`: source files to take into account in the map.
* `outputFiles`: output files to take into account in the map.

### Map file configuration

* `mapFile`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: `'map.js'`
	* The output name of the map file.
* `mapFileEncoding`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: [`null`](http://devdocs.io/javascript/global_objects/null)
	* The encoding of the map file.
* `outputDirectory`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: [`null`](http://devdocs.io/javascript/global_objects/null) (uses the global directory instead)
	* The output directory of the map file.

## Implemented methods

### `onAfterBuild`





----

JavaScript specific visitors.

__Unless specified otherwise__, the default value for files filters includes all JavaScript files: `[**/*.js]`.

----





# Minify a JavaScript file

## Configuration

### Files filtering

* `files`: global filter
* `outputFiles`: output files to take into account
* `inputFiles`: input files to take into account

### Minification configuration

* `skipJSConcatParts`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* If truthy, will not process input files part of an output file built with the built-in builder `JSConcat`. Useful since this doesn't make much sense to minify each single part, but rather the resulting output file as whole.
* `compress`
	* interface: if the value `false` is not given, as expected by `UglifyJS.Compressor`
	* default: [`undefined`](http://devdocs.io/javascript/global_objects/undefined), and if value is different from `false` but still falsy, an empty object `{}`
	* The UglifyJS compressor configuration, used to compress the AST.
* `mangle`
	* interface: if the value `false` is not given, as expected by UglifyJS AST method `mangle_names`
	* default: [`undefined`](http://devdocs.io/javascript/global_objects/undefined), and if value is different from `false` but still falsy, an empty object `{}`
	* The name mangling configuration.
* `output`
	* interface: as expected by the second argument of the `uglifyHelpers`'s `astToString` helper
	* default (if value is falsy): `{ascii_only: true}`
	* The output options to be used for the AST.

## Implemented methods

### `onWriteInputFile`

Will minify the input file AST if:

* it matches the `files` filter
* it matches the `inputFiles` filter
* in case `skipJSConcatParts` is truthy: it is not part of an output file whose builder is an instance of the built-in builder `JSConcat`

It retrieves the AST content using the built-in `uglifyJSContentProvider` content provider, and if there is one compresses it. See below section for more information.

It will set the new AST using the same content provider, and forwarding the given `output` configuration.

Finally, the content provider of the file will be set to the one used during this whole process, to be sure the proper content will be retrieved later on.

### `onWriteJSOutputFile`

Compresses the given AST in forwarded content wrapper object, and sets the latter's output `options` using the one passed in configuration (under `output` property).

See below section for more information.

## AST compression

To compress the AST, two things are used:

* the compressor
* the names mangling

Both are used only if a corresponding configuration object has been created (please refer to the visitor configuration documentation for more details).

If the `compressor` configuration is not `false`, its computed value is forwarded to the constructor `UglifyJS.Compressor` to be build a new compressor object, and then used to compress the AST.

If the `mangle` configuration is not `false`, its computed value is forwarded to the method `mangle_names` of the AST object in order to mangle the names.





# Check/Add dependencies

* Name: `CheckDependencies`

Check the dependencies on output files, and add corresponding missing source files to the packaging: they need to be part of the package in order to be used by the depending file.

## Configuration

### Files filtering

* `files`

### Dependencies check

* `noCircularDependencies`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* Whether to allow [circular dependencies](https://en.wikipedia.org/wiki/Circular_dependency) or not.
* `addUnpackagedDependencies`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* Whether to add missing dependencies instead of just checking for their presence.
* `unpackagedDependenciesError`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* If unpackaged dependencies are not added (`addUnpackagedDependencies` is falsy), whether to log an error in case of missing dependency or not.

### Ordering

* `checkPackagesOrder`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* Whether dependencies should be included by the main build before or during the build of this output file. indeed, in some cases some will be included after, as dependencies of other output files.
* `reorderFiles`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* Controls the order of source files contained by the output file: whether to keep the order in which source files have been encountered while processing dependencies, or to let the latter at the end of the list, as they were appended.


## Implemented methods

### `onBeforeOutputFileBuild`

Anytime a circular dependency is found, an error is logged (using [`grunt.log.error`](http://gruntjs.com/api/grunt.log#grunt.log.error-grunt.verbose.error)), specifying the list of concerned dependencies, and the current dependency processing is stopped.





# Check global variables use

* Name: `CheckGlobals`

Checks global variables use.

## Configuration

### Files filtering

* `files`

### Globals configuration

* `strict`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default:
	* Whether to use strict checking or not. In strict mode, a global must be explicitly allowed to be considered as accepted, while otherwise it must only not be forbidden.

Globals permissions:

1. `allowStdJSGlobals`
	* interface: value `false` or anything else
	* default: [`undefined`](http://devdocs.io/javascript/global_objects/undefined) (truthy in this context)
	* Whether to allow standard JavaScript globals or not (see below for a list).
1. `allowCommonJSGlobals`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: falsy
	* Whether to allow global defined by [CommonJS](http://wiki.commonjs.org/wiki/CommonJS) or not (see below for a list).
1. `allowedGlobals`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array) of [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: [`undefined`](http://devdocs.io/javascript/global_objects/undefined)
	* If specified, the globals listed here are set as being allowed.
1. `forbiddenGlobals`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array) of [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: [`undefined`](http://devdocs.io/javascript/global_objects/undefined)
	* If specified, the globals listed here are set as being forbidden.

The list above is ordered, since all these properties are processed in this order, with any of the possible override. So for instance listed forbidden globals have precedence over allowed one.

Now here are the lists of globals per category:

* Standard (`allowStdJSGlobals`):
	* [`Math`](http://devdocs.io/javascript/global_objects/math)
	* [`RegExp`](http://devdocs.io/javascript/global_objects/regexp)
	* [`Array`](http://devdocs.io/javascript/global_objects/array)
	* [`Date`](http://devdocs.io/javascript/global_objects/date)
	* [`Number`](http://devdocs.io/javascript/global_objects/number)
	* [`Function`](http://devdocs.io/javascript/global_objects/function)
	* [`String`](http://devdocs.io/javascript/global_objects/string)
	* [`Error`](http://devdocs.io/javascript/global_objects/error)
	* [`Object`](http://devdocs.io/javascript/global_objects/object)
	* [`parseInt`](http://devdocs.io/javascript/global_objects/parseint)
	* [`parseFloat`](http://devdocs.io/javascript/global_objects/parsefloat)
	* [`isNaN`](http://devdocs.io/javascript/global_objects/isnan)
	* [`isFinite`](http://devdocs.io/javascript/global_objects/isfinite)
	* [`encodeURIComponent`](http://devdocs.io/javascript/global_objects/encodeuricomponent)
	* [`decodeURIComponent`](http://devdocs.io/javascript/global_objects/decodeuricomponent)
	* [`encodeURI`](http://devdocs.io/javascript/global_objects/encodeuri)
	* [`decodeURI`](http://devdocs.io/javascript/global_objects/decodeuri)
	* `escape`
	* `unescape`
	* [`eval`](http://devdocs.io/javascript/global_objects/eval)
	* [`NaN`](http://devdocs.io/javascript/global_objects/nan)
	* [`undefined`](http://devdocs.io/javascript/global_objects/undefined)
	* [`Infinity`](http://devdocs.io/javascript/global_objects/infinity)
* [CommonJS](http://wiki.commonjs.org/wiki/CommonJS) (`allowCommonJSGlobals`):
	* `module`
	* `exports`
	* `require`
	* `__filename`
	* `__dirname`

## Implemented methods

### `onWriteInputFile`

Will check all files matching the given pattern `file` for global variables use.

Everytime a forbidden global is used in one of those files, an error will be logged using grunt ([`grunt.log.error`](http://gruntjs.com/api/grunt.log#grunt.log.error-grunt.verbose.error)), specifying which global was used and in which file.





# Remove JS Banner

* Name: `JSStripBanner`

Removes from the content of the file what is considered as a banner; content is considered itself as JavaScript source code.

## Configuration

### Files filtering

* `files`
	* default: `['**/*.js']` (all JavaScript files)
	* A set of patterns to filter the files to be processed.

### Banners types

* `line`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: falsy
	* Whether single line comments should be removed as well.
* `block`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: falsy
	* Whether all block comments should be removed unconditionally, or block comments with a specific syntax should be preserved.

## Implemented methods

### `onWriteInputFile`

A banner is a piece of comment put on top of the file, before any actual source code.

Here are the kind of comments removed, depending on the given configuration:

* `line` is truthy: `// ...` leading comments are removed
* `block` is truthy: `/* ... */` leading comments are removed
* `block` is falsy: `/* ... */` leading comments are removed, except those with an extra exclamation mark, like this: `/*! ... */`

You can see that anyway one type of block comments will be removed.





----

Aria Templates specific visitors.

__Unless specified otherwise__, the default value for files filters includes all files: `[**/*]`. In some cases some are specified with a filter to include JavaScript files. Indeed, Aria Templates is made of both JavaScript files and Template files, and the latter can even get compiled to JavaScript files too.

----





# Compile Aria Templates templates

* Name: `ATCompileTemplates`

Compiles Aria Templates templates.

## Configuration

### Files filtering

* `files`

## Implemented methods

### `onWriteInputFile`

In addition to the given `files` filter, if the file does not correspond to an actual template (we determine it if we can't find an associated parser), the file won't be processed.

It uses the content provider `ATCompiledTemplate` to compile the template and store the associated content, and also sets it as the default content provider of the file, so that this is the content that would be fetched.





# Compute Aria Templates dependencies

* Name: `ATDependencies`

Computes the dependencies of the given input file, and adds them to the latter.

## Configuration

### Files filtering

* `files`

### Options

* `mustExist`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* Whether to be strict or not when finding dependencies: if truthy, determined dependencies must be found in the packaging.
* `externalDependencies`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array) of [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: `[]`
	* List of paths of dependencies to consider as external, and therefore which do not have to be inside the packaging.

## Implemented methods

### `computeDependencies`

Dependencies of the given input file are computed, and then actually specified as being dependencies of the file.

Note that dependencies must be part of the packaging already, either already added to it or present in its source directory.

If a dependency could not be found, is not part of the specified `externalDependencies` and if `mustExist` is truthy, an error is logged.





# Remove Aria Templates documentation data

* Name: `ATRemoveDoc`

Removes documentation data, with impact on runtime and/or package's size.

## Configuration

### Files filtering

* `files`
	* default: `['**/*.js']` (all JavaScript files)

### Elements to remove

* `removeBeanDescription`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* Remove the description of properties in Bean definitions
* `removeEventDescription`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `true`
	* Remove the description of events in Aria object definitions
* `removeErrorStrings`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `false`
	* Remove the error strings. __See description for details__.
* `replaceStaticsInErrors`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: `false`
	*  __See description for details__.

## Implemented methods

### `onWriteInputFile

Removes the selected documentation content from the given input file.

If something actually changed (something was removed), the content provider of the file is set to `uglifyContentProvider`.

To remove the content, it uses the utility [`ATRemoveDoc`](../ATRemoveDoc.js). However, here is a description below.

Errors strings are removed from:

* `$statics`
* in function calls:
	* `$logError`
	* `$logWarn`
	* `$logInfo`
	* `_logError`





# AT URL Map

* Name: `ATUrlMap`

## Configuration

### Files filtering

* `sourceFiles`: source files to take into account in the map
* `outputFiles`: output files to take into account in the map





# Normalize Aria Templates skin

* Name: `ATNormalizeSkin`

Normalizes Aria Templates skin definitions.

## Configuration

### Files filtering

* `files`
	* default: `['**/*.js']` (all JavaScript files)

### Normalization options

* `jsonIndent`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: _4 spaces_
	* The indentation string to use.
* `strict`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: falsy
	* Whether to log actual error messages if there are errors, or to simply log warnings.

## Implemented methods

### `onWriteInputFile`

First note that in addition to the given `files` filter, files who don't define an Aria class with a classpath corresponding to the skin (`aria.widgets.AriaSkin`) will be skipped as well.

Then, it normalizes the skin definition using `aria.widgets.AriaSkinNormalization.normalizeSkin`, replacing the content of the file if done with success, logging an error otherwise.
