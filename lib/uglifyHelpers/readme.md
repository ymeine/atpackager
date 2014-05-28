# File system layout

* [`readme.md`](./readme.md): this current documentation file

* [`astToString.js`](./astToString.js):
* [`cloneNode.js`](./cloneNode.js):
* [`getExpression.js`](./getExpression.js):
* [`jsToAST.js`](./jsToAST.js):
* [`replaceNode.js`](./replaceNode.js):
* [`setJSONPropertyInAST.js`](./setJSONPropertyInAST.js):
* [`wrapCode.js`](./wrapCode.js):





# Introduction

As the package's name stands, here is a set of helpers for UglifyJs. They aim at simplifying the use of features provided by the latter.


----


# Convert an AST to a string

After applying the options, generates a string from the given AST using the method `print_to_string`.

## Parameters

1. `ast`
	* interface: UglifyJS AST
	* __required__
	* The AST to use to generate a string representation.
1. `outputOptions`
	* interface: `Object`, see [below](#options) for a list of available options
	* default: `{}`
	* The options to customize the output string.

## Options

* `comments`
	* interface: `Boolean`
	* default: falsy
	* If truthy, removes the following (it it exists) prior any serialization: `node.start._comments_dumped`, `node.end._comments_dumped`






# Clone a node

Clones the given node's associated subtree, and return the latter's root.

## Parameters

1. `node`
	* interface: UglifyJS Node
	* __required__
	* The node to clone.





# Get a possible expression from a node

Tries to return an expression node inside the given node, and returns it if found, otherwise throws an exception.

## Parameters

1. `node`
	* interface: UglifyJS Node
	* __required__
	* The node to extract the expression from.

## Description

The node can be of two possible types:

* `UglifyJS.AST_Toplevel`: the expression is searched inside the first element of its `body` (using our own function), if it exists
* `UglifyJS.AST_SimpleStatement`: the expression is its `body`





# Generate an AST from a JS Object: why???

Generates an AST from the given object, throwing an exception if it doesn't know how to handle its type.

## Description

The way to generate an AST depends on the object's type. The latter is checked using the empiric method of applying the native `Object`'s `toString` implementation on the object, so you can expect having an implementation for the following types:

* `Array`
* `RegExp`
* `Date`
* `String`
* `Number`
* `Undefined`
* `Boolean`
* `Null`
* `Object`
* `Function`

For any other type, an exception is thrown.





# Replace/remove a child node in a given parent

## Parameters

1. `node`
	* interface: UglifyJS Node
	* __required__
	* The node to replace.
1. `parent`
	* interface: UglifyJS Node
	* __required__
	* The parent node containing the node to replace and that will therefore receive the new node.
1. `newNode`
	* interface: UglifyJS Node
	* default: void
	* The node to use to replace the given node inside the given parent. If void, will delete the node instead.

## Description

It handles nodes stored directly as properties of the parent, or in an array.





# Set an object's property's value directly in its AST representation

## Parameters

1. `node`
	* interface: UglifyJS Node
	* __required__
	* __in & out__
	* The root node from which the property is accessed, directly or by traversing some subobjects.
1. `path`
	* interface: `String` or `Array`
	* __required__
	* __in__
	* The path of the property. It can be given as an array of path portions (a sequence of property names), or as a string containing those portions separated by a single dot `.`.
1. `value`
	* interface: can be anything
	* default: `undefined`
	* __in__
	* The value to use to replace the property's one.

## Description

Here, _setting_ means actually making the property exist with the given value, whether it was existing before with a different one, or if it has to be created.

It will create a set of objects if needed along the property's path, possibly replacing some existing values if they were not objects before.

## Example

```javascript
object = {
	one: 1
};
setJSONPropertyInAST(object, "one.two.three"):
equivalent = {
	one: {
		two: {
			three: undefined
		}
	}
}
```





# Wrap code

## Parameters

1. `wrapper`
	* interface: `String`
	* __required__
	* Source code used to wrap the given statements.
1. `statements`
	* interface: `UglifyJS.AST_Node` or `Array`
	* __required__
	* ???

## Description

???
