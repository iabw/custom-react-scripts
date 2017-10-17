# Custom custom-react-scripts

### ⚠️ Warning ⚠️

This is not a real release. If you found this repo on npm, **please do not depend on it**.

If you're sure this is something you want to be doing (it probably isn't!), reference the most recent commit on master and create your own fork.

### References

- CRA README - https://github.com/mattphillips/create-react-app/blob/1270b587196b9fcb74db20fb472029df39be30cb/packages/react-scripts/template/README.md
- Introduction to Custom React Scripts - https://medium.com/@kitze/configure-create-react-app-without-ejecting-d8450e96196a
- Extending CRA - https://medium.com/@shubheksha/tweaking-configuration-for-react-scripts-in-create-react-app-d91e9d03a42f
- Another 'extending CRA' overview - https://medium.com/@denis.zhbankov/maintaining-a-fork-of-create-react-app-as-an-alternative-to-ejecting-c555e8eb2b63
- Overview of extending CRA's webpack config without ejecting or forking - https://medium.com/@viankakrisna/extending-create-react-app-webpack-config-c70828593c96
- Large example of the above behavior - https://gist.github.com/dthpth/d13f190ad03c1075dd3e300f04e8da73
- Master ticket in CRA where such issues have been getting discussed - https://github.com/facebookincubator/create-react-app/issues/682

### Managing a Top-level Webpack Config

You're going to need a top-level webpack that will extend custom-react-scripts's webpack configs.

Here's an example that will allow importing files from outside src

```
const webpack = require("webpack");
const path = require("path");

const devMode = process.env.DEV_MODE || false;

const reactScriptsConfig = require(`custom-react-scripts/config/webpack.config.${devMode ? 'dev' : 'prod'}`);

// Folder needs to be inserted as both a Babel target and a resolve alias
const notInSrc = path.resolve( __dirname + "/notInSrc" );

// Look at this **AMAZING** hack to find the Babel module
// This is by far the grossest part of this whole process.
const babelModuleRule = reactScriptsConfig.module.rules[1].oneOf.find(function(rule){
    return rule.options && rule.options.babelrc !== undefined;
});

// The Babel module's include is sometimes only a string, so we concat off of notInSrc
babelModuleRule.include = [notInSrc].concat(babelModuleRule.include);

// Actually extending the CRA webpack config
module.exports = Object.assign({}, reactScriptsConfig, {
    resolve: Object.assign({}, reactScriptsConfig.resolve, {
        alias: Object.assign({}, reactScriptsConfig.resolve.alias, {
            notInSrc: notInSrc
        }),
        // Over-riding ModuleScopePlugin that doesn't let us import modules from outside src
        plugins: [],
    }),
});
```

# ☢ custom-react-scripts ☢
Latest version of original react-scripts: **1.0.11**

### ⚠️ Disclaimer:
> This is **not** a fork of ```create-react-app```. It's just a fork of ```react-scripts``` with simple babel/webpack modifications that can toggle extra features.

The reason for this fork's existence is explained better in [this Medium article](https://medium.com/@kitze/configure-create-react-app-without-ejecting-d8450e96196a).

### 💡Features:
* Decorators
* babel-preset-stage-0
* Less
* Sass
* CSS modules
* Sass modules
* Less modules
* Stylus modules

**the features are optional and can be turned on/off individually*

### ❔How to use it
```create-react-app my-app --scripts-version custom-react-scripts```

Modify the ```.env``` file in the root of the generated project, and add any of the configuration options below 👇 to enable that feature.

The generated project comes with every option turned on by default, but you can remove them at any time by removing the options from the ```.env``` file.

### 📝 Configuration options

#### Styling
- ```REACT_APP_SASS=true``` - enable SASS support
- ```REACT_APP_LESS=true``` - enable LESS support
- ```REACT_APP_STYLUS=true``` - enable Stylus support
- ```REACT_APP_CSS_MODULES=true``` - enable CSS modules
- ```REACT_APP_SASS_MODULES=true``` - enable Sass modules
- ```REACT_APP_LESS_MODULES=true``` - enable Less modules
- ```REACT_APP_STYLUS_MODULES=true``` - enable Stylus modules

Note: to use modules the file must be named in the following format: ```$name.module.$preprocessorName```.

For example ```styles.module.css``` or ```header.module.sass``` or ```footer.module.less```, etc. Files that are not prefixed with module will be parsed normally.

#### Babel
- ```REACT_APP_BABEL_STAGE_0=true``` - enable stage-0 Babel preset
- ```REACT_APP_DECORATORS=true``` - enable decorators support

#### Other
- ```REACT_APP_WEBPACK_DASHBOARD=true``` - Enables connection to the [webpack-dashboard](https://github.com/FormidableLabs/electron-webpack-dashboard) Electron app (the app must be installed on local machine)

### 🤔 Why?
The ```create-react-app``` app doesn't allow user configuration and modifications for few reasons:

* Some of the babel presets and plugins that people might use are experimental.  If they're used in a project and then they don't make it in the ES spec, they will break backwards compatibility.
* It's hard to maintain code for all of these custom configurations that people want to use.

But people still want to use some of these features, and they're either ejecting their CRA app, or just don't use ```create-react-app``` because they're *just* missing **X** feature.

So instead of [searching npm](https://www.npmjs.com/search?q=react-scripts) for a ```react-scripts``` fork with the **X** feature you need, this fork provides support for all of these extra features with simply adding a line in the ```.env``` config.

### How does it work?
The CRA team recently [added support](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-development-environment-variables-in-env) for an ```.env``` file in the root of the generated CRA project.

From the original readme:
> To define permanent environment vairables, create a file called .env in the root of your project:
> ```REACT_APP_SECRET_CODE=abcdef```

I just added support for extra environment variables that actually turn on certain plugins, babel plugins, presets, and loaders in the webpack and babel configs of ```react-scripts```.

### Future plans

I will put all of my efforts into supporting this fork to be always on par with features with the newest ```create-react-app``` and ```react-scripts``` versions.
