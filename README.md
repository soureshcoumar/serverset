# serverset
Creating a Node app with React, Webpack 4, Babel 7, and Express 

Make a new directory. Let's call it react-boilerplate.
mkdir reactserver

Now, initialize the node project.
npm init

Install Express.js:
npm install --save express


✨ A folder called node_modules should automatically be created. Anything we install in our project will reside in that folder.

🆕 Time to write the server. Create a new folder called server. In that new folder, create a file index.js. Add this basic minimal code to that file:
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;
const mockResponse = {
  foo: 'bar',
  bar: 'foo'
};
app.get('/api', (req, res) => {
  res.send(mockResponse);
});
app.get('/', (req, res) => {
 res.status(200).send('Hello World!');
});
app.listen(port, function () {
 console.log('App listening on port: ' + port);
});
This allows the app to run on the specified port.
The code also tells the app that home route: "/" should return a status of 200 (success) and send the text: Hello World. Basic enough!
It also has a route "/api" that returns a dummy JSON object. It shows how fetching data would work.

⭐️ Remember that the order of the routes matters. When a request comes through, Express starts matching routes from the top. When it matches a route, the response is returned and further routes are not checked.

✏️ Now that the server is set up, in the package.json file, under "scripts", add a start command like so:
"scripts": {
  "start": "node server/index.js",
  "test": "echo \"Error: no test specified\" && exit 1"
}
Here, we're telling Node, that to start the project, start with server/index.js.

🏃🏻‍♂️If you run the npm run start command now, you should get a message:
"App listening on port: 3000" on the terminal.

🎉 Now go to http://localhost:3000 on your browser and the "Hello World" message should be showing up there. Go to http://localhost:3000/api and the dummy JSON should show up.

1.2 Webpack
📦 Time to install 

webpack (The bundler)
webpack-cli (Command Line Interface to be able to run webpack commands)
npm install --save-dev webpack webpack-cli

In the package.json file, under "scripts", add a build command:
"scripts": {
  "start": "node server/index.js",
  "build": "webpack --mode production",
  "test": "echo \"Error: no test specified\" && exit 1"
}
🆕 Now, create a folder called src. This is where most of our project's source code will exist. In that new folder src, create a file index.js.
Leave the file empty for now.

🏃🏻‍♂️If you run the npm run build command, it will create a dist folder with a bundled main.js file in it. The code in it will be minified for production use.

🛰️ Chapter 2: Twilight Ozone
2.1 Babel
🤗 React embraces JSX. (Although JS would work perfectly fine too).
Babel helps compile JSX syntax to JS.
♻️ Not just that, but for regular .js files, we can now use the ES6 syntax and Babel will compile that to its equivalent ES5 form.

📦 Install

@babel/core (To transform ES6+ code to ES5)
@babel/preset-env (Preset to allow polyfills)
@babel/preset-react (Preset for React and JSX)
babel-loader (Webpack helper)
npm install --save-dev @babel/core @babel/preset-env @babel/preset-react babel-loader

Among these 4 new packages, 2 of them are presets. Babel core needs to know that it has these plugins. They need to be specified.

🆕 On the project's root level, create a .babelrc file. And specify the presets as an array like so:
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
If your code needs to be polyfilled, you will also need these Node packages: core-js and regenerator-runtime. More about that here.

2.2 Babel + Webpack
Based on Babel's functionality, Webpack needs to know that .js and .jsx files need to go through Babel before being bundled.
So, we need to configure Webpack for that rule.

