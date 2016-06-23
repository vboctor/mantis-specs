# Modern Build for MantisBT
MantisBT currently doesn't have a build step as part of the developer workflow.  We have some custom scripts that are run at publishing time when packaging releases or daily builds, but are not used on the developers machine.  Such builds are also hosted in a separate repository.

The goal of this document is to explain how different aspects of a typical build system are handled today and will make the case for transitioning to more modern tools to help provide a better development experience as well as same or better runtime experience.  The document will suggest specific tools to use, but we may end up leveraging similar tools that do the same function as we agree on the model and do the necessary homework.

## Package Management
At the moment, MantisBT doesn't leverage a package management tool, but has a manual process that leverages the following:

- Leverages git submodules to add dependencies.
- Uses a text file to manually track the versions for such components.
- If parts of these components need to be accessed from web pages (e.g. images, css, javascript), we need to manually have these files added to the corresponding folders in MantisBT path.  For example, jscalendar, jQuery, drop zone, etc.

The proposal is to leverage package management tools to pull in such dependencies, track their versions and dependencies, update them, etc.  The proposal would be to provide a way to easily manage both php and javascript packages.  Hence, we should consider leveraging:
- [composer](https://getcomposer.org/) for php package management
- [npm](https://www.npmjs.com/) for javascript package management

## Javascript
At the moment, MantisBT code base doesn't include much MantisBT-specific javascript code.  For the javascript code that is authored for MantisBT, it is typically not minified and is written to the target javascript version, rather than the latest which is typically more developer friendly.  The javascript code is also distributed in the same modules that are used during development.  Hence, to be more efficient, code that is re-used often, should be part of a single (common.js) file which other code can be in its own file (e.g. bugFilter.js).  Anyways, there is a tension between maintainability and efficiency.  We also don't minify files that we author.

As we modernize MantisBT UI, we will leverage more Javascript code, including MantisBT specific Javascript code as well as libraries that help power the modern experiences.  The goal is to optimize for the developer as well as the runtime environment.  This will be achieved by:

- Leverage [ECMAScript 2015](https://babeljs.io/docs/learn-es2015/) which enables the developer to leverage the latest and greatest Javascript features (e.g. classes), and structure the code in a way that is maintainable (e.g. a javascript file per class or module).
- Leverage [Babel](https://babeljs.io/) to compile the javascript code into the Javascript version that is compatible with browsers that are used by target users.  The compiler will also achieve tasks like consolidation into fewer JS files for browsers to download, minification of such JS files, and static analysis to capture errors that would otherwise be captured at runtime.

## CSS
We currently author standard CSS which is what browsers expect.  We don't do any CSS minification or leverage languages that are more developer friendly and end up producing the CSS that browsers support.  The proposal is to leverage [less](http://lesscss.org/) or [sass](http://sass-lang.com/).

## Build
At the moment, we don't have a build that can be leveraged during the process of development, hence, there is no transformation step between what developer writes and what ends up executing.  This is OK for the PHP code, but is more problematic when authoring css and javascript.

The goal would be to leverage a build system (e.g. [gulp](http://gulpjs.com/)) that would provide the following features:

- Supports watch mode where as developer saves a change, the necessary compilation is done automatically.
- Drives transformations for css, javascript, images, etc.
- Compiles documentation
- Publishes daily and official releases.
- Run static analysis
- Run unit test tools
- Trim files that may not be necessary for build target (e.g. remove tests/ folder from release package).

Some of the above steps, will only be run for specific build targets, where others will always run.

## Maintain Simplicity
It is important that as we make the above improvements, we still maintain the simple setup that we currently offer to our users.  Hence, the process of downloading a MantisBT daily build or release and installing it shouldn't be affected by changes suggested in this document.
