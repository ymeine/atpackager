Built-in builders.

# File system layout

* [`readme.md`](./readme.md): this current documentation file

General purpose specific builders:

* [`Concat.js`](./Concat.js): [Concatenate files](#concatenate-files)
* [`Copy.js`](./Copy.js): [Copy files](#copy-files)

JavaScript specific builders:

* [`JSConcat.js`](./JSConcat.js): [Concatenate JavaScript files](#concatenate-javascript-files)

Aria Templates specific builders:

* [`ATMultipart.js`](./ATMultipart.js): [Create an Aria Templates multi-part file](#create-an-aria-templates-multi-part-file)



----

# Introduction: what is a builder?

A builder is in charge to create the content of an output file (normally using its source files), store it in memory, and write it on the storage device.





# Some concepts

_Before going further into details, here are a few concepts to know, in order to understand some features, and also to avoid repeating things in the documentation, making it less digestible._

All builders expect a configuration object as the unique argument of their constructors; they are described in this documentation for each specific builder.

Note that the configuration object is not altered, its properties are simply used.





# Interface of a builder

## Methods



### Build

* Name: `build`

Main entry point of the builder to generate the content of the given output file.

__All builders implement this method.__

#### Parameters

1. `outputFile`
	* interface: `Output File`
	* __required__
	* __in & out__
	* The output file to build.





----

General purpose specific builders

----





# Concatenate files

* Name: `Concat`

Creates the output content by concatenating its input files' contents together, optionally adding a header at the beginning and a footer at the end.



## Configuration

* `outputEncoding`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: `grunt.file.defaultEncoding`
	* The encoding of the output file.
* `header`
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: [`undefined`](http://devdocs.io/javascript/global_objects/undefined)
	* The header to put at the beginning of the output file.
* `footer`:
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* default: [`undefined`](http://devdocs.io/javascript/global_objects/undefined)
	* The footer to put at the end of the file.



## Interfaces

### Out

* Name: `Out`
* Instances often referenced as `out`
* Often called _output_, or _out stream_

Out's interface is in fact the `Array`. It is just intended to be used to put together a sequence of lines that will be joined together to produce a whole content. Simply alter the array to alter this content. At that level, changes' granularity is the line.



## Protected methods

Those methods are used from the `build` method, and can be overridden to create a new builder based on this one.



### Write header

* Name: `writeHeader`

Writes the header content at the beginning of the output.

#### Input

* `outputFile`:
	* interface: `File`
	* __required__
	* The output file.
* `out`:
	* interface: `Out`
	* __required__



### Write footer

* Name: `writeFooter`

Writes the footer content at the end of the output.

#### Input

* `outputFile`:
	* interface: `File`
	* __required__
	* The output file.
* `out`:
	* interface: `Out`
	* __required__



### Write input file

* Name: `writeInputFile`

Writes the given source file's content into the given output. Once done, the source file is cleared using `clearContent`.

#### Parameters

* `outputFile`:
	* interface: `Output File`
	* __required__
	* The output file.
* `sourceFile`:
	* interface: `Source File`
	* __required__
	* The source file.
* `out`:
	* interface: `Out`
	* __required__

#### Visitor

* Name: `onWriteInputFile`
* Moment: very beginning of the method
* Arguments:
	* 0: `[outputFile, sourceFile]`



### Write output file

* Name: `writeOutputFile`

Actually writes the content of the output file on the disk.

Uses [`grunt.file.write`](http://gruntjs.com/api/grunt.file#grunt.file.write).

#### Input

* `outputFile`:
	* interface: `Output File`
	* __required__
	* The output file (used for its description).
* `content`:
	* interface: [`String`](http://devdocs.io/javascript/global_objects/string)
	* __required__
	* The content to write into the given output file.
* `options`:
	* interface: [`Object`](http://devdocs.io/javascript/global_objects/object)
	* __required__
	* Options passed directly to the underlying write method.

#### Visitors

* Name: `onWriteOutputFile`
* Moment: right before the actual write operation is called
* Arguments:
	* 0: `[outputFile, {content: content, options: options}]`
* It can be used to alter the content to be written and/or the output options





# Copy files

* Name: `Copy`



## AT Multipart

* Name: `ATMultipart`

This builder creates files in the Aria Templates multipart format. Inherits `concat`.
