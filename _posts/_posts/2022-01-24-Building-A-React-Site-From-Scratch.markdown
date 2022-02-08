---
layout: post
title:  "Building A React Site From Scratch"
date:   2022-01-24
categories: blog
---

# Notes to Self

This started as a list for personal use, as I can never remember the exact tools and dependencies to set up a React app with a Node backend. After learning to do things the hard way, I've developed a preference for setting things up manually rather than the one-and-done `create-react-app`... but which packages get marked as 'dev' dependencies again? And what were the Babel presets I needed? I'm surely not the only developer out there that struggles to recall these sorts of details, so here I present my steps for building a React app from scratch.

## The Back End

I'm going to assume you already have your basics set up - Node.js, npm, VS Code or your favorite editor/IDE.

We need a way to serve our front end and Express.js makes this a relatively simple task. Adding Nodemon to the mix allows the server to automatically restart when files are changed, which makes development much smoother. Nodemon can be installed as a global package if you want to use it directly from the command line, but I prefer to install locally as a dev dependency. Rounding out the back end we have `dotenv` and `cors`.

```
npm install express dotenv cors
npm install --save-dev nodemon
```

## Installing React

All we need here are the React and ReactDOM libraries:

```
npm install react react-dev
```

I guess that's the easy one to remember. Depending on your project needs you might also want React Router for website navigation, version 6 is the newest at the time of this writing:

```
npm install react-router-dom@6
```

## Development Dependencies

Now for the complicated stuff. Linting, transpiling, bundling, oh my. First the list we have Webpack to bundle all the front end code into one file. Babel along with it's presets *transpile* any code with ES6 (or above) syntax to ES5 so it can be used by older browsers. Finally our "loaders" to get our CSS and JavaScript into our bundle.

```
npm install --save-dev webpack webpack-cli \
@babel/core @babel/preset-react @babel/preset-env \
babel-loader css-loader style-loader html-webpack-plugin
```

I added `html-webpack-plugin` to generate our index HTML file so we don't have to worry about plugging in any scripts or links every time something changes.

## Configuration and File structure

This part of the setup is largely personal preference as long as you know how to configure webpack and imports to look in the right place. Here is a general overview of my typical file structure:

root_folder/
- build/
- client/
- - index.js
- - index.html
- node_modules/
- server/
- - app.js
- .babelrc.js
- webpack.config.js

For our Babel config we just need to add any presets or plugins:

```
module.exports = {
  presets: [
    [
      "@babel/preset-react",
      {
        development: process.env.PROJECT_MODE === "development"
      }
    ],
      "@babel/preset-env"
  ]
};
```

The Webpack config is a bit more involved. The first piece is the "mode" property, which tells webpack if the project should be built for development or production. We also need to specify an entry point and output location for the bundle file. Module setup :

```
const path = require('path');
const webpack = require('webpack');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  mode: process.env.PROJECT_MODE,
  entry: path.resolve(__dirname, 'client', 'index.js'),
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: 'bundle.js'
  },
  module: {
    rules: [{
      test: /\.(js|jsx)$/,
      include: path.resolve(__dirname, 'client'),
      exclude: /node_modules/,
      use: [{
        loader: 'babel-loader',
        options: {
          presets: [
            ['@babel/preset-env', { "targets": "defaults" }],
            '@babel/preset-react'
          ]
        }
      }]
    },
    {
      test: /\.css$/,
      use: [
        MiniCssExtractPlugin.loader,
        "css-loader",
        "postcss-loader"
      ]
    }]
  },
  plugins: [
    new MiniCssExtractPlugin(),
    new HtmlWebpackPlugin({ template: './client/index.html' })]
};
```

The "module" property tells our loaders what type of files to looks for and where to find them so they can be put into the final bundle. I have added the Mini CSS Extract plugin so I can have a separate css file for each component if I want.

## Client and Server

Whew, finally done with all the setup! Now to get something on the page!

Our index.html is very simple, as we just need a "root" element to plug our react code into:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>PAGE TITLE</title>
</head>
<body>
  <div id="root"></div>
</body>
</html>

```

Similarly, index.js doesn't require much to get something to display:

```
import React from 'react';
import { render } from 'react-dom';

const root = document.getElementById('root');

render(
  <h1>Exciting App Content</h1>,
  root
);

```

Finally, a quick server with express.js to load our content:

```
require('dotenv').config();
const express = require('express');
const app = express();
const path = require('path');

const port = process.env.PORT || 8080;
const static_path = (path.join(__dirname, '../build'));

const cors = require('cors');
let corsOptions = {
  origin: 'http://localhost:8081'
};

app.use(cors(corsOptions));
app.use(express.json());
app.use(express.urlencoded({ extended: true}));
app.use(express.static(static_path));

app.get('/', (req, res) => {
  res.sendFile(path.join(static_path, 'index.html'));
});

app.listen(port, () => {
  console.log(`App running at http://localhost:${port}`);
});

```

## Running the App

Now that all the configuration and code is complete, we just need a couple scripts to build the client-side code and run the server. In package.json:

```
"scripts": {
    "server-dev": "nodemon app.js",
    "client-dev": "webpack build --watch"
  },
```

All that's left is to open two terminals, in the first we run our client build process `npm run client-dev`, and the second we start our server `npm run server-dev`. Once the server is up, it should print out a link to your localhost port where the app is running:


![server running]({{ BASE_URL }}/resources/server-dev.png)


And there's the app!


![server running]({{ BASE_URL }}/resources/app-content.png)


