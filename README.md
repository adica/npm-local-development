# Working locally with multiple NPM packages from GitHub

Recently, I've been asked to split a very big client side project into small chunks of code. The purpose was to isolate sections of the app, to make the code chunks smaller and more readable, and to enable scaling the development process by letting many developers working simoulatnously on separated sections of the code.

The way our packages are designed is that every package exports it's isolated code into a `dist` folder (`app.js` and `app.css` files), and the main orchestrator package uses those files to build the web page.

```
|───package-a
|   |
|   └───dist
|       | app.js
|       | app.css
|
|───package-b
|   |
|   └───dist
|       | app.js
|       | app.css
|
|───orchestrator
|   │
|   └───node_modules
|       │
|       └───package-a/dist
|       |   |  app.js
|       |   |  app.css
|       |
|       └───package-b/dist
│           |  app.js
|           |  app.css
```

At first I split the code into separate Git repositories and run [npm init](https://docs.npmjs.com/cli/init) for each one of them, to make it a valid npm package.

Then I had to choose how to host those packages.

I found a few options to manage private npm packages code:
* [npm enterprise](https://www.npmjs.com/enterprise) - paid service by npm.js itself
* [sinopia](https://www.npmjs.com/package/sinopia) - open source private npm repository server that we can install on our servers
* [install](https://docs.npmjs.com/cli/install) directly from GitHub
 
We didn't want to pay for it (npm enterprise) or to handle another server (sinopia), so we decided to work directly from our GitHub repositories.

Soon we found an issue with this approach. At any time we push a piece of code into a dependency package, we have to update the orchestrator package, so we have to run `npm update`, but unfortunatly, the code was not updated.

The reason is that npm was not aware of the new code, because we didn't change the dependency `package.json` 'version' attribute.

To solve this issue we could take the following:
* use [semver](http://semver.org/) versioning on each package (create new [Git Tag](https://git-scm.com/book/en/v2/Git-Basics-Tagging) on each `git push` command -
we found it too difficult because we have too many dependencies among packages.
* use [npm link](https://docs.npmjs.com/cli/link) on each package - work locally with [symbolic links](https://en.wikipedia.org/wiki/Symbolic_link) -
we found it very hard to work this way - whenever `npm install` is inovked it deletes our symlinks and we must run it again.
* delete our packages everytime we run `npm update` with some [npm scripts](https://docs.npmjs.com/misc/scripts) simple trick-
but it took too much time.
* create a script that updates the corresponding `dist` folders everytime we rebuild a package- 
this was the easiest and most elegant solution, so we chose it.

So we wrote a small node.js script. Each time we build one of our packages, it updates the `dist` folder on every `node_modules` folder that uses it. This is not the best solution, but it effectively solves the npm update issue when developing locally.

[Here](https://gist.github.com/adica/0ceddc17cbfc4c94b299d4e76a6092aa) you can download the script; I hope you will find it usefull for your project.

Notice: the npm team has told us that a better solution for working locally with many dependencies should be available soon during 2017 - I'm surely waiting for it.








