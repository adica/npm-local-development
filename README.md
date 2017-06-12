# Working locally with multiple NPM packages from GitHub

On my current project, I've been asked to split very big client side project into small chunks of code. the purpose was to isolate sections of the app, make the code chunks small and readable, and enable scaling the development process by letting many developers works each one on a separate section of the code.

The way our packages build is that every package is export it's isolated code into `dist` folder (`app.min.js` and `app.css` files), and the main orchestrator package uses those files to build the web page.

The first thing I had to do was to split the code into separate Git repositories and run [npm init](https://docs.npmjs.com/cli/init) for each one of them, to make it valid npm package.

Then I had to choose how to host those packages.

I found few options to manage private npm packages code:
* [npm enterprise](https://www.npmjs.com/enterprise) - paid service by npm.js itself
* [sinopia](https://www.npmjs.com/package/sinopia) - open source private npm repository server that we can install on our servers
* [install](https://docs.npmjs.com/cli/install) directly from GitHub
 
Our organization didn't want to pay for it (npm enterprise) or to handle another server (sinopia), so we choose to work directly from our repositories on GitHub.

Soon we found an issue with this approach. Each time we pushed code into dependency package, we wanted to update the orchestrator package, so we run `npm update`, but the code did not update..

The reason is that npm did not know that we push new code, cause we didn't change the dependency `package.json` 'version' attribute. 

To solve this issue we could do one of the following:
* use [semver](http://semver.org/) versioning on each package (create new [Git Tag](https://git-scm.com/book/en/v2/Git-Basics-Tagging) on each `git push` command -
we found it's a hassle to develop like that (cause we use many packages as dependencies).
* use [npm link](https://docs.npmjs.com/cli/link) on each package - work locally with [symbolic links](https://en.wikipedia.org/wiki/Symbolic_link) -
we found it very hard to work this way (each time you run `npm install` it will delete your symlinks and you must run it again).
* delete our packages everytime we run `npm update` with some [npm scripts](https://docs.npmjs.com/misc/scripts) simple tricks -
but it took too long.
* create a script that updates our packages `dist` folder everytime we build a package- 
this a was very easy solution, so we choose it.

So I wrote a small node.js script. Each time we build one of our packages, it will update the `dist` folder on every `node_modules` folder that uses it. This is not the best solution, but it solved the npm update issue when developing locally very easily and effectively.

[Here]() you can see the script, and if you ever enter the same situation maybe you can find it useable.

Notice: the npm team told us that a better solution for working locally with many dependencies should be available later this year (2017) - I surely hope so..








