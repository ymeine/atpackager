# File system layout

* [`readme.md`](./readme.md): this current documentation file

* [`ATCompileTemplates.js`](./ATCompileTemplates.js):
* [`ATDependencies.js`](./ATDependencies.js):
* [`ATNormalizeSkin.js`](./ATNormalizeSkin.js):
* [`ATRemoveDoc.js`](./ATRemoveDoc.js):
* [`ATUrlMap.js`](./ATUrlMap.js):
* [`CheckDependencies.js`](./CheckDependencies.js):
* [`CheckGlobals.js`](./CheckGlobals.js):
* [`CheckPackaged.js`](./CheckPackaged.js):
* [`CopyUnpackaged.js`](./CopyUnpackaged.js):
* [`Hash.js`](./Hash.js):
* [`ImportSourceFile.js`](./ImportSourceFile.js):
* [`ImportSourceFiles.js`](./ImportSourceFiles.js):
* [`JSMinify.js`](./JSMinify.js):
* [`JSStripBanner.js`](./JSStripBanner.js):
* [`Map.js`](./Map.js):
* [`TextReplace.js`](./TextReplace.js):



----

# Visitor

__To be completely checked__

A visitor is used when a JavaScript AST is traversed.

It can implement a set of methods, considered as hooks, called - if defined - when a node is being traversed.

Hooks:

* `computeDependencies`?
* `onInit`
* `onWriteInputFile`
* `onWriteJSOutputFile`
* `onBeforeOutputFileBuild`
* `onAfterOutputFileBuild`
* `onReachingBuildEnd`
* `onAfterBuild`


# Specific visitors

See links on top of the page.
