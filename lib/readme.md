# File system layout

* [`readme.md`](./readme.md): this current documentation file
* [`atpackager.js`](./atpackager.js): module entry point. Exports:
	* Shortcuts:
		* `grunt`: the global unique grunt instance
		* `uglify`: reference to the `uglify-js` package
		* `Packaging`: [`packager/packaging`](./packager/packaging)
	* full modules (using `requireDirectory`):
		* `uglifyHelpers`: [`uglifyHelpers`](./uglifyHelpers)
		* `builders`: [`builders`](./builders)
		* `contentProviders`: [`contentProviders`](./contentProviders)
		* `visitors`: [`visitors`](./visitors)



## Sub modules

* [`builders`](./builders):
* [`contentProviders`](./contentProviders):
* [`packager`](./packager):
* [`uglifyHelpers`](./uglifyHelpers):
* [`visitors`](./visitors):

## Business

* [`main.js`](./main.js): Controls the workflow of the packager: loads plugins, calls them with the instance of atpackager once grunt has been initialized
* [`ATGetDependencies.js`](./ATGetDependencies.js): returns the list of dependencies of an Aria Templates file (dependencies are expressed as logical paths to the required classes)
* [`ATInPackaging.js`](./ATInPackaging.js): ensures an Aria Templates context (instance?) is created for the given packaging object. returns this context.
* [`ATRemoveDoc.js`](./ATRemoveDoc.js): removes some content from the source code, related to pure documentation or development purposes

## Adapters

* [`ariatemplates.js`](./ariatemplates.js): adapter to make the local installation of Aria Templates work (in the context of being out of a browser)
* [`grunt.js`](./grunt.js): implements a singleton pattern for a grunt instance. One can gives the unique instance whenever it wants, and then access it. Functions using this instance can also register, being called immediately if the instance is already available, or later on (in order) as soon as this instance gets available.

## Utilities

* [`requireDirectory.js`](./requireDirectory.js): requires all JavaScript files in a given folder, and returns a map with the created modules. Indexes are file names without the `.js` extension, entries are the modules.
* [`findFile.js`](./findFile.js): searches for a file inside a given list of folders. Returns its absolute path if found, `null` otherwise.
