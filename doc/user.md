# Introduction: what is AT Packager?

AT Packager is a tool to package your products, of any kind, since its basic features process files and folders.

However, it already embeds some components which can handle specific types of content, such as JavaScript, or Aria Templates, at both file level and whole product level.

> What is packaging in a word?

The idea of packaging is to take a bunch of source files (code, documentation, resources, whatever), and to apply some transformations to their content and to their layout (it is possible to concatenate files, move them, etc.), in order to deliver the product in a tailored form, usually for production.

> What's the main concept of AT Packager regarding that?

AT Packager treats the set of source files as a tree, and then traverses it, applying a set of transformations to each node (understand each file and folder). The transformations are applied using the concept of visitors: a visitor is called for each node, and depending on its configuration, it will do something or not on it. There can be several visitors.

> Other remarks?

Yes, AT Packager is built for [node.js](http://nodejs.org/), as a [Grunt](http://gruntjs.com/) plugin.





# Download

## Download using npm

_[atpackager](https://www.npmjs.org/package/atpackager) on [npm](https://www.npmjs.org/)_

This is the easiest way, and we assume you have a basic knowledge of what npm is.

To install it inside your project, execute this:

```bash
npm install atpackager
```

## Download using Maven

AT Packager is also available as a Maven artifact. Its name is `aria-templates-build-dependencies` (`artifactId`) and it is a `zip` (`type`) file.

Here is a sample plugin section you can tweak and add to your `pom.xml` configuration file in order to download this artifact and extract it to the `target/at-packaging` folder automatically during the build:

```xml
<project>
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<version>2.6</version>
				<executions>
					<execution>
						<id>unpack-atpackager</id>
						<phase>initialize</phase>
						<goals>
							<goal>unpack</goal>
						</goals>
						<configuration>
							<artifactItems>
								<artifactItem>
									<groupId>com.ariatemplates</groupId>
									<artifactId>aria-templates-build-dependencies</artifactId>
									<version>1.0.0</version>
									<type>zip</type>
								</artifactItem>
							</artifactItems>
							<outputDirectory>target/at-packaging</outputDirectory>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
</project>
```





# Configure

AT Packager is a tool, and it needs some instructions to do something, like: where to take files from, what to do with them, etc.

In general, there are several ways to pass such information to a tool, depending on its interface: command line arguments, "standard" configuration files, etc.

In the case of AT Packager, since the latter is built as a Grunt plugin, this information will be passed as for other Grunt plugins: using a configuration object in the `Gruntfile.js` file. For more information about this file, you can have a look at [the Grunt website](http://gruntjs.com/sample-gruntfile).

For a full description of the configuration object of AT PAckager, please refer to the [corresponding section](./configuration.html).





# Execute

To execute the packager and actually build the package, you will need to invoke grunt with the `Gruntfile` containing the configuration as discussed [above](./configuration).

There are several ways to do this, please refer to the [Grunt website](http://gruntjs.com/getting-started) to learn about _standard_ ways. However, we quickly recap them anyway below.

## Using the CLI program

If the Grunt CLI is globally installed (`npm install -g grunt-cli`), you can directly use it in the same folder where the `Gruntfile.js` file is: executing `grunt` will run the default task.

Note that it is still necessary to have the grunt package installed inside your project, `grunt-cli` being only the command line interface to the former.

## Using the CLI function in Grunt directly

In our example below, we assume two things:

* the piece of JavaScript code is executed in the same folder where the `Gruntfile.js` file is
* `grunt` can be required with an absolute id (no relative path)

```javascript
var grunt = require("grunt");

grunt.cli({
	gruntfile : __dirname + "/Gruntfile.js"
});
```

The list of available options can be seen in the source file [grunt/lib/grunt/cli.js at master on gruntjs/grunt repository](https://github.com/gruntjs/grunt/blob/master/lib/grunt/cli.js).

## Using Maven

The technique with Maven is to store the piece of JavaScript code shown in previous section in a file put in the same folder as the `Gruntfile.js` file, and then execute it using a specific Maven plugin.

Note that in this context, the `grunt` module might not be available as a global module, so you should adapt the path passed to `require` to point to teh location where the module is actually installed (something like: `"../../../target/at-packaging/grunt"`).

Here is the sample plugin section your can tweak and add to your `pom.xml` file to achieve this:

```xml
<project>
	<build>
		<plugins>
			<plugin>
				<groupId>com.ariatemplates.attester</groupId>
				<artifactId>attester-maven-plugin</artifactId>
				<version>1.0.0</version>
				<executions>
					<execution>
						<id>at-packager</id>
						<goals>
							<goal>node</goal>
						</goals>
						<phase>compile</phase>
						<configuration>
							<arguments>
								<arg>src/build/at-packaging/build.js</arg>
								<arg>--ariatemplates-version</arg>
								<arg>${at.version}</arg>
							</arguments>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
</project>
```

The `attester-maven-plugin` automatically downloads node.js from the maven repository and runs it with the provided arguments.

Note that more arguments can be passed in `arguments`, they will notably be available in the Grunt configuration file by using the `grunt.option("...")` utility method.
