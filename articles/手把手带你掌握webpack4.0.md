# 手把手带你掌握webpack4.0 #
## 1.webpack究竟是什么？ ##
1. webpack 是一个模块打包工具。
2. npx 的原理就是运行的时候，会到node_modules/.bin路径和环境变量$PATH里面，检查命令是否存在。

## 2.什么是模块打包工具？ ##
1. webpack支持的模块方法：ES6、CommonJS、AMD、标签模块(Labeled Modules)、 webpack 特定方法。
2. 模块规范：ES Module、CommonJS(Node.js使用该规范)、CMD、AMD。
3. webpack 通过 loader 可以支持各种语言和预处理器编写模块。

## 3.webpack的正确安装方式 ##
1. 安装步骤：
    - 安装Node.js。
    - `npm init -y` 初始化一个项目
    - `npm install webpack webpack-cli -D`(项目内安装webpack)
2. `npx webpack -v` 检查当前项目webpack版本。
3. `npm info webpack` 查看webpack历史版本信息。
4. `npm install webpack@4.16.5 webpack-cli -D` 安装指定版本webpack。
5. package.json配置文件：
    - `private: true || false`(防止意外发布私有库,如果你设置"private": true，npm就不会发布它。)
    - main字段配置一个文件名指向模块的入口程序。（一般模块默认为index.js文件，如果设置了就以设置的文件为入口。）

## 4.使用webpack的配置文件 ##
1. `npx webpack` 命令默认使用webpack.config.js文件进行打包，使用命令`npx webpack --config xxx.js`可以指定配置文件进行打包。
2. webpack打包的三种方式
    - `webpack index.js`全局安装webpack
    - `npx webpack index.js`局部安装webpack
    - `npm run bundle > webpack`package.json文件script中书写命令


``` 
            const path = require('path')
            
            module.exports = {
                entry: './src/index.js',
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'bundle')
                }
            }

```

## 5.浅析webpack打包输出内容 ##
1. mode:"production"（代码压缩） | "development" （代码不压缩）| "none"。


``` 
Hash: ba2c90eb3d133c49c4c5 //本次打包所对应的唯一hash值
Version: webpack 4.30.0 // 本次打包使用的webpack版本
Time: 147ms //本次打包耗时
Built at: 2019-05-05 08:03:47
  Asset // 打包出来的文件     Size // 打包出的文件大小 Chunks // 每一个js文件对应的ID            Chunk Names // 每一个js文件对应的名字
main.js  1.39 KiB       0  [emitted]  main
Entrypoint main = main.js // 打包的入口文件
[0] ./src/index.js 255 bytes {0} [built]
[1] ./src/header.js 207 bytes {0} [built]
[2] ./src/content.js 210 bytes {0} [built]
[3] ./src/footer.js 207 bytes {0} [built] 
```

## 6.什么是loader ##
1. 什么是loader？
- webpack不能识别不是.js结尾文件，只能通过配置loader来让webpack可以识别。

