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
* context: the containing packaging.



## Constructor parameters

1. `packaging`
	* interface: [`Packaging`](#packaging)
	* __required__
	* Packaging which contains this output file.
1. `logicalPath`
	* interface: `String`
	* __required__
	* Logical path of the file.



## Instance properties

* `packaging`
	* ___from constructor___
	* rights: access
* `logicalPath`
	* ___from constructor___
	* rights: access
* `sourceFiles`
	* interface: `Array`
	* rights: access, reordering. Adding and removing elements should be done through `setOutputFile`.
	* Source files to be included into this output file.
* `outputPath`
	* interface: `String`
	* rights: access
	* This property is updated with the complete output path just before the builder is called to build the output file.
* `builder`
	* interface: `Builder`
	* rights: set, access
	* default (on `build`): `this.packaging.createObject(this.packaging.defaultBuilder, this.builtinBuilders)`
	* The builder to use.
* `finished`
	* interface: `Boolean`
	* rights: access
	* `false` until the first time the output file is built, `true` afterwards.



## Prototype attributes



## Prototype methods



### Match?

* Name: `isMatch`

Whether the logical path of this output file matches the given patterns.

#### Parameters

1. `patterns`
	* interface: `Array`. Content: `String`?
	* __required__
	* The list of patterns to match against.

#### Return value

* interface: `Boolean`
* `true` if the `logicalPath` matches all the given patterns, `false` otherwise



### Build

* Name: `build`

#### Parameters

_None_





----





# Packaging

* Name: `Packaging`

Represents a packaging, with a set of source files and destination file.

## Instance properties

* `sourceDirectories`
	* interface: `Array`
	* default: `[]`
	* rights: access
	* Directories where logical paths are looked for.
* `sourceFiles`
	* interface: Map
	* default: `{}`
	* rights: access
	* Map with information about each source file. The key in the map is the logical path of the file.
* `outputDirectory`
	* interface: `String`
	* default: `"."`
	* rights: access
	* Directory in which packages are written.
* `outputFiles`
	* interface: Map
	* default: `{}`
	* rights: access
	* Map with information about each output file. The key in the map is the name of the output file, relative to outputDirectory.
* `outputFilesQueue`
	* interface: `Array`
	* deault: `[]`
	* rights: access
	* Queue of output files to be built. Files will be built in this order. Already built files are not in this queue any more.
* `visitors`
	* interface: `Array` of Visitor
	* default: `[]`
	* rights:
	*
* `defaultBuilder`
	* interface: Builder
	* default: `null`
	* rights: access
	* Default builder.

## Prototype methods

### Call visitors

* Name: `call`

Calls the given method - if defined - on all `this.visitors` with the given parameters.

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



### Expand logical paths ??

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




----





# Source file

* Name: `Source file`



## Constructor parameters

1. `packaging`
	* interface: [`Packaging`](#packaging)
	* __required__
	* Packaging which contains this output file.
1. `logicalPath`
	* interface: `String`
	* __required__
	* Logical path of the file.



## Instance properties

* `packaging`
	* ___from constructor___
	* rights: access
* `logicalPath`
	* ___from constructor___
	* rights: access
* `contentProvider`
	* interface: `Array`
	* rights: access, reordering. Adding and removing elements should be done through `setOutputFile`.
* `outputFile`
	* interface: [`OutputFile`](#output-file)
	* rights: access. Set: `setOutputFile`
* `dependencies`
	* interface: `Array`. Content: [`SourceFile`](#source-file), coming from the same `packaging` as the instance.
	* rights: access


