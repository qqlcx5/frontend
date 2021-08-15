## 前言

本文可以分为三个部分：

- 第一部分：主要简述 了解 webpack 零配置 demo
- 第二部分：主要简述 了解 webpack 配置基本概念
- 第三部分：主要简述 了解常见一些插件和 loader 使用

## 什么是 Webpack

webpack 是一个现代 JavaScript 应用程序的静态模块打包工具。可以看做是模块打包机。
![clipboard.png](https://user-gold-cdn.xitu.io/2019/3/31/169cf62863c9bcdb?w=800&h=316&f=png&s=83221)
例如上图所示，它做的事情是，分析你的项目结构，找到 JavaScript 模块以及解析其他拓展语言（Scss，TypeScript 等），并将其打包浏览器能够解析的语言使用。

## webpack 构建

构建就是把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码，包括如下内容：

- 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
- 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
- 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
- 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
- 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
- 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
- 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

构建其实是工程化、自动化思想在前端开发中的体现，把一系列流程用代码去实现，让代码自动化地执行这一系列复杂的流程。 构建给前端开发注入了更大的活力，解放了我们的生产力。

## webpack 基础使用

### 初始化项目

```
mkdir wp // 创建文件夹
cd wp
npm init -y // 初始化项目
```

初始化项目后会生成`package.json`文件，我们再里面添加一个`scripts：[]`语句，如下

```

{
  "name": "wp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "webpack-dev-server --inline --progress --config webpack.config.js",//运行到开发环境
    "build": "webpack --mode production" //进行打包
  },
  "dependencies": {},
  "devDependencies": {},
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

### 安装和使用

```
npm install webpack webpack-cli -g
// OR
yarn global  add webpack webpack-cli
```

`webpack-cli` 是使用 `webpack` 的命令行工具，在 4.x 版本之后，webpack 启动命令行的代码放入了`webpack-cli` 中。如果只安装了 webpack，只能在 nodejs 环境中使用，就不能在命令行中使用。

我们在 src 文件夹下创建 `index.js`和`test.js` 文件

`./src/test.js`

```
module.exports = '我的wepback_module_test文件'
```

`./src/index.js`

```
const stra = require('./test.js')
console.log(stra)
```

保存之后在命令行工具执行 `npx webpack` 或 `npm run build` 命令，你就会发现新增了一个 dist 目录，里边存放的是 webpack 构建好的 main.js 文件。

此时 main.js 文件就是我们打包后的文件，我们创建一个 HTML 文件引入这个 main.js，然后在浏览器打开，就可以在控制台就能看到`我的wepback_module_test文件`被打印出来。

**npx 说明**

```
1. npm 5.2.0 以上版本中内置的命令：npx，类似于 npm 简化了项目开发中的依赖安装与管理
2. npx 会自动查找当前依赖包中的可执行文件

@IF EXIST "%~dp0\node.exe" (
"%~dp0\node.exe" "%~dp0\..\webpack-cli\bin\cli.js" %*
) ELSE (
@SETLOCAL
@SET PATHEXT=%PATHEXT:;.JS;=;%
node "%~dp0\..\webpack-cli\bin\cli.js" %*
)
查看从上面的源码，得到npx webpack命令其实
就是执行`node_modules\.bin\webpack-cli.cmd`路径下文件
```

上述就是 webpack4.x 零配置简单 demo。

### 缺点：

零配置的 webpack 缺少很多实际项目需要的功能，所以你还是需要一个配置文件，来完善 webpack 功能。在当前目录下新建一个`webpack.config.js`的文件，webpack 运行时默认读取当前项目下的 webpack.config.js 文件作为配置。如果需要运行指定文件名称例：`webpack.config.dev.js` 需要在命令行运行以下代码来声明以此文件作为配置文件。

```
npx webpack --config webpack.config.dev.js
```

### 了解 webpack 配置基本概念

**webpack.config.js**

```
const path=require('path');
module.exports={
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname,'dist'),
        filename:'bundle.js'
    },
    module: {},
    plugins: [],
    devServer: {}
}
```

从上面的`webpack.config.js`配置文件看到，webpack 配置由以下几部分组成。

- Entry：入口， webpack 会读取这个文件，解析其中依赖。
- Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。
- Chunk：代码块，用于代码合并与分割，常用在配置多入口页面使用。
- Loader：模块转换器，解析拓展语言（less,TypeScript）转换成浏览器能解析语言。
- Plugin：扩展插件，在 Webpack 构建流程中的特定时机注入扩展逻辑来改变构建结果。
- Output：输出结果，在 Webpack 经过一系列打包处理并得出最终代码文件。

我们可以把 wepback 看作成一个食品加工厂，Entry 就是食品原材料，Output 就是加工后的产品，
Module 就是生产的流程图，Loader 就是识别原材料是什么，然后进行解析加工。Plugin 就是 Loader 增强版可以执行 Loader 不能实现复杂的操作。

### 入口

webpack 构建时 会读取这个文件，并从它开始解析其中各种依赖，然后进行打包 bundle 的文件中。
可以通过在 webpack 配置中配置 entry 属性，来指定一个入口起点（或多个入口起点）。默认值为 ./src。

接下来我们看一个 entry 配置的简单例子：

**webpack.config.js**

```
module.exports = {
  entry: './src/index.js'
};
// 等同于
module.exports = {
  entry: {
    index: './src/index.js'
  }
}
// 或者配置多个入口
module.exports = {
  entry: {
    index: './src/index.js',
    login: './src/login.js',
    // ...
  }
}
```

### 出口(output)

告诉 webpack 在哪里输出打包后的 bundles，以及如何命名这些文件，默认值为 ./dist。
基本上，整个应用程序结构，都会被编译到你指定的输出路径的文件夹中。你可以通过在配置中指定一个 output 字段，来配置这些处理过程：

**webpack.config.js**

```
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
};
// 或者多个入口生成不同文件
module.exports = {
  entry: {
    index: './src/index.js',
    login: './src/login.js',
    // ...
  },
  output: {
    filename: '[name].[hash:8].js', // filename 输出包的名称，hash 哈希值前8位
    path: __dirname + '/dist', //__dirname当前目录下
  },
}

