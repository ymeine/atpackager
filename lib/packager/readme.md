# File system layout

* [`readme.md`](./readme.md): this current documentation file

Classes:

* [`outputFile.js`](./outputFile.js): [`OutputFile`](#output-file)
* [`packaging.js`](./packaging.js): [`Packaging`](#packaging)
* [`sourceFile.js`](./sourceFile.js): [`SourceFile`](#source-file)




----





# Output file

* Name: `OutputFile`

Represents how an output file must be built.

Generic information is used for that:

* input: a list of source files
* output: a wanted output path
* context: the containing packaging

## Constructor parameters

1. `packaging`: see corresponding instance property
1. `logicalPath`: see corresponding instance property

## Instance properties

* `packaging`
	* interface: [`Packaging`](#packaging)
	* __required__
	* rights: access
	* Packaging which contains this output file.
* `logicalPath`
	* interface: `String`
	* __required__
	* rights: access
	* The path of the file relative to the packaging output path.
* `sourceFiles`
	* interface: `Array` of [`Source File`](#source-file)
	* default: `[]`
	* rights: access, reordering. Adding and removing elements should be done by calling prototype method `setOutputFile` of `Source File` objects (inversion of responsibility)
	* Source files to be included into this output file.
* `outputPath`
	* interface: `String`
	* initial value: `null`
	* rights: access
	* This property is updated with the complete output path just before the builder is called to build the output file. It is the result of joining the packaging's `outpoutDirectory` and this instance property `logicalPath`.
* `builder`
	* interface: `Builder`
	* default: `null` at instantiation, and then when calling the prototype method `build`: `this.packaging.createObject(this.packaging.defaultBuilder, this.builtinBuilders)`
	* rights: set, access
	* The builder to use.
* `finished`
	* interface: `Boolean`
	* initial value: `false`
	* rights: access
	* `false` until the first time the output file is built, `true` afterwards.



## Prototype attributes

* `builtinBuilders`: the [builders shipped with AT Packager](../builders), equivalent of the property `builders` in module file [`atpackager`](../atpackager)

## Prototype methods



### Logical file path match test

* Name: `isMatch`

Whether the logical path of this output file matches the given patterns.

#### Parameters

1. `patterns`
	* interface: as expected by first parameter of method `grunt.file.isMatch`.
	* __required__
	* __in__
	* The list of patterns to match against.

#### Return value

* interface: `Boolean`
* `true` if the `logicalPath` matches all the given patterns, `false` otherwise

#### Description

Uses the `grunt.file.isMatch` method.



### Build

* Name: `build`
* _No parameter_
* _No return value_

Builds the content of the output file from all the source files, with given builder (referenced by instance property `builder`), and writes this content to the disk under path corresponding to instance property `outputPath`.

Visitors' `onBeforeOutputFileBuild` methods are called on this file before the builder's build process is called, and `onAfterOutputFileBuild` methods are called after this process has been executed.

After this function has executed properly, instance property `finished` is set to `true`.

If no builder was specified and no default one could be created from the packaging, an error is logged using `grunt` and the function execution is aborted.





----





# Source file

* Name: `Source file`

Represents a source file.


## Constructor parameters

1. `packaging`: see corresponding instance property
1. `logicalPath`: see corresponding instance property



## Instance properties

* `packaging`
	* interface: [`Packaging`](#packaging)
	* __required__
	* rights: access
	* Packaging which contains this source file.
* `logicalPath`
	* interface: `String`
	* __required__
	* rights: access
	* Logical path of the file.
* `contentProvider`
	* interface: `Content Provider`, using either method `getTextContent` or `getBinaryContent`
	* default: built-in content provider `fileLoader`
	* rights: set, access
	* The content provider used to get the content corresponding to this source file.
* `outputFile`
	* interface: [`OutputFile`](#output-file)
	* rights: access. Set it using the prototype method `setOutputFile`
* `dependencies`
	* interface: `Array` of [`SourceFile`](#source-file)
	* initial value: `null`
	* rights: access
	* The source files are assured to belong to the same packaging as this source file. This property is filled and/or used by visitors.



## Prototype attributes

* `builtinBuilders`: the [builders shipped with AT Packager](../builders), equivalent of the property `builders` in module file [`atpackager`](../atpackager)



## Prototype methods



### Set the output file this source file is targeting

* Name: `setOutputFile`
* _No return value_

#### Parameters

1. `outputFile`
	* interface: [`Output File`](#output-file)
	* default: void
	* __in & out__
	* The output file (destination) this source file will contribute to.

#### Description

If the exact same output file was already set, this function won't do anything.

Otherwise, sets the instance property `outputFile` to given `outputFile`.

If the new output file is valid (non void), it will add this source file to its list of input files.

Also, if another (valid) output file had been already set before, it will remove this source file from its list of input files.



### Get the text content of the file


* Name: `getTextContent`
* __Delegated__

Delegates to instance property `contentProvider`'s `getTextContent` method, applied on this file.

#### Exceptions

* type: `Error`

If no `contentProvider` was specified.



### Get the binary content of the file


* Name: `getBinaryContent`
* __Delegated__

Delegates to instance property `contentProvider`'s `getBinaryContent` method, applied on this file.

#### Exceptions

* type: `Error`

If no `contentProvider` was specified.



### Set the text content of the file

* Name: `setTextContent`
* __Delegates__
* _No return value_

Sets the instance property `contentProvider` to built-in content provider `textContent`, and delegates to its `setTextContent` method, applied on this file (parameters are forwarded).



### Set the binary content of the file

* Name: `setBinaryContent`
* __Delegates__
* _No return value_

Sets the instance property `contentProvider` to built-in content provider `binaryContent`, and delegates to its `setBinaryContent` method, applied on this file (parameters are forwarded).



### Logical file path match test

* Name: `isMatch`

Whether the logical path of this output file matches the given patterns.

#### Parameters

1. `patterns`
	* interface: as expected by first parameter of method `grunt.file.isMatch`.
	* __required__
	* __in__
	* The list of patterns to match against.

#### Return value

* interface: `Boolean`
* `true` if the `logicalPath` matches all the given patterns, `false` otherwise

#### Description

Uses the `grunt.file.isMatch` method.



### Get dependencies

* Name: `getDependencies`
* _No parameter_

If instance property `dependencies` already contains a non-void value, returns it.

Otherwise, sets it to an empty array (`[]`), and call the visitor `computeDependencies` applied on this file in order to let it fill this array.

#### Return value

The dependencies of the file, stored or freshly computed.



### Add a dependency

* Name: `addDependency`
* _No return value_
* __Should be called only by visitors, from the `computeDependencies` method__

Adds the given source file as a dependency of this source file.

If the dependency is already present, does nothing.

#### Parameters

1. `otherSourceFile`
	* interface: [`Source file`](#source-file)
	* __required__
	* __in__
	* A source file on which this source file depends.



### Clear the content of the file

* Name: `clearContent`
* _No parameter_
* _No return value_

Clears all the content stored on this object.

#### Description

Content providers are supposed to store their content on this object, prefixing the properties with `content`. This method clears all the properties this objects contains and whose names start with `content`.





----





# Packaging

* Name: `Packaging`

Represents a packaging, with a set of source files and output files (destinations).

## Instance properties

* `sourceDirectories`
	* interface: `Array`
	* default: `[]`
	* rights: access
	* Directories where logical paths are looked for.
* `sourceFiles`
	* interface: Map of [`Source file`](#source-file)
	* default: `{}`
	* rights: access
	* The packaging's source files indexed by their logical paths.
* `outputDirectory`
	* interface: `String`
	* default: `"."` (current working directory)
	* rights: access
	* Directory in which packages are written.
* `outputFiles`
	* interface: Map of [`Output file`](#output-file)
	* default: `{}`
	* rights: access
	* The packaging's output files indexed by their logical paths.
* `outputFilesQueue`
	* interface: `Array`
	* default: `[]`
	* rights: access
	* Queue of output files to be built. Files will be built in this order. Already built files are not in this queue any more.
* `visitors`
	* interface: `Array` of Visitor
	* default: `[]`
	* rights: access. Add visitors using prototype method `addVisitors`.
	* The list of visitors this package uses.
* `defaultBuilder`
	* interface: Builder
	* default: `null`
	* rights: set, access
	* Default builder to be used by output files which don't specify any builder.



## Prototype methods

### Call visitors

* Name: `callVisitors`

Calls the given method - if defined - on all visitors used by this package (stored in instance property `visitors`), with the given arguments.

#### Parameters

1. `method`
	* interface: `String`
	* __required__
	* __in__
	* The name of the method to call.
1. `args`
	* interface: `Array`
	* __required__
	* __in__
	* The (partial) list of arguments to pass to the method.

#### Description

Note that an additional argument is prepended before the call: a reference to this packaging instance.



### Expand logical paths - to be completed

* Name: `expandLogicalPaths`

Expands the given patterns of logical paths, using `this.sourceDirectories` to find them.

#### Parameters

1. `patterns`
	* interface: `String`
	* __required__
	* __in__
	* Patterns to be expanded.
1. `onlyAlreadyAdded`
	* interface: `Boolean`
	* default: falsy
	* __in__
	* If truthy, only use already added source files.

#### Return value

The result.



### Resolve a relative path against the packaging's one

* Name: `getAbsolutePath`

#### Parameters

* `logicalPath`
	* interface: `String`
	* __required__
	* __in__
	* The relative path to resolve

#### Description

The operation resolves the given logical path against the path of the packaging, to give an absolute path. However, this is not a pure path manipulation, the result must resolve to an existing node in the file system to be considered as valid, otherwise nothing is returned.

#### Return value

The absolute path of the file if found, `null` otherwise.



### Add source files

* Name: `addSourceFiles`

#### Parameters

1. `patterns`
	* interface: same as in prototype method `expandLogicalPaths`
	* __required__
	* __in__
	* Passed to prototype method `expandLogicalPaths` to get a list of paths

#### Description

Expands the given pattern to a list of paths using the prototype method `expandLogicalPaths`, and then uses the prototype method `addSourceFile` to add each of them.

#### Return value

Returns an array containing the results to each call to the prototype method `addSourceFile`.



### Add a file

Add a file

There are actually two methods for that:

* `addSourceFile`: will add a [source file](#source-file), referring to its map `sourceFiles`
* `addOutputFile`: will add an [output file](#output-file), referring to its map `outputFiles`

#### Parameters

1. `logicalPath`
	* interface: `String`
	* __required__
	* __in__
	* The path to add as a file. It is normalized using Node.js `path` module's `normalize` method.
1. `mustCreate`
	* interface: `Boolean`
	* default: falsy
	* __in__
	* If truthy and path already present, makes the function fail.

#### Description

If file is already present in the map: does nothing except returning the corresponding file if `mustCreate` is falsy, otherwise throws an error.

Otherwise, it will create a new file, passing the given path, and add it to its map. It will eventually return this new instance, but before it will call its visitors with method `onAddSourceFile` for source files and `onAddOutputFile` for output files, passing the file instance as argument.

There is only one little difference in the case of the output file: the newly created instance is also added to the `outputFilesQueue` instance property, before the visitors are called.

#### Return value

The file corresponding to the given path, whether it was already present or freshly created.

#### Exceptions

* type: `Error`

Thrown if `mustCreate` is truthy and given path is already present.



### Get a file: source file or output file

Returns the file instance corresponding to the given path if it has been added to the packaging yet.

There are actually two methods for that:

* `getSourceFile`: to request a source file, using `sourceFiles` internally
* `getOutputFile`: to request an output file, using `outputFiles` internally

#### Parameters

1. `logicalPath`
	* interface: `String`
	* __required__
	* __in__
	* The path corresponding to the requested file. It is normalized using Node.js `path` module's `normalize` method (as when the file is added)

#### Description

Internally, it uses one of its map, the only added value being to normalize the given path to have more chance to have a valid index.

#### Return value

The file corresponding to the given path if it exists, `undefined` otherwise.



### Add a package

* Name: `addPackage`

Adds a package to this packaging.

#### Parameters

1. `packageDesc`
	* interface: `Object`, with specific properties (see description)
	* __required__
	* __in__

#### Description

The package description has the following properties:

* ...