## 7.使用loader打包静态资源（图片篇） ##
1. url-loader 功能类似于 file-loader，但是在文件大小（单位 byte）低于limit指定的限制时，会将图片转化为base64格式。
2. 官方文档：[https://www.webpackjs.com/loaders/file-loader/](https://www.webpackjs.com/loaders/file-loader/ "file-loader")    [https://www.webpackjs.com/loaders/url-loader/](https://www.webpackjs.com/loaders/url-loader/ "url-loader")

```
module: {
        rules: [
            {
                test: /\.jpg$/,
                use: [
                    { 
                        loader: 'url-loader',
                        options: {
                            name: '[name].[hash].[ext]', //placeholders占位符 文件名.hash值.文件扩展名
                            outputPath: 'assest/', //打包后文件文件输出目录
                            limit: 102400, //小于102400kb时，打包为base64格式，大于时打包为文件
                        }
                    }
                ]
            }
        ]
    },
```

## 8.使用loader打包静态资源（样式篇-上） ##
1. 使用多个loader时，webpack遵循从右到左，从下到上的顺序打包。
2. 样式加浏览器前缀可以使用postcss-loader中的autoprefixer插件。

```
{
          test: /\.scss$/,
          use: ['style-loader', 'css-loader', 'sass-loader', 'postcss-loader']
}
```
## 9.使用loader打包静态资源（样式篇-下） ##
1.postcss-loader中的postcss-import插件可以解决@import规则路径，但要放在plugins中的第一列使用。
2.字体图标打包用file-loader。
3.css-loader配置项中modules用来开启css模块，importLoaders配置css-loader 作用于 @import 的资源之前有多少个 loader。
```
{
                test: /\.(eot|svg|ttf|woff)$/,
                use: [
                    {
                        loader: 'file-loader',
                        options: {
                            // name: '[name].[ext]', //placeholders占位符 文件名.hash值.文件扩展名
                            outputPath: 'assest/', //打包后文件文件输出目录
                        }
                    }
                ]
            }
```

```
{
                test: /\.scss$/,
                use: [
                    'style-loader',
                    {
                        loader: 'css-loader',
                        options: {
                            importLoaders: 2, //配置css-loader 作用于 @import 的资源之前有多少个 loader
                            modules: true, //css模块
                        }
                    },
                    'sass-loader',
                    'postcss-loader'
                ]
            }
```
## 10.使用plugins让打包更便捷 ##
1. plugin可以在webpack运行的某个时刻帮助你实现某些功能。
2. html-webpack-plugin会在打包后生成一个html文件并自动帮你引入打包后的js文件。
3. clean-webpack-plugin会在打包前帮你删除打包后文件所在的文件夹。

```
plugins: [
        new HtmlWebpackPlugin({
            template: './index.html'
        }),
        new CleanWebpackPlugin()
    ]
```

## 11.Entry 与 Output 的基础配置 ##
```
entry: {
        dist: './index.js',
        sub: './index.js'
    },
output: {
        publicPath: 'http://',
        filename: '[name].js',
        path: path.resolve(__dirname, 'dist')
    },
```

## 12.Source Map的配置 ##
1. 什么是source map？
    
    - Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。
2. webpackd devtool配置5个关键字说明。
    - eval： 使用eval包裹模块代码
    - source-map： 产生.map文件
    - cheap：不包含列信息，也不包含loader的sourcemap
    - module： 包含loader的sourcemap（比如jsx to js ，babel的sourcemap）
    - inline：将.map作为DataURI嵌入，不单独生成.map文件。
3. eval模式
    - 使用eval将webpack中每个模块包裹，然后在模块末尾添加模块来源//# souceURL， 依靠souceURL找到原始代码的位置。包含eval关键字的配置项并不单独产生.map文件，依靠sourceURL来定位原始代码。

    ```
    eval("consoee.log(666)\n\n//# sourceURL=webpack:///./index.js?");
    ```

4. source map模式
    - 都会产生一个.map文件，该文件保存有原始代码与运行代码的映射关系， 浏览器可以通过它找到原始代码的位置。（注：包含inline关键字的配置项也会产生.map文件，但是这个map文件是经过base64编码作为DataURI嵌入）。使用.map文件的方式来定位。
    - eval-source-map是eval和source-map的组合，可知使用eavl语句包括模块，也产生了.map文件。webpack将.map文件作为DataURI替换eval模式中末尾的//# souceURL。
5. source map文件基本格式。
    ```
    {
    　　　　version : 3, //Source map的版本，目前为3。
    　　　　file: "out.js", //转换后的文件名。
    　　　　sourceRoot : "", //转换前的文件所在的目录。如果与转换前的文件在同一目录，该项为空。
    　　　　sources: ["foo.js", "bar.js"], //转换前的文件。该项是一个数组，表示可能存在多个文件合并。
    　　　　names: ["src", "maps", "are", "fun"], //转换前的所有变量名和属性名。
    　　　　mappings: "AAgBC,SAAQ,CAAEA" //记录位置信息的字符串，分成三层。
    　　}
    ```
6. mappings属性
    - 第一层是行对应，以分号（;）表示，每个分号对应转换后源码的一行。所以，第一个分号前的内容，就对应源码的第一行，以此类推。
    - 第二层是位置对应，以逗号（,）表示，每个逗号对应转换后源码的一个位置。所以，第一个逗号前的内容，就对应该行源码的第一个位置，以此类推。
    - 第三层是位置转换，以VLQ编码表示，代表该位置对应的转换前的源码位置。
7. webpack devtool 常用配置
    - development模式：cheap-module-eval-source-map
    - production模式：cheap-module-source-map
8. 推荐阅读博客
    - [Webpack中sourcemap的配置](http://www.cnblogs.com/axl234/p/6500534.html)
    - [JavaScript Source Map 详解](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)

## 13.使用webpackDevServer提升开发效率 ##
1. 为什么要使用webpack-dev-server？
    - 要发ajax请求文件必须是以http协议打开，file协议打开的文件不能发送ajax请求。


2. webpack 中有几个不同的选项，可以帮助你在代码发生变化后自动编译代码：
    - webpack's Watch Mode //观察者模式
    - webpack-dev-server
    - webpack-dev-middleware
    ```
    "scripts": {
        "watch": "webpack --watch",
        "start": "webpack-dev-server",
        "server": "node server.js"
      },
    ```

3. 如何自己实现webpack-dev-server?

    ```
    const express = require('express')
    const webpack = require('webpack')
    const webpackDevMiddleware = require('webpack-dev-middleware')
    const config = require('./webpack.config.js')
    const complier = webpack(config)
    
    const app = express()
    app.use(webpackDevMiddleware(complier, {}))
    
    app.listen(3000, () => {
    	console.log("server is running")
    })
    ```

## 14.Hot Module Replacement 热模块更新 ##
1. 模块热替换(HMR - Hot Module Replacement)功能会在应用程序运行过程中替换、添加或删除模块，而无需重新加载整个页面。
2. webpack-dev-server会把打包生成的内容放在电脑内存里，这样可以提高它的打包速度。
3. hot 和 hotOnly 的区别是在某些模块不支持热更新的情况下，前者会自动刷新页面，后者不会刷新页面，而是在控制台输出热更新失败。
4. 本质上如果想实现HMR功能，页面上就一定要写下面的代码，但是由于一些loader等文件帮我们实现了这个代码，所以我们不用自己手动写。
    ```
    if(module.hot) {
	module.hot.accept('./number.js', () => {
		    
     document.body.removeChild(document.getElementById('number'))
		number()
	  })
    }
    ```

## 15.使用Babel处理ES6语法 ##
1.什么是Babel？
- Babel 是一个 JavaScript 编译器，Babel 是一个工具链，主要用于将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。

```
webpack.config.js文件
{
    test: /\.js$/,
    exclude: /node_modules/, //排除node_modules文件中的js文件
    loader: "babel-loader",
}
.babelrc文件
{
    presets: [
        [
            "@babel/preset-env", 
            {
                useBuiltIns:'usage',
                corejs: 3,
            }
        ],
        "@babel/preset-react" //编译react语法
    ]
}
```
## 16.Tree Shaking概念详解 ##
1. 什么是Tree Shaking?
    - 当引入一个模块的时候，只引入其中使用的模块，把模块里没有引入的东西都剔除掉。
    - 只支持ES Module的引入（静态的引入），不支持CommonJS的引入方式（动态的引入）。

2. `mode: 'development'`开发环境里Tree Shaking默认是不开启的，需要在webpack.config.js文件中使用以下配置。(即使在开发环境下使用Tree Shaking，也不会把没有引入的模块代码删除，只会在打包后的文件注释一下说明只使用了那个模块)
    ```
    optimization: {
        usedExports: true  //开启Tree Shaking
    }
    ```
3. package.json文件sideEffects配置项
    - `"sideEffects": false,`表示所有引入的模块都使用Tree Shaking，没有使用引入模块的文件将会被剔除掉。
    - `"sideEffects": ["*.css"],`表示对所有以.css为后缀结尾的文件不使用Tree Shaking。

## 17.Production和Development模式的区分打包 ##
1. `webpack-merge`可以合并两个webpack配置文件。

```
//提取公共配置到webpack.common.js文件中，通过webpack-merge合并公共配置到webpack.prod.js文件中，打包开发环境安装包。
const CleanWebpackPlugin = require('clean-webpack-plugin')
const merge = require('webpack-merge')
const commonConfig = require('./webpack.common.js')

const prodConfig = {
    mode: 'production',
    devtool: 'cheap-module-eval-source-map',
    // development模式：cheap-module-eval-source-map
    // production模式：cheap-module-source-map
    plugins: [
        new CleanWebpackPlugin(),
    ],
}

module.exports = merge(commonConfig, prodConfig)
```

## 18.Code Splitting ##
1. Code Splitting是什么，有什么好处？
    - Code Splitting和webpack无关，它是单独的一个概念，用来提升项目的性能。
    - Code Splitting就是把代码分割为几个不同的文件。
    ```
    entry: {
        loadsh: './lodash.js',
        main: './index.js', //注意：此处顺序就为index.html引入顺序
    },
    ```

2. webpack中如何实现代码分割？
    1. 同步代码：只需要在webpack配置文件中作optimization的配置。
    ```
        optimization: {
          splitChunks: {
            chunks: 'all' //同步代码实现webpack代码分割
            }
        }
    ```

    2. 异步代码（指import语法引入的异步组件）：异步代码，无需作任何配置，会自动进行代码分割。

    ```
        //第一种 同步代码
        // import _ from 'lodash'

        // ducument.write(_.join(['a','b'], '.'))

        //第二种 异步代码
        function getComponent() {
            return import('lodash').then(({default: _}) => {
            var element = document.createElement('div')
            element.innerHTML = _.join(['a','b'], '.')
            return element
        })
    }

    getComponent().then(element => {
        document.body.appendChild(element)
    })

    ```

## 19.SplitChunksPlugin配置参数详解 ##
1. 如何使用SplitChunksPlugin插件？
    - SplitChunksPlugin是webpack自带的插件，无需安装，直接在webpack配置文件中进行配置。
```
//下面配置都是默认配置，直接写成上文配置即可
optimization: {
        splitChunks: {
            chunks: "all", //initial 同步代码 async  异步代码  all 所有,默认值为 async
            minSize: 30000,  //大于30000字节的才去做代码分割
            minChunks: 1, //打包生成的文件被最少被几个chunk文件依赖才进行代码分割
            maxAsyncRequests: 5, //同时加载的请求不能超过5个
            maxInitialRequests: 3, //入口文件加载的文件不能超过3个
            automaticNameDelimiter: '~', //组和文件用~连接作为文件名
            name: true, //配置不变
            cacheGroups: { // 缓存组  打包同步代码时，会继续执行cacheGroups里面的内容
                vendors: {
                    test: /[\\/]node_modules[\\/]/, //node_modules内引入的文件
                    priority: -10, //优先级 执行优先级高的组
                    filename: 'vendors.js' //分割后的文件名
                },
                default: {
                    minChunks: 2,
                    priority: -20,
                    reuseExistingChunk: true //如果一个模块已经被打包过了，那么会忽略这个模块
                }
            }
        }
    }
```

## 20.Lazy Loading懒加载，Chunk是什么? ##
1. 什么是Lazy Loading？
    - 在webpack中指的是通过import这个语法，异步加载组件，只有在使用的时候加载文件。
    
    ```
    async function getComponent() {
        const { default: _ } = await import(/* webpackChunkName:"lodash" */ 'lodash')
        var element = document.createElement('div')
        element.innerHTML = _.join(['a', 'b'], '.')
        return element
    }
    
    document.addEventListener('click', () => {
        getComponent().then(element => {
            document.body.appendChild(element)
        })
    })
    ```
2. 什么是Chunk？
    
    - 在webpack打包的过程中，生成的每一个js文件都是一个chunk。

## 21.打包分析，Preloading，Prefetching ##
1. 如何进行打包分析？
    1. 在package.json文件中添加`webpack --profile --json > stats.json`命令。
    2. 使用分析工具进行分析。[webpack-chart](https://alexkuz.github.io/webpack-chart/)

2. 为什么webpack默认异步代码才使用代码分割？
    1. 同步代码进行代码分割只能增加缓存，对性能影响是有限的。
    2. 异步代码通过页面的一些交互操作时进行加载文件，这样可以提高网站首页渲染时间。（可以在浏览器上通过ctrl+shift+p使用Coverage查看加载的js文件的代码覆盖率。）

3. preloading和prefetching
    - 在link标签中使用
        ```
        <link rel="preload" href="bg-image-narrow.png" as="image" media="(max-width: 600px)">
        <link rel="preload" href="bg-image-wide.png" as="image" media="(min-width: 601px)">
        ```
    - 在webpack中使用
        ```
        import(/* webpackPrefetch: true */ 'LoginModal');
        import(/* webpackPreload: true */ 'ChartingLibrary');
        ```
    - 区别
        
        - preloading会立刻加载，prefetching会先加载主模块，然后在浏览器闲置下来的时候进行加载。

## 22. CSS文件的代码分割 ##
1. chunkFilename
    ```
    output: {
        // publicPath: '/',
        filename: '[name].js',
        chunkFilename: '[name].chunk.js', //在打包后入口文件中引入的模块文件名使用chunkFilename配置
        path: path.resolve(__dirname, '../dist')
    },
    ```

2. 如何对样式文件进行代码分割。
    ```
    1. 在plugins中引入MiniCssExtractPlugin插件
        plugins: [
            new MiniCssExtractPlugin({}),
        ],
    2. 用MiniCssExtractPlugin.loader代替style-loader
        module: {
        rules:  [
            {
                test: /\.scss$/,
                use: [
                    MiniCssExtractPlugin.loader,
                    {
                        loader: 'css-loader',
                        options: {
                            importLoaders: 2
                        }
                    },
                    'sass-loader',
                    'postcss-loader'
                ]
            },
            {
                test: /\.css$/,
                use: [
                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    'postcss-loader'
                ]
            },
        ],
    }
    3. 通过optimize-css-assets-webpack-plugin对样式文件进行压缩
        optimization: {
        minimizer: [
            new OptimizeCSSAssetsPlugin({})
        ],
    },
    ```

## 23.webpack与浏览器缓存(Caching) ##
1. `performance: false`阻止webpack打包时提示性能问题。
2. 在打包文件内容有变化时，contenthash值会自动变化。
    ```
    output: {
        filename: '[name].[contenthash].js',
        chunkFilename: '[name].[contenthash].chunk.js',
    },
    ```

## 24.Shimming ##
1. 什么是Shimming?
    - 改变webpack打包的默认行为，或者实现webpack打包实现不了的效果。
    ```
    new webpack.ProvidePlugin({
                        $: 'jquery', //相当于import $ from 'jquery
                        _: 'lodash',
                        _join: ['lodash', 'join'],  //相当于import { join } from 'lodash
                    })
    //通过imports-loader使项目中的this指向window
    {
            test: /\.js$/,
            exclude: /node_modules/, //排除node_modules文件中的js文件
            use: [
                'babel-loader',
                'imports-loader?this=>window'
            ]
        }
    ```

## 25.环境变量的使用 ##
```
1. package.json文件传递
"scripts": {
    "dev": "webpack --config ./build/webpack.common.js",
    "start": "webpack-dev-server --config ./build/webpack.common.js",
    "build": "webpack --env.production --config ./build/webpack.common.js"
  },

2.webpack.common.js文件使用
module.exports = (env) => {
    if (env && env.production) {
        return merge(commonConfig, prodConfig)
    }else {
        return merge(commonConfig, devConfig)
    }
}
```

## 26.Library的打包 ##
1. 打包发布一个库文件基本配置
    ```
    const path = require('path')

    module.exports = {
        mode: 'production',
        entry: './src/index.js',
        externals: ['lodash'], //打包库文件时，不打包lodash，而是业务代码必须加载lodash
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: 'library.js',
            library: 'library', //script标签引入时，注入全局变量library
            libraryTarget: 'umd' //适合通用的模块引入方式
        }
    }
    ```
## 27.PWA的打包配置 ##
1.PWA（Progressive Web Application）是什么？
    - 当服务器挂掉时，使用PWA技术，可以当用户访问网站时，启用本地缓存文件，使用户体验更好。

2. 如何配置？
    1. 使用`workbox-webpack-plugin`插件
    2. webpack配置文件配置
        ```
        new WorkboxPlugin.GenerateSW({
            clientsClaim: true,
            skipWaiting: true
        })
        ```
    3. 写业务代码
        ```
        if ('serviceWorker' in navigator) {
          window.addEventListener('load', () => {
              navigator.serviceWorker.register('/service-worker.js').then(registration => {
            console.log('service-worker registed')
        }).catch(error => {
            console.log('service-worker registed error')
              })
          })
        }
        ```

## 28.TypeScript的打包配置 ##
1. 什么是typescript？
    - typescript是javascript的超级。
    - typescript支持属性和方法类型的校验，提高我们代码的严谨性。

2. webpack打包typescript的简单配置。
    ```
    1. webpack.config.js文件配置
        const path = require('path')

        module.exports = {
            mode: 'production',
            entry: './src/index.tsx',
            module: {
                rules: [{
                    test: /\.tsx?$/,
                    use: 'ts-loader',
                    exclude: /node_modules/
                }]
            },
            output: {
                filename: 'bundle.js',
                path: path.resolve(__dirname, 'dist')
            }
        }
    2. ts-loader需要新加一个tsconfig.js文件配置。
        {
            "compilerOpitons": {
                "outDir": "./dist", //把打包生成的文件放在dist目录下
                "module": "es6", //支持用es Module的模块方法引入
                "target": "es5", //把代码转换为es5语法
                "allowJs": true //支持在ts文件中引入js模块
            }
        }
    ```

3. typescript在引入外部库时，外部库不支持typescript语法检测提示，这是需要安装对应的检测文件。`npm i @types/lodash -D //安装loadsh对应的语法检测文件`

## 29.使用WebpackDevServer实现请求转发 ##
1. proxy属性是在devServer中，所以只在开发环境中有效，生产环境中还是以页面写的请求地址为准。
2. proxy简单的配置。
    ```
    devServer: {
        contentBase: './dist',
        // open: true,
        port: 3000,
        hot: true, //开启HMR功能
        hotOnly: true, //即使HMR功能没有生效，也不让浏览器自动重新刷新,
        proxy: {
            '/react/api': {
                target: 'https://www.dell-lee.com',//请求代理到的网址
                secure: false, //可以对https生效
                pathRewrite: { //对路径的重写
                    'header.json': 'demo.json'
                },
                changeOrigin: true, //改变请求内origin的选项，使我们的代理支持更多的域名下请求的访问（针对一些反爬虫网站的设置）
                headers: { //在请求头内自定义设置，模拟请求的配置
                    host: 'www.dell-lee.com'
                }
            }
        }
    },
    ```

## 30.WebpackDevServer解决单页面应用路由问题 ##
1. historyApiFallback是在devServer中，所以只在开发环境中有效，生产环境中要在Apache等服务器上进行配置。
    `historyApiFallback: true, //任何url路径访问都会访问根路径html文件，从而使路由生效`

## 31.EsLint在webpack中的配置 ##
1. 如何在项目中配置eslint？
    1. `npm install eslint --save-dev`。
    2. 运行`npx eslint --init`按需选择生成.eslintrc.js文件。

2. 如何使用eslint?
    - 运行`npx eslint src`命令，检测src目录下的代码。
    - 编辑器安装ESLint插件，代码提示语法错误。
    - 使用eslint-loader当代码打包时进行语法规范检测。
        ```
        {
            test: /\.js$/,
            exclude: /node_modules/, // 排除node_modules文件中的js文件
            include: path.resolve(__dirname, '../src'),//只对src源代码文件夹中的代码使用loader
            use: ['babel-loader', {
              loader: 'eslint-loader',
              options: {
                fix: true, // 修复打包时浅显的问题
                cache: true, // 将eslint结果缓存到文件中，减少打包时间
              },
            }],
          },
        //可以在devServer中配置overlay页面弹层，显示错误
        devServer: {
            overlay: true,
          },
        ```

    - 由于使用eslint-loader必然会降低打包速度，可以选择使用git 钩子，当提交代码时，进行代码检测，不通过不允许提交。

## 32.webpack性能优化 ##
1. 如何提升webpack打包速度？
    - 跟上技术迭代（Node、Npm、Yarn）。
    - 在尽可能少的模块上应用Loader。
    - Plugin尽可能少和可靠。
    - resolve参数配置合理。
        ```
        resolve: {
            extensions: ['.js', '.jsx'], //先找有没有后缀为.js的文件，没有的话再找后缀为.jsx的文件。
            mainFiles: ['index', 'child'], //先找路径下有没有文件名为index的文件，再找文件名为child的文件。
            alias: {
                child: path.resolve(__dirname, 'src/child/child'), //取别名，页面上可以直接引入import child from child引用
            }
        }
        ```
    
    - 使用DllPlugin提高打包速度(通过把一些库文件存起来，不让它每一次都重新打包)
        ```
        1. 配置webpack.dll.js文件，对第三方模块单独进行打包，生成打包结果。
        const path = require('path')
        const webpack = require('webpack')
        
        module.exports = {
            mode: 'production',
            entry: {  //需要进行打包的第三方模块代码
                vendors: ['lodash'],
                react: ['react-dom', 'react']
            },
            output: {
                filename: '[name].dll.js',
                path: path.resolve(__dirname, './dll'),
                library: '[name]' //通过全局变量暴露出去
            },
            plugins: [
                new webpack.DllPlugin({ //生成映射文件
                    name: '[name]',
                    path: path.resolve(__dirname, './dll/[name].manifest.json')
                })
            ]
        }
        2. 把生成的文件引入到html中，并引入分析映射文件。
        plugins: [
            new HtmlWebpackPlugin({
                // title: 'webpack-study',
                template: './index.html',
            }),
            new CleanWebpackPlugin(),
            new webpack.HotModuleReplacementPlugin(),
            new addAssetHtmlWebpackPlugin({ //把生成的dll文件挂载到html文件中
                filepath: path.resolve(__dirname, './dll/vendors.dll.js')
            }),
            new addAssetHtmlWebpackPlugin({ //把生成的dll文件挂载到html文件中
                filepath: path.resolve(__dirname, './dll/react.dll.js')
            }),
            new webpack.DllReferencePlugin({ //分析引入映射文件
                manifest: path.resolve(__dirname, './dll/vendors.manifest.json')
            }),
            new webpack.DllReferencePlugin({ //分析引入映射文件
                manifest: path.resolve(__dirname, './dll/react.manifest.json')
            })
        ]
        ```
    - 控制包文件大小。
    - thread-loader,paraller-webpack,happypack多进程打包。
    - 合理使用sourceMap。
    - 结合stats分析打包结果。
    - 开发环境内存编译。
    - 开发环境无用插件剔除。

## 33.webpack进行多页面应用打包 ##
```
1. entry入口文件引入。
entry: {
        index: './index.js',
        list: './list.js'
    },
2. 生成多个html文件。
plugins: [
        new HtmlWebpackPlugin({
            // title: 'webpack-study',
            filename: 'index.html',
            template: './index.html',
            chunks: ['react', 'vendors', 'index'],//需要引入的chunks
        }),
        new HtmlWebpackPlugin({
            filename: 'list.html',
            template: './index.html',
            chunks: ['react', 'vendors', 'list'],
        }),
    ]
```

## 34. 如何编写一个Loader ##
```
1. webpack.config.js内容
    const path = require('path')

    module.exports = {
        mode: 'development',
        entry: {
            bundle: './src/index.js'
        },
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: '[name].js'
        },
        resolveLoader: { //先在node_modules中找loader，如果没有在./loaders路径下找loader
            modules: ['node_modules', './loaders']
        },
        module: {
            rules: [
                {
                    test: /\.js$/,
                    use: [
                        {
                            loader: 'replaceLoader',
                        },
                        {
                            loader: 'replaceLoaderAsync',
                            options: {
                                name: 'wu'
                            }
                        }
                    ]
                }
            ]
        }
    }

2. loaders文件代码
    const loaderUtils = require('loader-utils')

    module.exports = function (source) { //不能写成箭头函数，会改变里面this指向
        const options = loaderUtils.getOptions(this); //使用该插件获得this.query中的参数
        const callback = this.async() //异步返回结果
    
        setTimeout(() => {
            const result =  source.replace('Ronaldo', options.name)
            callback(null, result)
        },2000)
    }
```

## 35.如何编写一个Plugin ##
```
class CopyRightWebpackPlugin {
    // constructor(options) {
    //     console.log(options)
    // }
 
    apply(compiler) {
        //同步
        compiler.hooks.compile.tap('CopyRightWebpackPlugin', (compilation) => { //一个新的编译(compilation)创建之后，钩入(hook into) compiler。
            console.log('compiler')
        })
        //异步
        compiler.hooks.emit.tapAsync('CopyRightWebpackPlugin', (compilation, callback) => { //emit钩子表示在要生成打包文件夹时执行
            compilation.assets['copyRight.txt'] = { //创建文件
                source: function() {
                    return 'copyRight by Ronaldo'
                },
                size: function() {
                    return 21
                }
            }
            callback()
        })
    }
}

module.exports = CopyRightWebpackPlugin
```
- 如何使用node调试
    - inspect 开启node调试
    - inspect-brk 代码第一行开启断点
    ```
    {
      "name": "35",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "debug": "node --inspect --inspect-brk node_modules/webpack/bin/webpack.js",
        "build": "webpack"
      },
      "keywords": [],
      "author": "",
      "license": "ISC",
      "devDependencies": {
        "webpack": "^4.33.0",
        "webpack-cli": "^3.3.4"
      }
    }

    ```

## 36.Bundler源码编写 ##
```
const fs = require('fs')
const path = require('path')
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default
const babel = require('@babel/core')

const moduleAnalyser = (filename) => {
    const content = fs.readFileSync(filename, 'utf-8') //读取文件内容
    const ast = parser.parse(content, { //转化为抽象语法树
        sourceType: 'module' //以严格模式解析并允许模块声明
    })
    const dependencies = {}
    traverse(ast, {
        ImportDeclaration({ node }) { //遍历抽象语法树，找到type为ImportDeclaration的节点
            //生成绝对路径
            const dirname = path.dirname(filename)
            const newFile = './' + path.join(dirname, node.source.value)
            dependencies[node.source.value] = newFile
        }
    })
    //把es6语法转化为浏览器可以执行的语法
    const { code } = babel.transformFromAst(ast, null, {
        presets: ["@babel/preset-env"]
    })
    return {
        filename,
        dependencies,
        code
    }
}

const makeDependenciesGraph = (entry) => {
    //生成依赖图谱,代码分析所有模块
    const entryModule = moduleAnalyser(entry)
    const graphArray = [ entryModule ]
    for(let i = 0; i < graphArray.length; i++) {
        const item = graphArray[i]
        const { dependencies } = item
        if(dependencies) {
            for(let j in dependencies) {
                graphArray.push(
                    moduleAnalyser(dependencies[j])
                )
            }
        }
    }
    //数据格式转换为对象
    const graph = {};
    graphArray.forEach(item => {
        graph[item.filename] = {
            dependencies: item.dependencies,
            code: item.code
        }
    });
    return graph;
}

const generateCode = (entry) => {
    const graph = JSON.stringify(makeDependenciesGraph(entry))
    //使用闭包让模块代码不会影响到外部变量
    return `
        (function(graph) {
            function require(module) {
                function localRequire(relativePath) {
                    return require(graph[module].dependencies[relativePath])
                }
                var exports = {};
                (function(require, exports, code) {
                    eval(code)
                })(localRequire, exports, graph[module].code);
                return exports;
            };
            require('${entry}')
        })(${graph});
    `
}

const code = generateCode('./src/index.js')
console.log(code)

```
[源码](https://github.com/seven-WX/webpack-study-code)