```

`path`模块用于处理文件和目录路径，`path`模块可以将将绝对路径处理成相对路径。

### loader

webpack 只能理解 JavaScript 和 JSON 文件。loader 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块，以供应用程序使用，以及被添加到依赖图中。

- test 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。
- use 属性，表示进行转换时，应该使用哪个 loader。

```
module: {
  rules: [
    {
      test: /\.jsx?/, // 匹配文件路径的正则表达式
      include: [    //include 手动指定必须处理的文件夹，相反exclude屏蔽不需要处理的文件夹
        path.resolve(__dirname, 'src') // 指定当前src路径下的文件需要经过 loader 处理
      ],
      use: 'babel-loader', // 指定使用的 loader
    },
  ],
}
```

### 插件(plugin)

loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

**插件接口(plugin interface) 功能极其强大，可以用来处理各种各样的任务。**

```
    plugins: [
        new htmlWebpackPlugin({
            template: "index.html", // 把index文件作为模板
            filename: "index.html", // 打包后的文件名称
            hash: true,
            minify: {
                removeAttributeQuotes: true, //去除 html的双引号
                collapseWhitespace: true //将html代码压缩成一行
            }
        })
    ]
```

### 模式(mode)

通过选择 `development`, `production` 来设置 mode 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 production。

```
module.exports = {
  mode: 'production'
};
```

**webpack.config.js**

```
let path = require('path');
let htmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  mode: 'production', //development 或 production
  entry: {
    index: './src/index.js', //入口文件
  },
  output: {
    filename: "[name].[hash:8].js", //出口文件
    path: path.resolve(__dirname, 'dist'), //__dirname当前目录, ,../,上级目录
  },
  plugins: [
    new htmlWebpackPlugin({
      template: 'index.html', // 把index文件作为模板
      filename: 'index.html', //打包后的文件名
      minify: {
        removeAttributeQuotes: true, //去除 html的双引号
        collapseWhitespace: true //压缩成一行
      }
    })
  ]
}

```

上述我们对 webpack 配置有了一定了解，接下来我们来学习搭建基本的前端开发环境吧！

## 了解常见一些插件和 loader 使用

### 关联 HTML

webpack 默认从作为入口的 .js 文件进行构建，但通常一个前端项目都是从一个页面（即 HTML）出发的，最简单的方法是，创建一个 HTML 文件，使用 script 标签直接引用构建好的 JS 文件，如：

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>webpack App</title>
  </head>
  <body>
    <script src="./dist/bundle.js"></script>
  </body>
</html>
```

但是对于在文件名中包含每次会随着编译而发生变化哈希的 webpack bundle，我们可以用`HtmlWebpackPlugin`插件简化了 HTML 文件的创建，关联打包后的`bundle.xxx.js`。

#### 安装

```
npm install html-webpack-plugin -D
// OR
yarn add html-webpack-plugin -D
```

#### 基本用法

**webpack.config.js**

将 `html-webpack-plugin` 添加到 `plugins` 列表中：

