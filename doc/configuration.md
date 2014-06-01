# Recap

AT Packager is built as a Grunt plugin and therefore expects it configuration to be passed using `grunt.initConfig` in the `Gruntfile.js` file.

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





# General configuration not specific to AT Packager

## Set the base directory of the project

This is the equivalent of the _current working directory_, but for Grunt.

```javascript
grunt.file.setBase("...")
```

Replace `...` with the actual path to the root of the project.

## Load AT Packager tasks

```javascript
grunt.loadTasks(".../atpackager/tasks")
```

Replace `...` with the actual path to the `atpackager` module.




# AT Packager specific configuration

(to be put inside the `grunt.initConfig` method as shown is sample above)

Note that since it is a Grunt plugin, AT Packager uses a lot of the latter's features. Notably regarding paths for files and folders specifications: [globs](https://github.com/isaacs/node-glob) are accepted.

## Options

### Specify the input content (files and folders)

Use `sourceDirectories` to specify a list of root folders on which the packager will work.

Then, `sourceFiles` will specify the list of files to process. They are relative from the given source directories.

### Specify the output directory

The `outputDirectory` option tells the directory in which to put the final result.

### Packages definition

Pass to the `packages` option a list of packages definitions: see [below](#package-definition) to know more about package definition.

You can also specify a default builders for those packages definitions, when no explicit builder is specified there. Simply put a builder configuration  in `defaultBuilder`. Once again, see [below](#package-definition) for more information on how a builder configuration looks like.

### Visitors specifications

`visitors` is used to specify the list of visitors to be used.

All visitors will be applied in order to each resolved source file.

In this list, you can specify a visitor either is its short form - its name - or a full configuration object, of this form:

```
{
	type: "..." // The name of the visitor (as in the short form),
	cfg: { // The configuration of the visitor
		// ...
	}
}
```

Please see the [dedicated section about visitors](./visitors.html) to learn more about this concept and to know which are available.



### Aria Templates specific

___TO BE DONE___

* ATBootstrapFile



## Package definition

A package is a single file resulting from a building process applied on a set of input files.

Therefore a package definition is made of the following properties:

* `name`: the name of the resulting file
* `builder`: the builder to use, along with its configuration, to create the output file from the input files. If not specified, the `defaultBuilder` specified in global AT Packager's configuration will be used.
* `files`: the list of input files processed by the `builder` to create the output file with given `name`

Note that this list of input files can be extended by some specific visitors if needed. For instance, for Aria Templates specific products, using the visitors `ATDependencies` and `CheckDependencies` will compute static dependencies for each file, adding them in current package definition if not already present in any other package definition.

Now, here is how to configure a builder (it follows the same principle as for visitors):

```javascript
{
	type: "...", // The name of the builder
	cfg: { // The configuration of the builder
		// ...
	}
}
```


Please see the [dedicated section about builders](./builders.html) to learn more about this concept and to know which are available.
