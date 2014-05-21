# File system layout

* [`readme.md`](./readme.md): this current documentation file

* [`outputFile.js`](./outputFile.js): class [`OutputFile`](#output-file)
* [`packaging.js`](./packaging.js): class [`Packaging`](#packaging)
* [`sourceFile.js`](./sourceFile.js): class [`SourceFile`](#source-file)




----





# Output file

* Name: `OutputFile`

Represents how an output file must be built. The three general things are fulfilled: input, output and context, respectively with a list of source files, a wanted output path, and the containing packaging.



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

## Constructor parameters





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