🆕 On the project's root level, create a webpack.config.js file. This will be the file for all webpack configs. Add the rule to it like so:
module.exports = {
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
⭐️ Remember: Since webpack bundles everything and creates a simple browser-readable code, all packages, presets and plugins you install will need to be configured in webpack.

🏖️ Chapter 3: Utopia
3.1 React
Time to install react and react-dom.
npm install --save react react-dom

🆕 In the folder src/, create a new file index.html. This will be the main and the only HTML file in our project.
It would be like any regular HTML file you've made, with one difference: It needs a <div> in the <body> that React can populate. 
🔍 And it needs some form of identifier that React can lookup for.
We set id="root" on the div. You can set the id to anything you want.
Here's what a simple index.html with <div id="root"></div> looks like:
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>React Boilerplate</title>
</head>
<body>
  <div id="root"></div>
</body>
</html>
✏️ Remember that empty src/index.js file we created in Section 1.2?
Time to add code to it:
import React from 'react';
import ReactDOM from 'react-dom';
const Index = () => {
  return <div>Welcome to React!</div>;
};
ReactDOM.render(<Index />, document.getElementById('root'));
⚡️Here, Index is a functional component that returns a React element. And ReactDOM renders it inside the <div id="root"></div> from index.html.

3.2 React + Webpack
Similar to what we did for .js and .jsx files, we need to tell Webpack what to do with the new .html file. Webpack needs to bundle it to the dist folder.

📦 For that we install html-webpack-plugin.
npm install --save-dev html-webpack-plugin

✏️ The webpack config file needs to be updated to handle this plugin. We also tell webpack that the now-coded src/index.js is the entry point.
Here's what the config file looks like after adding that:
const HtmlWebPackPlugin = require("html-webpack-plugin");
const path = require('path');
const htmlPlugin = new HtmlWebPackPlugin({
  template: "./src/index.html", 
  filename: "./index.html"
});
module.exports = {
  entry: "./src/index.js",
  output: { // NEW
    path: path.join(__dirname, 'dist'),
    filename: "[name].js"
  }, // NEW Ends
  plugins: [htmlPlugin],
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
When instantiating htmlPlugin, the template option tells webpack what file to pick and the filename option tells what to name the bundled .html file in the dist folder.

3.3 React + Express
🏃🏻‍♂️ If you run npm run start now, we would still get the "Hello World" message on localhost. That is because the Express server does not know of these new files.

✏️ In package.json, the start script simply starts the server.
We now need the webpack to bundle up our files and then start the server. 
Under "scripts", add a new dev command.
"scripts": {
  "start": "node server/index.js",
  "dev": "webpack --mode development && node server/index.js",
  "build": "webpack --mode production",
  "test": "echo \"Error: no test specified\" && exit 1"
}
We should now update Express and change what the route "/" returns.
It should return the dist/index.html file.

✏️ In server/index.js, make the updates (New parts of code end with a comment: // NEW):
const express = require('express');
const path = require('path'); // NEW

const app = express();
const port = process.env.PORT || 3000;
const DIST_DIR = path.join(__dirname, '../dist'); // NEW
const HTML_FILE = path.join(DIST_DIR, 'index.html'); // NEW
const mockResponse = {
  foo: 'bar',
  bar: 'foo'
};
app.use(express.static(DIST_DIR)); // NEW
app.get('/api', (req, res) => {
  res.send(mockResponse);
});
app.get('/', (req, res) => {
 res.sendFile(HTML_FILE); // EDIT
});
app.listen(port, function () {
 console.log('App listening on port: ' + port);
});
🎉 Now run npm run dev and go to http://localhost:3000 on your browser. The "Welcome to React!" message from src/index.js should be showing up there. The "/api" route still works as before.

React Components
React projects are made up of a number of smaller components. The Index in src/index.js is one such component. You will be creating more such components and importing them (into one another 🤨).

📂 I would suggest creating a folder called components/ inside the src/ folder. And store all the other components in that folder.

Ideally, inside of components/, create a sub-folder for every component. 
But it's up to the individual preference!

💡 Don't forget that: React component files should export the component Class or function.
Once you add some components to the src/index.js, it would look something like this:
import React from 'react';
import ReactDOM from 'react-dom';
import Header from './components/Header/index.jsx';
import Content from './components/Content/index.jsx';
const Index = () => {
  return (
    <div className="container">
      <Header />
      <Content />
    </div>
  );
};
ReactDOM.render(<Index />, document.getElementById('app'));
🔧 Additional Webpack Configuration
Like other files, images or any other static files also need to be bundled. Webpack needs to know of that.
📦 Install file-loader as a devDependency (--save-dev) for all of such files.
And add the following rule in webpack.config.js:
{
  test: /\.(png|svg|jpg|gif)$/,
  loader: "file-loader",
  options: { name: '/static/[name].[ext]' }
}
In the above code, the test regex only specifies general image extensions. But, you can add any extensions for other files too (in the same rule object).

✏️ To use an image or any other assets in the components, it needs to be imported in that.js/.jsx file first. So, Webpack can bundle it right and make it available in the bundled folder. You could use the actual [name] of the file or [hash] it up. With or without the file [ext].
// Import
import LOGO from '<path-to-file>/logo.png';

...

// Usage
<img src={LOGO} alt="Page Logo" />
🙅🏼‍♂️ Git, ignore!
For deployment, a Node-compatible platform like Heroku or Netlify, runs the build command in your app. That installs all the node_modules and generates the dist folder and its contents.
So, we don't need to push the local folders: node_modules and dist to remote.

🆕 To let Git know this, create a new file .gitignore on the project root level.
Anything you want Git to ignore can be added here. Here's a basic version:
# Deployment directories
node_modules
dist
# Optional npm cache directory
.npm
# Mac
.DS_Store
🍺 That concludes the setup. This project can act as a great boilerplate for any future React w/ server apps or even for stand-alone Express projects.

👍🏼 Thanks for making it all the way through the long article. Setting up an error-free Node app with Webpack and Babel and Express is definitely not a cakewalk. But I hope this article helped you. 

🌏 Go Planet!

reference https://dev.to/kedar9/creating-a-node-app-with-react-webpack-4-babel-7-express-and-sass-3mae
