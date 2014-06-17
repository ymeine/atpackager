# Recap

AT Packager is built as a Grunt plugin and therefore expects it configuration to be passed using [`grunt.initConfig`](http://gruntjs.com/api/grunt.config#grunt.config.init) in the `Gruntfile.js` file.

Here is an example of such a file:

```javascript
module.exports = function (grunt) {
	grunt.file.setBase(__dirname + "...");
	grunt.loadTasks(".../atpackager/tasks");

	grunt.initConfig({
		atpackager: {
			build: {
				options: {
					// sourceDirectories,
					// outputDirectory,
					// ATBootstrapFile,
					// sourceFiles,
					// defaultBuilder,
					// packages,
					// visitors
				}
			}
		}
	});

	grunt.registerTask('default', [
		'atpackager:build'
	]);
};
```

The rest of this documentation mainly discusses the actual configuration of AT Packager, that is the `options` object you can see in the snippet of code.





# General configuration not specific to AT Packager

## Set the base directory of the project

This is the equivalent of the _current working directory_, but for Grunt.

```javascript
grunt.file.setBase("...");
```

Replace `...` with the actual path to the root of the project.

## Load AT Packager tasks

```javascript
grunt.loadTasks(".../atpackager/tasks");
```

Replace `...` with the actual path to the `atpackager` module.




# AT Packager specific configuration

Note that since it is a Grunt plugin, AT Packager uses a lot of the latter's features. Notably regarding paths for files and folders specifications: [globs](https://github.com/isaacs/node-glob) are accepted.

Note also the following terms and concepts used in this article:

* a _packaging_ is what is built by the packager from the configuration object
* the _packager_ is what builds the packaging
* a _package_ is the equivalent of an output file: a packaging is made of a set of them, as mentioned in [introduction](./user.html#the-at-packager-design)
* a _logical path_ is a path relative to the packaging (which can have several roots!)

## Options

### Input content (files and folders)

Use `sourceDirectories` to specify the packaging's root folders' paths. It is used to search for files specified with a logical path. The root folders' paths however are either absolute or relative to the base path configured for Grunt.

Then, you can use `sourceFiles` to populate the packaging with source files. These source files include both those used directly to build output files, and those used for other purposes (direct copy, dependencies computation, etc.). Logical paths are expected.

Example:

```javascript
{
	// ...
	sourceDirectories: [
		'src/main/static',
		'...'
	],
	sourceFiles: [
		'**/*.js',
		'...'
	]
	// ...
}
```

### Output directory

Use the `outputDirectory` option to tell in which directory to put the built packages. This one is relative to the base path configured for Grunt.

Example:

```javascript
{
	// ...
	outputDirectory : 'target/static'
	// ...
}
```

### Packages definition

Pass to the `packages` option a list of packages definitions: see [below](#package-definition) to know more about package definition.

You can also specify a default builder for packages, for when no explicit builder is specified for a given package. Simply put a [builder](#builders) configuration  in `defaultBuilder`.

### Visitors specifications

_Visitors are [built-in objects](#built-in-objects-definition), located in folder `visitors`_.

Use `visitors` to specify the list of visitors to be used in the packaging.

It is important to note that visitors are called in the order in which they were specified.

Please see the [dedicated section about visitors](./visitors.html) to learn more about this concept and to learn about the built-in ones.



### Aria Templates specific

___TO BE DONE___

`ATBootstrapFile`



## Built-in objects definition

Built-in objects all follow the same principle:

* they have a name: in practice it corresponds to the source file name
* they are located in a specific folder depending on their types (the folder is named after that)
* they optionally take a configuration object

Thus, there are two ways to specify the use of one of those objects:

* the short form: a simple string with the name of the specific object to be used. The packager will know its type and where to look for it depending on the context where it is used.
* the long form, whose name is put under a property `type` and whose configuration is put under a property `cfg`.

Example:

```javascript
{
	visitors: [
		{
			type: "...", // The name of the object
			cfg: { // The configuration of object
				// ...
			}
		}
	]
}
```



## Package definition

A package is a single file resulting from a building process applied on a set of input files.

Therefore a package definition is made of the following properties:

* `name`: the name of the resulting file
* `builder`: the [builder](#builders) configuration to use to create the output file from the input files. If not specified, the `defaultBuilder` specified in global AT Packager's configuration will be used.
* `files`: the list of input files to be processed by the `builder` to create the output file with given `name`

Note that this list of input `files` will possibly be extended by some specific visitors if needed.

Example of a package definition:

```javascript
{
	name: "basename.ext",
	files: [
		"...",
		// ...
	],
	builder: {
		// ...
	}
}
```

### Builders

_Builders are [built-in objects](#built-in-objects-definition), located in folder `builders`_.

Please see the [dedicated section about builders](./builders.html) to learn more about this concept and to learn about the built-in ones.
