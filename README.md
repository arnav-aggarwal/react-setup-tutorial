# React Ecosystem Setup —Simple, Step-By-Step Walkthrough

## React, Webpack, Babel, Node, Express

![React logo](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/react-logo.png)

### What This Guide is For

React is arguably the hottest framework currently used by the web development community at this point. It makes large, dynamic, single-page applications faster and easier to develop while providing speed optimizations that can make sites blazingly fast. Unfortunately, for newcomers and even more seasoned developers, setting up the React development environment for the first time is difficult, frustrating, and daunting. There are boilerplates such as [create-react-app](https://github.com/facebookincubator/create-react-app) readily available for people who want to skip configuration and get started, but using them doesn’t provide insight into what’s actually going on.

**This article is meant to take you through the setup process while teaching you as much as possible about what you and the tools you’re using are doing.** The technologies we’ll discuss are what the React community has decided to embrace in the front-end ecosystem and what you’ll see professional developers and industry leaders using. It’s also what you’ll see in most examples online. There’s a whole ton of tools and configuration purposely left out as this is meant to be a bare-bones beginners’ guide.

The state of the current master branch of this repository is what we'll be working towards.

This article requires some elementary proficiency with HTML, Javascript (including very basic ES6 syntax), CSS, Node, and preferably Express. **If you can make a to-do app, you’re ready for this**. Let’s get started.

I’m using npm v 5 and Node v 8. If you’re using an older version of Node you should be fine. npm 5 saves dependencies to your package.json by default without the need for a flag, so if using npm 4 or below, make sure to use `--save` when installing dependencies. I’d recommend upgrading to npm 5. It’s faster and locks down your dependencies with a package-lock.json file, denoting every version of every dependency. You can use webpack 2 or 3, which was released 6/19/17 — both should work.

### Elementary Setup

#### Initial Configuration

Let’s set up a very basic file system. Run `npm init` and get a `package.json`. Let’s add a few files. In your project’s root directory, create a file called `server.js` and a folder called **`dist`**. **`dist`** is where we will store all client-facing code. In dist, create an app.html file. Make sure your file structure looks like this. Files are in normal font and folders are bold. 

<pre>
<b>project_root</b>
|
|---<b>dist</b>
|    |
|    |---app.html
|
|---package.json
|---server.js
</pre>

The file structure can also be viewed [here](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/bc5495656c516509645a5d9e8b0e565fa61cff93).

#### Basic HTML File

Let’s write to our `dist/app.html` file. Note that we have a div with an ID of `container` in the body, containing only the word ‘React’.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>React - Basic Setup</title>
</head>
<body>
    <div id="container">React</div>
</body>
</html>
```

#### Basic Express Server

Run `npm install express`. You’ll get a `package-lock.json` if using npm 5. In `server.js`, we’ll write some code to get a basic Express server up and running. If you’ve set up an Express server before, this shouldn’t be new. If you haven’t used Express, it’s not critical to learning React. Just know that this creates a server on port 8080 and serves up `dist/app.html`.

```
const express = require('express');
const path = require('path');

const app = express();
const port = process.env.PORT || 8080;

app.set('port', port);
app.use(express.static(path.join(__dirname, './dist')));

app.get('*', (req, res) => {
    console.log('Serving ', req.url);
    res.sendFile(__dirname + '/dist/app.html');
});

app.listen(port, () => console.log('Listening on port', port));
```

Run `npm start` (this is a built-in alias for `node server.js`). You should see something like this.

![start server](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/start-server.png)


Verify that the server is working by going to [localhost:8080](localhost:8080). You should see the word ‘React’ on the page. Awesome, we have a basic HTML file and server!

You should have [these changes](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/e5d3ad4bb129ee1b05947372a4f65675fa36eb50).

### Webpack

![webpack logo](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/webpack.png)

#### What It Does

Common practice in the React ecosystem is to bundle all of the different Javascript and CSS files we write into one big file. Webpack is the tool for the job. During the bundling, we’ll have webpack perform some more actions.

- It’ll resolve our dependencies by going into the modules we import and concatenating those as well, pulling them out of our `node_modules` and putting them at the top of our single file. It’ll recursively go through every dependency and the dependencies’ dependencies, resolving all the way down and generating one massive bundle.

- It’ll transpile our farm-fresh, cutting-edge ES6 down into ES5 so that it’ll work on virtually every browser.

- It’ll minify our code by removing whitespace and shortening variable names, reducing the final file size.

In the end, we’ll be able to write modular, modern Javascript and CSS that we can work with easily and use best practices on. Webpack will take all of our code and optimize it by creating one minified file with all of the Javascript and CSS that our website needs.

#### Getting Started

Let’s create a folder named **`src`** in our root directory. This will contain all of our Javascript and CSS. For now, we’ll focus on the Javascript, so put a file called `index.js` into **`src`**. In `src/index.js`, put a single line of code:

```
console.log('Testing our bundle');
```

With webpack, `src/index.js` will be our starting point, or entry point. Webpack will start with that file and generate our bundle. Let’s install webpack:

```
npm install -D webpack
```

Webpack requires a configuration file called `webpack.config.js`. Let’s create this file in our root directory. Excluding `node_modules`, our file structure now looks like this.

<pre>
<b>project_root</b>
|
|---<b>dist</b>
|    |
|    |---app.html
|
|---<b>src</b>
|    |
|    |---index.js
|
|---package.json
|---package-lock.json
|---server.js
|---webpack.config.js
</pre>

`webpack.config.js` needs to export a configuration object. Add this into the file.

```
const path = require('path');
module.exports = {
    entry: path.resolve(__dirname, './src/index.js'),
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: 'app.bundle.js',
    }
};
```

What our configuration object is telling webpack to do is to start at the entry point, `src/index.js`. It’ll go into that file, resolve all dependencies, and bundle them into the output path provided, the folder **`dist`**. It’ll name the new bundled file `app.bundle.js`.

Go ahead and run `webpack` on the command line and see what happens.

Done? Good. You should see `app.bundle.js` magically appear in your **`dist`** folder. Go ahead and run

```
node dist/app.bundle.js
```

You should see '`Testing our bundle`' log on your Node console. Congratulations, you’ve created your first webpack build!

If using git, you’ll want to add `dist/app.bundle.js` to your `.gitignore`. We only want to keep track of the source files, not the generated bundle.

Eventually, we’re going to want to run the code in this file to generate our app to the DOM. Let’s add a script tag in our `dist/app.html`.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>React - Basic Setup</title>
</head>
<body>
    <div id="container">React</div>
</body>
<script src="app.bundle.js"></script>
</html>
```