```
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');
module.exports = {
  // ...
 output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'bundle.[hash:8].js'
  },
 plugins: [
        new HtmlWebpackPlugin({
         minify: {
            removeAttributeQuotes:true
        },
        template: './index.html',
        filename:'index.html'
    })]
}
```

- `minify` 是对 html 文件进行压缩，`removeAttrubuteQuotes`是去掉属性的双引号
- `hash` 引入产出资源的时候加上查询参数，值为哈希避免缓存
- `template` 把当前目录下`index.html`作为模板
- `filename` 打包后产出的文件名

这将会产生一个包含以下内容的文件 `dist/index.html`：

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>webpack App</title>
  </head>
  <body>
    <script src="src=index.7f7a3d17.js"></script>
  </body>
</html>
```

这样配置好之后，通过 html-webpack-plugin 就可以将我们的页面和构建 JS 关联起来，从页面开始开发。

### 配置开发服务器

`webpack-dev-server` 为你提供了一个简单的静态服务来进行开发。

#### 安装

```
npm install webpack-dev-server –D
// OR
yarn add webpack-dev-server –D
```

#### 基本用法

**webpack.config.js**

```
module.exports = {
    mode: "development", //development 或 production
    devServer: {
        contentBase: path.join("dist"), //告诉服务器从哪里提供内容
        compress: true, //为所服务的一切启用gzip压缩
        port: 9000  //本地服务器端口号
    }
```

然后我们可以在`package.json`添加直接运行 `dev server` 的 `script：`

**package.json**

```
{
  "name": "wp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
     "dev": "webpack-dev-server --inline --progress --config webpack.config.js",
     "build": "webpack --mode production"
  },
  "dependencies": {},
  "devDependencies": {},
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

其实`npm init -y`时候，我就添加`"scripts"`中，在这顺便在提醒一下。

接下来尝试着运行 `npm run dev` 或者 `yarn dev`，然后就可以访问 `http://localhost:9000/` 来查看你的页面了。

默认是访问 `index.html`，如果是其他页面要注意访问的 URL 是否正确。

### 支持加载 css 文件

上述说过 webpack 不支持 CSS 代码，所以我们需要 loader，让 webpack 能解析处理 CSS 代码。

**Loader**

通过使用不同的 Loader，Webpack 可以要把不同的文件都转成 JS 文件，比如 CSS、ES6/7、JSX 等

- test：匹配处理文件的扩展名的正则表达式
- use：loader 名称，就是你要使用模块的名称
- include/exclude:手动指定必须处理的文件夹或屏蔽不需要处理的文件夹

我们需要安装两个 loader

```
npm install css-loader style-loader –D
// OR
yarn add css-loader style-loader –D
```

- css-loader 负责解析 CSS 代码，主要是为了处理 CSS 中的依赖，例如 @import 和 url() 等引用外部文件的声明；
- style-loader 会将 css-loader 解析的结果转变成 JS 代码，运行时动态插入 style 标签来让 CSS 代码生效。

接下来在`webpack.config.js`添加规则

```
module: {
        rules: [
            {
                test: /\.css/,
                include: path.resolve(__dirname,'src'),
                exclude: /node_modules/,
                use:['style-loader','css-loader']
            }
        ]
    }
```

我们在 src 下创建一个 index.css 文件，随便添加一些样式，并在 index.js 中引用它，然后进行打包。

```
import "./index.css";
OR
require("./index.css");
```

打包后你会发现引入的 index.css 在浏览器中能正常使用了。

### 分离 CSS

先简单解释一下为何要把 CSS 文件分离出来，而不是直接一起打包在 JS 中。最主要的原因是我们希望更好地利用缓存。

假设我们原本页面的静态资源都打包成一个 JS 文件，加载页面时虽然只需要加载一个 JS 文件，但是我们的代码一旦改变了，用户访问新的页面时就需要重新加载一个新的 JS 文件。有些情况下，我们只是单独修改了样式，这样也要重新加载整个应用的 JS 文件，相当不划算。

还有一种情况是我们有多个页面，它们都可以共用一部分样式（这是很常见的，CSS Reset、基础组件样式等基本都是跨页面通用），如果每个页面都单独打包一个 JS 文件，那么每次访问页面都会重复加载原本可以共享的那些 CSS 代码。如果分离开来，第二个页面就有了 CSS 文件的缓存，访问速度自然会加快。虽然对第一个页面来说多了一个请求，但是对随后的页面来说，缓存带来的速度提升相对更加可观。

因此当我们考虑更好地利用缓存来加速静态资源访问时，会尝试把一些公共资源单独分离开来，利用缓存加速，以避免重复的加载。除了公共的 CSS 文件或者图片资源等，当我们的 JS 代码文件过大的时候，也可以用代码文件拆分的办法来进行优化。

因为 CSS 的下载和 JS 可以并行，当一个 HTML 文件很大的时候，我们可以把 CSS 单独提取出来加载。这时我们就需要`MiniCssExtractPlugin`插件来实现这功能。

#### 兼容性

`MiniCssExtractPlugin`建立在新的 webpack 4x 功能（模块类型）之上，并且需要 webpack 4 才能工作。

好在 webpack 5 已经内置了`CSS minimizer` 和 `JS minimizer`

**安装依赖模块**

```
npm install  mini-css-extract-plugin --save-dev
OR
yarn add mini-css-extract-plugin --save-dev
```

#### 基本用法

`webpack.config.js`

```
let MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  // ...
   module: {
   rules: [
            {
                test: /\.css$/,
                include: path.resolve(__dirname,'src'),
                exclude: /node_modules/,
                use: ['style-loader',{ loader: MiniCssExtractPlugin.loader },'css-loader']
            }
        ]
----
    plugins: [
        new MiniCssExtractPlugin({
            filename: "css/style.css"
        })
    ]
```

这样配置好之后，通过 `MiniCssExtractPlugin`会自动 将引入 js 的 CSS 代码分离生成 css/style.css 的文件，然后`link`标签的形式自动引入到构建`index.html`中。

### 自动添加 CSS3 属性前缀

为了浏览器的兼容性，有时候我们必须加入`-webkit`,`-ms`,`-o`,`-moz`这些前缀

- `Trident`内核：主要代表为 IE 浏览器, 前缀为-ms
- `Gecko`内核：主要代表为 Firefox, 前缀为-moz
- `Presto`内核：主要代表为 Opera, 前缀为-o
- `Webkit`内核：产要代表为 Chrome 和 Safari, 前缀为-webkit

**安装依赖模块**

```
 npm install postcss-loader autoprefixer --save-dev
 OR
 yarn add postcss-loader autoprefixer --save-dev
```

在`webpack.config.js`同级目录下新建`postcss.config.js`文件

```
module.exports={
    plugins:[require('autoprefixer')]
}
```

**webpack.config.js**

```
module.exports = {
  // ...
   module: {
   rules: [
            {
                test: /\.css$/,
                include: path.resolve(__dirname,'src'),
                exclude: /node_modules/,
                use: ['style-loader',{ loader: MiniCssExtractPlugin.loader },'css-loader','postcss-loader']
            }
          ]
```

### 效果

**初始 CSS**

```
body {
  transform: rotateY(180deg);
  background-color: antiquewhite;
}
```

**转换后**

```
body {
  -webkit-transform: rotateY(180deg);
          transform: rotateY(180deg);
  background-color: antiquewhite;
}
```

### 压缩 JS 和 CSS

**安装依赖模块**

```
npm install uglifyjs-webpack-plugin optimize-css-assets-webpack-plugin --save
OR
yarn add uglifyjs-webpack-plugin optimize-css-assets-webpack-plugin --save
```

**配置 webpack.config.js**

```
let UglifyJsPlugin = require("uglifyjs-webpack-plugin");
let OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
module.exports = {
    mode: 'production',
    optimization: {
        minimizer: [
            new UglifyJsPlugin({
                cache: true,//启动缓存
                parallel: true//启动并行压缩
            }),
            //压缩css资源的
            new OptimizeCSSAssetsPlugin({})
        ]
    },
```

**转换后效果**

```
body{-webkit-transform:rotateY(180deg);transform:rotateY(180deg);background-color:#faebd7}body h3{color:#bdb76b}
!function(t){var r={};function o(e){if(r[e])return r[e].exports;var n=r[e]={i:e,l:!1,exports:{}};return t[e].call(n.exports,n,n.exports,o),n.l=!0,n.exports}o.m=t,o.c=r,o.d=function(e,n,t){o.o(e,n)||Object.defineProperty(e,n,{enumerable:!0,get:t})},o.r=function(e){"undefined"!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},o.t=function(n,e){if(1&e&&(n=o(n)),8&e)return n;if(4&e&&"object"==typeof n&&n&&n.__esModule)return n;var t=Object.create(null);if(o.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:n}),2&e&&"string"!=typeof n)for(var r in n)o.d(t,r,function(e){return n[e]}.bind(null,r));return t},o.n=function(e){var n=e&&e.__esModule?function(){return e.default}:function(){return e};return o.d(n,"a",n),n},o.o=function(e,n){return Object.prototype.hasOwnProperty.call(e,n)},o.p="",o(o.s=0)}([function(e,n,t){new function e(){!function(e,n){if(!(e instanceof n))throw new TypeError("Cannot call a class as a function")}(this,e),alert("class")};t(1)},function(e,n,t){}]);
```

### CSS 预处理器

**安装依赖模块**

```
npm isntall less less-loader --save-dev //less
npm isntall node-sass sass-loader --save-dev //sass
npm isntall stylus stylus-loader --save-dev //stylus
OR
yarn add less less-loader --save-dev //less
yarn add node-sass sass-loader --save-dev //sass
yarn add stylus stylus-loader --save-dev //stylus
```

**配置 webpack.config.js**

以`less`为例：

```
module.exports = {
  // ...
  module: {
    rules: [
       {
        test: /\.less/,
        include: path.resolve(__dirname,'src'),
        exclude: /node_modules/,
        use: [{
            loader: MiniCssExtractPlugin.loader,
        },'css-loader','less-loader']
    }],
  }
}
```

**初始 Less**

```
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;

#header {
  color: @light-blue;
}

```

**输出**

```
#header {
  color: #6c94be;
}
```

### 支持图片

**安装依赖模块**

```
npm isntall file-loader url-loader -D
OR
yarn add file-loader url-loader -D
```

**说明**

- `file-loader` 解决 CSS 等文件中的引入图片路径问题
- `url-loader` 当图片小于 limit 的时候会把图片 BASE64 编码，大于 limit 参数的时候还是使用 file-loader 进行拷贝

**情况一：JS 中引入图片**

```
let logo=require('./images/logo.png');
let img=new Image();
img.src=logo;
document.body.appendChild(img);
```

**webpack.config.js**

```
{
  test:/\.(jpg|png|bmp|gif|svg|ttf|woff|woff2|eot)/,
    use:[
    {
       loader:'url-loader',
       options:{limit:4096}
    }
  ]
}
```

**情况二：CSS 中引入图片**

```
.logo{
    width:355px;
    height:133px;
    background-image: url(./images/logo.png);
    background-size: cover;
}
```

**HTML**

```
<div class="logo"></div>
```

### 对图片进行压缩和优化

**安装依赖模块**

```
npm install image-webpack-loader --save-dev
OR
yarn add image-webpack-loader --save-dev
```

**基本用法**

```
rules: [{
  test: /\.(gif|png|jpe?g|svg)$/i,
  use: [
    'file-loader',
    {
      loader: 'image-webpack-loader',
      options: {
        mozjpeg: {
          progressive: true,
          quality: 65
        },
        // optipng.enabled: false will disable optipng
        optipng: {
          enabled: false,
        },
        pngquant: {
          quality: '65-90',
          speed: 4
        },
        gifsicle: {
          interlaced: false,
        },
        // the webp option will enable WEBP
        webp: {
          quality: 75
        }
      }
    },
  ],
}]
```

### 转义

Babel 主要用于将 ES6/ES7/JSX 的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。
**安装依赖模块**

```
npm isntall babel-loader @babel/core @babel/preset-env  --save-dev
OR
yarn add babel-loader @babel/core @babel/preset-env --save-dev
```

在`webpack.config.js`目录下新建`.babelrc`文件

```
{
  "presets": [
    "@babel/preset-env"
  ]
}
```

**webpack.config.js**

```
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.js?$/,
        include: [
          path.resolve(__dirname, 'src'), // src 目录下的才需要经过 babel-loader 处理
        ],
        loader: 'babel-loader',
      },
    ],
  },
}
```

在`index.js`入口文件添加以下代码

```
class My {
    constructor() {
    console.log("class my");
    }
}
let my = new My();

```

命令行输入`npm run dev` 或 `yarn dev`，在浏览器打开，可见控制台`class my`被打印出来。

**babel-loader 缺陷**

Babel 对一些公共方法使用了非常小的辅助代码，比如 `_extend`。默认情况下会被添加到每一个需要它的文件中
你可以引入 Babel runtime 作为一个独立模块，来避免重复引入。
**安装依赖模块**

```
npm install  @babel/plugin-transform-runtime --save-dev
npm install  @babel/runtime --save
OR
yarn add  @babel/plugin-transform-runtime --save-dev
yarn add install  @babel/runtime --save
```

**修改`.babelrc`文件**

```
{
  "presets": ["@babel/preset-env"],
  "plugins": [
   [
         "@babel/plugin-transform-runtime"
    ]
  ]
}
```

`webpack`打包的时候，会自动优化重复引入公共方法的问题。

### 参考文档

- [webpack 官网](https://webpack.docschina.org/)
- [用 webpack 定制前端开发环境](https://juejin.im/book/6844733709808041992)
- 珠峰架构课
