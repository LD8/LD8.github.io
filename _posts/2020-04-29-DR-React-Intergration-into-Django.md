---
layout: post
title: "React Django Integration"
categories: Daily Notes
---

## Make a Django app as usual

When working with React, Django mainly serves as an API. Django REST framework is a must.

## Make a React app as usual

The react app can be created with `create-react-app`, it doesn't really matter. Whenever you need to make an API request to get data to render, fetch it from the API you just made with Django. Of course you can use dummy data if you start off with React instead of Django.

## Integration

There should be at least 3 folders in your Django app:

1. `project/project` folder, meaning the one with `settings.py`
2. `project/api` folder, the one with the api `models.py`, `serializers.py` etc.
3. `project/frontend` folder, it's created by `manage.py startapp frontend` command and in this folder, more folders should be created:

   - `src`folder: contains an `index.js` file and all other react components in `src/components` folder
   - `static/frontend` folder: where `main.js` file lives after you run `npm run build` or `npm run dev` (depends on the script you write --> keep reading)
   - `templates/frontend` folder: where you store your django template `index.html`, remember you can use django template language/syntax in these html files

### Reminders - Make sure:

1. you added `frontend` app in your `settings.py`
2. added `{% load static %}` and `<script src="{% static 'frontend/main.js' %}></script>` in `index.html`
3. added `index` function in your `views.py` file
4. added according url patters in your `urls.py` file
5. included url patterns in `frontend/urls.py` into `project/urls.py` file

## Webpack configuration:

Assuming you've had [`Node.js` installed](http://Nodejs.org)

### 1. `package.json`

```bash
$ npm init -y
```

To create a `package.json` file with all your javascript dependencies, `-y` means to install the defaults and not answering any questions

Now the `package.json` file looks like this:

```json
{
  "name": "frontend",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

### 2. install `webpack` and `webpack-cli`

```bash
$ npm i -D webpack webpack-cli
```

`-D` means to install as _**D**evelopment_ dependencies

Now a new key added to `package.json` file:

```json
{
  // ...
  "devDependencies": {
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.11"
  }
}
```

### 3. install `babel` and `react preset`

```bash
$ npm i -D @babel/core babel-loader @babel/preset-env @babel/preset-react babel-plugin-transform-class-properties
```

- `@babel/core`: the babel core package
- `babel-loader`: helps to transpile the code
- `@babel/preset-env`: compile down ES6 to be compatible with other versions
- `@babel/preset-react`: react preset
- `babel-plugin-transform-class-properties`: handles static class properties for ES2015 and ES2016

Now the `package.json` file looks like this:

```json
{
  // ...added in step 1
  "devDependencies": {
    "@babel/core": "^7.9.0",
    "@babel/preset-env": "^7.9.5",
    "@babel/preset-react": "^7.9.4",
    "babel-loader": "^8.1.0",
    "babel-plugin-transform-class-properties": "^6.24.1"
    // ...added in step 2
  }
}
```

### 4. install `react` and `react-dom`

```bash
$ npm i react react-dom prop-types
```

- [prop-types](https://www.npmjs.com/package/prop-types):
  > Runtime type checking for React props and similar objects.

Now the `package.json` file looks like this:

```json
{
  // ...added in stop 1,2,3
  "dependencies": {
    "prop-types": "^15.7.2",
    "react": "^16.13.1",
    "react-dom": "^16.13.1"
  }
}
```

In my project, I also installed other dependencies: `$ npm i react-router-dom react-markdown react-syntax-highlighter react-spring styled-components`

And you might need to install more loaders for different file types:

- `$ npm i -D style-loader css-loader` for css files
- `$ npm i -D file-loader url-loader` for image files

### 5. create file: `.babelrc`

In order to use the previously installed _presets_ and _plugin_, we need to create a `.babelrc` file.

`~/project/frontend/.babelrc`

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-react"],
  "plugins": ["transform-class-properties"]
}
```

### 6. create file: `webpack.config.js`

Whenever you use `webpack` you'll have this config file. All we want to do here is to load `babel-loader`:

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        },
      },
    ],
  },
};
```

What the object does:

- `test`: a regular expression to look through all of the js files
- `exclude`: exclude `node_modules` folder
- `use`: the `babel-loader` to transpile our code

The above is the simplest webpack config file. In real cases, there must be more `loaders` installed, you can refer to the [offical document](https://webpack.js.org/loaders/) for more information. At the end, my `webpack.config.js` becomes:

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"],
          },
        },
      },
      {
        test: /\.(png|jpe?g|gif|svg)$/i,
        use: [
          {
            loader: "url-loader",
            options: {
              limit: 8192,
            },
          },
        ],
      },
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

### 7. write `scripts` in `package.json`

To compile our React app which lives in the `frontend` app, some scripts have to be defined:

`~/project/frontend/package.json`

```json
{
  // ...
  "scripts": {
    "dev": "webpack --mode development --watch ./src/index.js --output ./static/frontend/main.js",
    "build": "webpack --mode production ./src/index.js --output ./static/frontend/main.js"
  }
  // ...
}
```

The scripts defined above would enable us the following commands:

- `$ npm run dev`: run `webpack`, use `--mode development`, `--watch` every updates in `./src/index.js` file, and compile it to `./static/frontend/main.js` file
- `$ npm run build`: does a few extra thing for production

### 8. `$ npm run dev`

After this script is run, `main.js` file will be ready to use.

## Notes on routing

- In the MAIN `urls.py` file, include the frontend urls at then end
- Use `re_path()` method in frontend `urls.py` file to define the url pattern
- Use extra 'negative lookahead' to exclude api calls in order to avoid url clash between different django apps (in this case the `frontend` app and my `api` app which serves the api calls):`re_path(r'^(?!api)(?:.*)/?$', views.index),`

## A remember of `npm` commands

- `npm uninstall <name>` removes the module from `node_modules` but does not update `package.json`

- `npm uninstall <name> --save` also removes it from `dependencies` in `package.json`

- `npm uninstall <name> --save-dev` also removes it from `devDependencies` in `package.json`

- `npm uninstall -g <name> --save` also removes it globally
