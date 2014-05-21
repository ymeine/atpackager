# File system layout

* [`readme.md`](./readme.md): this current documentation file

* [`ATMultipart.js`](./ATMultipart.js)
* [`Concat.js`](./Concat.js)
* [`Copy.js`](./Copy.js):
* [`JSConcat.js`](./JSConcat.js):





# Model

## Builder

A builder is a class.

Its constructor receives a configuration object.





----

Specific builders





# Concat

* Name: `Concat`

This builder creates the output file by concatenating its input files together, adding an optional header at the beginning and a footer at the end.

## Configuration

* `outputEncoding`
	* interface: `String`
	* default: `grunt.file.defaultEncoding`
	* The encoding of the output file.
* `header`
	* interface: `String`
	* _optional_
	* The header to put at the beginning of the output file.
* `footer`:
	* interface: `String`
	* _optional_
	* The footer to put at the end of the file.

## Interfaces

### Out [Frozen]

* Name: `Out`
* Instances often referenced as `out`

Out's interface is in fact the `Array`. It is just intended to be used to put together a sequence of lines that will be joined together to produce a whole content. Simply alter the array to alter this content. At that level, changes' granularity is the line.

### File

* Name: `File`

A representation of a file in the context of atpackager.

## Methods

### Build

* Name: `build`

Main entry point of the builder to actually write the given output file.

#### Input

* `outputFile`:
	* interface: `File`
	* __required__
	* The output file.

### Write header

* Name: `writeHeader`
* __Overridable__: used by the `build` method

Writes the header at the beginning of the file.

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
* __Overridable__: used by the `build` method

Writes the footer at the end of the file.

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
* __Overridable__: used by the `build` method

Writes the given source file's content into the given out stream. Once done, the source file is cleared using `clearContent`.

#### Input

* `outputFile`:
	* interface: `File`
	* __required__
	* The output file.
* `sourceFile`:
	* interface: `File`
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
* __Overridable__: used by the `build` method

Writes the content of the output file.

Uses `grunt.file.write`.

#### Input

* `outputFile`:
	* interface: `File`
	* __required__
	* The output file.
* `content`:
	* interface: `String`
	* __required__
	* The content to write into the given output file.
* `options`:
	* interface: `Object`
	* __required__?
	* Options passed directly to the underlying write method (see description).

#### Visitors

* Name: `onWriteOutputFile`
* Moment: right before the actual write operation is called
* Arguments:
	* 0: `[outputFile, {content: content, options: options}]`






## AT Multipart

* Name: `ATMultipart`

This builder creates files in the Aria Templates multipart format. Inherits `concat`.