If you open up `dist/app.html` in the browser, you’ll now see the ‘`Testing our bundle`’ statement log to the browser’s console.

At this point you should have added [these changes](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/57b0ecd0684e85b971c9b38977ff62398201d051).

#### Flags

At this point, nothing is being minified, so the code is all pretty long. Open up `dist/app.bundle.js` and see what’s inside if you like. To make webpack minify your code, use the production flag, `-p`. Type in `webpack -p` and look at the bundle file. You’ll see one long line of unreadable code with all unnecessary whitespace removed.

![server-output](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/start-server.png)

An issue with using a bundle instead of our source code is that it makes debugging more difficult. If we open our HTML file in the browser now, we’ll see this:

![console.log without sourcemap](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/no-sourcemap.png)

The stack trace for the `console.log` statement tells us to go to `app.bundle.js`, line 73. That’s not very useful. We want the stack trace to show our files correctly. 

We need a source-map — a way to tell the browser to display errors and logs according to the _source_ and not the final bundle. Using the development flag, `-d`, will tell webpack to generate a source map around our code. A source map adds a considerable amount of code, so the file size will be larger. Using the development flag,-d, will tell webpack to generate a source map for our code. Go ahead and run

```
webpack -d
```

Now, we get a better log. We can actually see that it comes from line 1 in `index.js`.

![console.log with sourcemap](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/sourcemap.png)

While developing, it’s not fun to have to type webpack over and over again. Using the --watch flag during development means webpack will watch our files and re-generate the bundle every time we save a change. **During development, we’ll want to use both the development flag and the watch flag, so our command will be**

