# File system layout

* [`readme.md`](./readme.md): this current documentation file

Classes:

* [`outputFile.js`](./outputFile.js): [`OutputFile`](#output-file)
* [`sourceFile.js`](./sourceFile.js): [`SourceFile`](#source-file)
* [`packaging.js`](./packaging.js): [`Packaging`](#packaging)




----





# Output file

* Name: `OutputFile`

Represents how an output file must be built.

Generic information is used for that:

* input: a list of source files
* output: a wanted output path
* context: the containing packaging

## Constructor parameters

1. `packaging`: see corresponding [instance property](#instance-properties)
1. `logicalPath`: see corresponding [instance property](#instance-properties)

## Instance properties

* `packaging`
	* interface: [`Packaging`](#packaging)
	* __required__
	* rights: access
	* Packaging which contains this output file.
* `logicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* rights: access
	* The path of the file relative to the packaging output path.
* `sourceFiles`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array) of [`Source File`](#source-file)
	* default: `[]`
	* rights: access, reordering. Adding and removing elements should be done by calling prototype method [`setOutputFile`](#set-the-output-file-this-source-file-is-targeting) of [`Source File`](#source-file) objects (this is an inversion of responsibility).
	* Source files to be included into this output file.
* `outputPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* initial value: [`null`](http://devdocs.io/javascript/global_objects/null)
	* rights: access
	* This property is updated with the complete output path just before the builder is called to build the output file. It is the result of joining the [packaging's `outpoutDirectory` property](#instance-properties_2) and the [instance property]((#instance-properties)) `logicalPath`.
* `builder`
	* interface: `Builder`
	* default: [`null`](http://devdocs.io/javascript/global_objects/null) at instantiation, and then when calling the prototype method [`build`](#build): `this.packaging.createObject(this.packaging.defaultBuilder, this.builtinBuilders)`
	* rights: set, access
	* The builder to use.
* `finished`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* initial value: [`false`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript)
	* rights: access
	* [`false`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) until the first time the output file is [built](#build), [`true`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) afterwards.



## Prototype attributes

* `builtinBuilders`: collection of the [builders shipped with AT Packager](../builders), equivalent of the property `builders` in module file [`atpackager`](../atpackager.js)

## Prototype methods



### Logical file path match test

* Name: `isMatch`

Whether the logical path of this output file matches the given patterns.

#### Parameters

1. `patterns`
	* interface: as expected by first parameter of method [`grunt.file.isMatch`](http://gruntjs.com/api/grunt.file#grunt.file.ismatch).
	* __required__
	* __in__
	* The list of patterns to match.

#### Return value

* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
* [`true`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) if the [`logicalPath`]((#instance-properties)) matches all the given `patterns`, [`false`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) otherwise

#### Description

Uses the [`grunt.file.isMatch`](http://gruntjs.com/api/grunt.file#grunt.file.ismatch) method to test individual matches.



### Build

* Name: `build`
* _No parameter_
* _No return value_

Builds the content of the output file from all the source files (referenced by instance property [`sourceFiles`](#instance-properties)), using the builder (referenced by instance property [`builder`](#instance-properties)), and writes this content to the disk under path specified by instance property [`outputPath`](#instance-properties).

Visitors' `onBeforeOutputFileBuild` methods are called on this file before the builder's build process is called, and `onAfterOutputFileBuild` methods are called after this process has been completed.

After this function has executed properly, instance property [`finished`](#instance-properties) is set to [`true`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript).

If no builder was specified and no default one could be created from the packaging, [an error is logged using `grunt`](http://gruntjs.com/api/grunt.log#grunt.log.error-grunt.verbose.error) and the function execution is aborted.





----





# Source file

* Name: `Source file`

Represents a source file.


## Constructor parameters

1. `packaging`: see corresponding [instance property](#instance-properties_1)
1. `logicalPath`: see corresponding [instance property](#instance-properties_1)



## Instance properties

* `packaging`
	* interface: [`Packaging`](#packaging)
	* __required__
	* rights: access
	* Packaging which contains this source file.
* `logicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* rights: access
	* Logical path of the file.
* `contentProvider`
	* interface: `Content Provider`
	* default: built-in content provider `fileLoader`
	* rights: set, access
	* The content provider used to get the content corresponding to this source file.
* `outputFile`
	* interface: [`OutputFile`](#output-file)
	* initial value: [`null`](http://devdocs.io/javascript/global_objects/null)
	* rights: access. Set it using the prototype method [`setOutputFile`](#set-the-output-file-this-source-file-is-targeting).
	* The output file this source file is targeting.
* `dependencies`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array) of [`SourceFile`](#source-file)
	* initial value: [`null`](http://devdocs.io/javascript/global_objects/null)
	* rights: access
	* Source files on which this current source file depends. They are assured to belong to the same packaging as this source file. This property is filled and/or used by visitors.



## Prototype attributes

* `builtinBuilders`: the [builders shipped with AT Packager](../builders), equivalent of the property `builders` in module file [`atpackager`](../atpackager.js)



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

Otherwise, sets the instance property [`outputFile`](#instance-properties_1) to given `outputFile`.

If the new output file is valid (non void), it will add this source file to its list of input files.

Also, if another (valid) output file had been already set before, it will remove this source file from its list of input files.



### Get the content of the file

There are actually two methods for that:

* `getTextContent`: for text content
* `getBinaryContent`: for binary content
* __Delegated__

Delegates to instance property [`contentProvider`](#instance-properties_1)'s `getTextContent` method for text content and `getBinaryContent` method for binary content, both applied on this file.

#### Exceptions

* type: `Error`

If no [`contentProvider`](#instance-properties_1) was specified.



### Set the content of the file

There are actually two methods for that:

* `setTextContent`: for text content
* `setBinaryContent`: for binary content
* __Delegates__
* _No return value_

Sets the instance property [`contentProvider`](#instance-properties_1) to a built-in content provider and delegates to one of its method. For text content, it uses the `textContent` content provider and its method `setTextContent`. For binary content, it uses the `binaryContent` content provider and its method `setBinaryContent`. In both cases, the method is applied on this file and parameters are forwarded.



### Logical file path match test

* Name: `isMatch`

Whether the logical path of this output file matches the given patterns.

#### Parameters

1. `patterns`
	* interface: as expected by first parameter of method [`grunt.file.isMatch`](http://gruntjs.com/api/grunt.file#grunt.file.ismatch).
	* __required__
	* __in__
	* The list of patterns to match against.

#### Return value

* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
* [`true`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) if the `logicalPath` matches all the given patterns, [`false`](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) otherwise

#### Description

Uses the [`grunt.file.isMatch`](http://gruntjs.com/api/grunt.file#grunt.file.ismatch) method.



### Get dependencies

* Name: `getDependencies`
* _No parameter_

If instance property [`dependencies`](#instance-properties_1) already contains a non-void value, returns it.

Otherwise, sets it to an empty array (`[]`), and calls the packaging's visitors' `computeDependencies` method applied on this file in order to let them fill this array.

#### Return value

The dependencies of the file, stored or freshly computed.



### Add a dependency

* Name: `addDependency`
* _No return value_
* __Should be called only by visitors, from their `computeDependencies` method__

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

Content providers are supposed to store their associated content on this object, prefixing the names of their properties with the text `content`. This method clears all the properties this object contains and whose names start with `content`.





----





# Packaging

* Name: `Packaging`

Represents a packaging, with a set of source files (inputs) and output files (destinations).

## Some concepts

### Logical path

We name _logical path_ a path internal/relative to the packaging.

### Path normalization

Unless specified, the paths given to method for any kind of file are normalized using [Node.js `path` module's `normalize` method](http://devdocs.io/node/path#path_path_normalize_p).

This assures that two equivalent paths finally resolving to a same one are actually considered the same.



## Instance properties

* `sourceDirectories`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array)
	* default: `[]`
	* rights: access
	* Directories where logical paths are looked for.
* `sourceFiles`
	* interface: Map of [`Source file`](#source-file)
	* default: `{}`
	* rights: access
	* The packaging's source files indexed by their logical paths.
* `outputDirectory`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: `"."` (current working directory)
	* rights: access
	* Directory in which packages are written.
* `outputFiles`
	* interface: Map of [`Output file`](#output-file)
	* default: `{}`
	* rights: access
	* The packaging's output files indexed by their logical paths.
* `outputFilesQueue`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array)
	* default: `[]`
	* rights: access
	* Queue of output files to be built. Files will be built in this order, and removed from this queue once built.
* `visitors`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array) of `Visitor`
	* default: `[]`
	* rights: access. Add visitors using prototype method `addVisitors`.
	* The list of visitors this package uses.
* `defaultBuilder`
	* interface: Builder
	* default: [`null`](http://devdocs.io/javascript/global_objects/null)
	* rights: set, access
	* Default builder to be used by output files which don't specify any builder.



## Prototype methods

### Call visitors

* Name: `callVisitors`

Calls the given method - if defined - on all visitors used by this package (stored in instance property [`visitors`](#instance-properties_2)), with the given arguments.

#### Parameters

1. `method`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* __in__
	* The name of the method to call.
1. `args`
	* interface: [`Array`](http://devdocs.io/javascript/global_objects/array)
	* __required__
	* __in__
	* The (partial) list of arguments to pass to the method.

#### Description

Arguments are expanded by using [`apply`](http://devdocs.io/javascript/global_objects/function/apply). Note that an additional argument is prepended before the call: a reference to this packaging instance.

So it more or less corresponds to the following code (pseudo-code: `in` loops on an array, `++` concatenates two arrays, `this` is the package instance):

```livescript
for visitor in this.visitors => visitor[method].apply(visitor, [this] ++ args)
```



### Expand logical paths

* Name: `expandLogicalPaths`

Expands the given patterns of logical paths, using instance property `sourceDirectories` to find them.

#### Parameters

1. `patterns`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* __in__
	* Patterns to be expanded.
1. `onlyAlreadyAdded`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: [falsy](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript)
	* __in__
	* If [truthy](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript), only use already added source files.

#### Return value

The result.



### Resolve a relative path against the packaging's one

* Name: `getAbsolutePath`

#### Parameters

* `logicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* __in__
	* The relative path to resolve.

#### Description

The operation resolves the given logical path against the path of the packaging, to give an absolute path. However, this is not a pure path manipulation, the result must resolve to an existing node in the file system to be considered as valid, otherwise nothing is returned.

#### Return value

The absolute path of the file if found, [`null`](http://devdocs.io/javascript/global_objects/null) otherwise.



### Add source files

* Name: `addSourceFiles`

#### Parameters

1. `patterns`
	* interface: same as in prototype method `expandLogicalPaths`
	* __required__
	* __in__
	* Passed to prototype method `expandLogicalPaths` to get a list of paths

#### Description

Expands the given pattern to a list of paths using the prototype method [`expandLogicalPaths`](#expand-logical-paths), and then uses the prototype method [`addSourceFile`](#add-a-file) to add each of them.

#### Return value

Returns an array containing the results to each call to the prototype method [`addSourceFile`](#add-a-file).



### Add a file

Adds either an input or an output file.

There are actually two methods for that:

* `addSourceFile`: will add a [source file](#source-file), referring to its map `sourceFiles`
* `addOutputFile`: will add an [output file](#output-file), referring to its map `outputFiles`

#### Parameters

1. `logicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* __in__
	* The path to add as a file. It is normalized using Node.js `path` module's `normalize` method.
1. `mustCreate`
	* interface: [`Boolean`](http://devdocs.io/javascript/global_objects/boolean)
	* default: [falsy](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript)
	* __in__
	* If [truthy](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) and path already present, makes the function fail.

#### Description

If file is already present in the map: does nothing except returning the corresponding file if `mustCreate` is [falsy](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript), otherwise throws an error.

Otherwise, it will create a new file, passing the given path, and add it to its map. It will eventually return this new instance, but before it will call its visitors with method `onAddSourceFile` for source files and `onAddOutputFile` for output files, passing the file instance as argument.

There is only one little difference in the case of the output file: the newly created instance is also added to the `outputFilesQueue` instance property, before the visitors are called.

#### Return value

The file corresponding to the given path, whether it was already present or freshly created.

#### Exceptions

* type: `Error`

Thrown if `mustCreate` is [truthy](https://en.wikipedia.org/wiki/Boolean_data_type#Python.2C_Ruby.2C_and_JavaScript) and given path is already present.



### Get a file

Returns the file instance corresponding to the given path if it has been added to the packaging yet.

There are actually two methods for that:

* `getSourceFile`: to request a source file, using [`sourceFiles`](#instance-properties_2) internally
* `getOutputFile`: to request an output file, using [`outputFiles`](#instance-properties_2) internally

#### Parameters

1. `logicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* __in__
	* The path corresponding to the requested file.

#### Description

Internally, it uses one of its map, the only added value being that the given path is normalized to ensure to get the proper entry.

#### Return value

The file corresponding to the given path if it exists, [`undefined`](http://devdocs.io/javascript/global_objects/undefined) otherwise.



### Add a package

* Name: `addPackage`

Adds a package to this packaging.

#### Parameters

1. `packageDesc`
	* interface: [`Object`](http://devdocs.io/javascript/global_objects/object), with specific properties (see description)
	* __required__
	* __in__

#### Description

The package description has the following properties:

* ...



### Rename an output file

* Name: `renameOutputFile`

Renames the specified output file from this packaging.

#### Parameters

1. `oldLogicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* __in__
	* The logical path of the source file, as it is currently set.
1. `newLogicalPath`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* __in__
	* the new, desired logical path for the source file corresponding to the given `oldLogicalPath`.

#### Description

First note that nothing is done if the new path doesn't differ from the old one.

Otherwise, it logically renames the file, setting its instance property `logicalPath` with `newLogicalPath`. In addition to that, it checks if the file exists on storage device (using [Node.js `fs.existsSync` method](http://devdocs.io/node/fs#fs_fs_existssync_path)), and if so renames it there too. This process updates the file's instance property `outputPath`.

#### Return value

* interface: [Output file](#output-file)

The renamed file.

#### Exceptions

* type: `Error`

If the original source file could not be found.



### Initialize

* Name: `init`
* _No parameter_
* _No return value_

Calls the visitors' `onInit` method (without any argument). This method should be called before adding any source file or package and after adding all visitors.



### Build the package

* Name: `build`
* _No parameter_
* _No return value_