```
webpack -d --watch
```

*Every time you change `webpack.config.js`, you’ll need to restart webpack for the changes to affect your bundle.

### Babel

![Babel logo](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/babel.png)

I mentioned earlier that we want to be able to use new, shiny ES6 code while making our websites cross-browser compatible. Babel is the library we’ll use to transpile our code, and we’ll configure webpack to do it for us when we run the webpack command. Go ahead and install these three dependencies.

```
npm install -D babel-core babel-loader babel-preset-env
```

- [Babel-core](https://www.npmjs.com/package/babel-core) is a library that takes in code as a string and transpiles it down. 

- [Babel-loader](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fbabel%2Fbabel-loader) is what allows us to tie babel-core into webpack and lets webpack’s configuration know to use the babel-core library. 

- [Babel-preset-env](https://babeljs.io/docs/plugins/preset-env/#how-it-works) is essentially a configuration for babel which lets us pass in options. Without the options, it uses defaults, which means it will understand code up to ES2017 standards and transpile it down to ES5.

There are a number of ways to configure webpack with babel and we’ll just pick an easy one. You’ll need to add some code into `webpack.config.js`.

```
const path = require('path');
module.exports = {
    entry: path.resolve(__dirname, './src/index.js'),
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: 'app.bundle.js',
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: [/node_modules/],
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['env']
                    }
                }
            }
        ]
    }
};
```

This rule is telling webpack to test the files in our directory and look for the filenames matching the regex in the test property. `/\.js$/` is looking for files ending with `.js`, so it’ll find all of our Javascript files. We’re telling it to exclude our `node_modules` folder, because we don’t want to transpile that down, as all of our dependencies should already be written in ES5. Lastly, we’re telling it we want to use babel-loader, which links the babel-core library to webpack. We want to use the `env` preset, which works with ES2015, 2016, and 2017 code, so we can use the newest features.

Now we can use ES6 code and the bundle generated by webpack will contain only ES5 code.
Changes are [here](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/2c38450e627a9a03d9850d5203f6294634904666).

### React

#### Setting it Up

Let’s get started actually learning React. Run the following two commands. You need react, react-dom, and babel-preset-react.

```
npm install react react-dom
npm install -D babel-preset-react
```

We’ll have to add a term to our `webpack.config.js` file to tell it that we’re writing React code.

```
const path = require('path');
module.exports = {
    entry: path.resolve(__dirname, './src/index.js'),
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: 'app.bundle.js',
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: [/node_modules/],
                use: {
                    loader: 'babel-loader',
                    options: {
                    presets: ['react', 'env']
                    }
                }
            }
        ]
    }
};
```

We’re telling Babel to use a special React preset. It’ll transform our React code into standard Javascript. *But isn’t React just Javascript??* Not exactly. We’ll get to that.

You should be [here](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/574f3478b2b94de9c69e4efbba826fb7e924a987).

#### Our First React Component

In `src/index.js`, delete the `console.log` statement and write the following code.

```
import React from 'react';
import ReactDOM from 'react-dom';

const container = document.getElementById('container');
const myDiv = <div>My First React Component!</div>;

ReactDOM.render(myDiv, container);
```

Start your server and webpack (use `webpack -d --watch`) to see what’s happening in your browser. Let webpack run in the background so we can continue to see our changes.
There’s a bunch of stuff going on here, so let’s break it down.

- We can use `import` statements because webpack will resolve those for us. It’ll grab React and ReactDOM out of our `node_modules` and throw them at the top of our bundle, with the rest of our code below it.

- We’re accessing the div with ID `container`. This is the DOM element that we’ll place our React elements in.

- The next line, creating the variable `myDiv`, is the most exciting. We’re writing something that looks like HTML in our Javascript. `myDiv` is a variable that contains what we can think of as a DOM element that we just created.

- The last line uses the ReactDOM library to render our myDiv element on the DOM.

Note that we import React, but don’t actually use it anywhere. This is because during transpilation, webpack and babel transform our code.

To see what it finally turns into after webpack runs its magic, we can go to [babeljs.io](https://babeljs.io/repl). The code below is the transpiled version of our five-line block above.

```
'use strict';
var _react = require('react');
var _react2 = _interopRequireDefault(_react);
var _reactDom = require('react-dom');
var _reactDom2 = _interopRequireDefault(_reactDom);
function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

var container = document.getElementById('container');
var myDiv = _react2.default.createElement(
  'div',
  null,
  'My First React Component!'
);

_reactDom2.default.render(myDiv, container);
```

We need React in scope because our transpiled code needs access to it, even though we don’t directly use it ourselves. Note also that all ES6 features have been removed — import statements and constant variable declarations are transformed down to ES5.

Run webpack on the terminal to transpile your code. Start the server with npm start and go to [localhost:8080](localhost:8080). You should see what you expect. If we open your browser’s dev tools and inspect the text, we see our div nested inside the `#container` div present in our original HTML.

![my first component](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/my_first_component.png)

### JSX

![JSX](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/jsx.png)

I said we’re writing stuff that looks like HTML. It’s not actually HTML. It’s something called [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html). JSX is something created by Facebook that provides an easy, mostly straightforward syntax to help us write React components. For the most part, it’s very similar to HTML, with a few key differences.

Start off writing a JSX component by writing something like you would in HTML. Instead of using the word `class` to denote the class we want to give our element, we need to use `className`. If we want to use Javascript while generating our element, we can use a pair of curly brackets to denote code that should be evaluated. Let’s type some more code into the babel repl and see how this works.

```
const out = 'outer';
const inn = 'inner';
const div = 'Div';
<div className={out + div}>
  <div className={inn + div}>
    <h1>JSX</h1>
    <span>Creating a JSX Div</span>
  </div>
</div>
```

This turns into:

```
'use strict';

var out = 'outer';
var inn = 'inner';
var div = 'Div';

React.createElement(
  'div',
  { className: out + div },
  React.createElement(
    'div',
    { className: inn + div },
    React.createElement(
      'h1',
      null,
      'JSX'
    ),
    React.createElement(
      'span',
      null,
      'Creating a JSX Div'
    )
  )
);
```

On the DOM, this looks like:

```
<div class="outerDiv">
    <div class="innerDiv">
        <h1>JSX</h1>
        <span>Creating a JSX Div</span>
    </div>
</div>
```

![DOM image](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/jsx-2.png)

#### Refactoring the Component

First, let’s go ahead and delete ‘React’ from inside our container div in `dist/app.html`. After that, you should have [these changes](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/93fd6a7575a2d46a979841f571cb76345db88078).

Let’s make it so `src/index.js` contains logic that only deals with rendering our app to the page. We want to abstract all the JSX into separate files. Let’s create a **`components`** folder in **`src`**. In there, let’s create a file called `app.js` that will contain the logic dealing with creating our site. All `src/index.js` should do is render our app to the DOM.

<pre>
<b>project_root</b>
|
|---<b>dist</b>
|    |
|    |---app.html
|
|---<b>src</b>
|    |
|    |
|    |---<b>components</b>
|    |    |
|    |    |---app.js
|    |
|    |---index.js
|
|---package.json
|---package-lock.json
|---server.js
|---webpack.config.js
</pre>

Let’s start by changing `src/index.js` a little bit. First, let’s grow our div a little bit. We’ll add an `h1` and a `span` inside our div and we’ll rename `myDiv` to `app`.

```
import React from 'react';
import ReactDOM from 'react-dom';

const container = document.getElementById('container');
const app = (
    <div>
        <h1>JSX</h1>
        <span>My first JSX span!</span>
    </div>
);

ReactDOM.render(app, container);
```

We’re now going to move the app variable into a separate file. Export it from `src/components/app.js`. `src/index.js` should now look like this:

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/app';

const container = document.getElementById('container');
ReactDOM.render(App, container);
```

`src/components/app.js` should now have:

```
import React from 'react';

export default (
    <div>
        <h1>JSX</h1>
        <span>My first JSX span!</span>
    </div>
);
```

Again, we need React in scope even though we don’t apply it directly. Without it, the JSX wouldn’t work. If you omit the import statement, you’ll get an error saying React isn’t in scope.

If you refresh your page, you should see that nothing’s changed.

#### Exporting a Component

We’re going to turn `app` into a function that _returns_ the JSX we want. We’ll explain why in a second. Change `src/components/app.js` into this.

```
import React from 'react';

export default function app() {
    return (
        <div>
            <h1>JSX</h1>
            <span>My first JSX span!</span>
        </div>
    );
}
```

Change `src/index.js` into this.

```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './components/app';
const container = document.getElementById('container');

ReactDOM.render(<App></App>, container);
```

When we import a function that returns JSX, we can use that function as a new element, just like we use what we’re already familar with. We’ve essentially defined a new element. We treat it like we would a `div`, `span`, `h1`-`h6`, etc. We’re rendering `App` and treating it just like we would an element, with an opening and closing tag. **Using those opening and closing tags invokes the corresponding function and inserts the returned element in their place.**

In addition, an empty tag can be self-closing. In other words,

```
<div></div> === <div />
<App></App> === <App />
```

We’ll be using this shorthand from now on. You should be [here](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/a2807f2d124e0b01f97d510eed2f4c10b9a16c50).

That’s the basics of the basics. You’re on your way to learning React.

### CSS

We want to be able to use stylesheets for our website. We need to add some babel configuration. First, we need new dependencies.

```
npm install -D style-loader css-loader
```

Css-loader will resolve the import statements we use and concatenate CSS into our bundle. Style-loader will actually apply that CSS so we see it working on the page.

We also need to update `webpack.config.js` to tell it to transform our imported CSS files using these loaders.

```
const path = require('path');

module.exports = {
    entry: path.resolve(__dirname, './src/index.js'),
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: 'app.bundle.js',
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: [/node_modules/],
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['react', 'env']
                    }
                }
            },
            {
                test: /\.css$/,
                use: [
                    'style-loader',
                    'css-loader'
                ]
            }
        ]
    }
};
```

It’s a “best practice” to create a separate CSS file for each component, with only the CSS needed for that component present in that file. In **`src`**, add a **`styles`** folder. We’ll create `src/styles/index.css` and add our main CSS to that file. Let’s also create `src/styles/app.css` to store more specific CSS.

<pre>
<b>project_root</b>
|
|---<b>dist</b>
|    |
|    |---app.html
|
|---<b>src</b>
|    |
|    |---index.js
|    |
|    |---<b>components</b>
|    |     |
|    |     |---app.js
|    |
|    |---<b>styles</b>
|          |
|          |---app.css
|          |---index.css
|
|---package.json
|---package-lock.json
|---server.js
|---webpack.config.js
</pre>

In `src/styles/index.css`, we’ll add what we want to apply across the whole document.

```
html, body {
    font-family: sans-serif;
    background-color: #dddddd;
}
```

In `src/styles/app.css`, we’ll add CSS specific to the component.

```
h1 {
    color: #31416D;
}
```

With our webpack setup, we can import stylesheets directly. Add this directly below the import statements in `src/index.js`.

```
...
import App from './components/app';
import './styles/index.css';
...
```

And similarly, under the import statement in `src/components/app.js`:

```
import React from 'react';
import '../styles/app.css';
...
```

With webpack running, if we refresh our web page we should see the styled changes.

![styled component](https://github.com/arnav-aggarwal/react-setup-tutorial/blob/readme/readme_images/styled.png)

The final changes are [here](https://github.com/arnav-aggarwal/react-setup-tutorial/commit/2133261712bfb051877647a1cfc7cc14b78b4b4e). The complete repository is at:

https://github.com/arnav-aggarwal/react-setup-tutorial

### Conclusion

Now you can focus on actually learning React. There are a ton of extra tools we can add — CSS preprocessors, testing frameworks, linters, other libraries such as redux and react-router. If interested, see [my more thorough React boilerplate repository](https://github.com/arnav-aggarwal/react-boilerplate-setup). But this will get you started.

If this was useful, please hit the heart and feel free to check out my other articles at

https://medium.com/@arnav_aggarwal

### That’s it. Go learn React.

