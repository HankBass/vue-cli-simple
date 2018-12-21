### vue-cli搭建的vue开发环境

  vue-cli是vue提供构建单页应用的脚手架。它能够帮助开发者快速的创建vue项目。

  > vue-cli 2.x ,vue-cli 3.x的部分内容可能有所更改。

  #### 安装vue-cli

  ```
    npm install vue-cli -g
  ```
  #### 创建项目

  通过`vue-cli`创建一个`vue`项目

  ```
    vue init webpack <your project>
  ```
  创建过程基本上是下一步下一步下一步，详看图例：

  ![image](https://raw.githubusercontent.com/HankBass/vue-cli-simple/master/src/assets/images/init.png)

  命令解释：

  * Project name :项目名称 ，如果不需要更改直接回车就可以了。注意：这里不能使用大写。
  * Project description:项目描述，默认为A Vue.js project,直接回车，不用编写。
  * Author：作者，如果你有配置git的作者，他会读取。
  * Install vue-router? 是否安装vue的路由插件，Y代表安装，N无需安装，下面的命令也是一样的。
  * Use ESLint to lint your code? 是否用ESLint来限制你的代码错误和风格。
  * setup unit tests with Karma + Mocha? 是否需要安装单元测试工具Karma+Mocha。
  * Setup e2e tests with Nightwatch?是否安装e2e来进行用户行为模拟测试。


  创建完成之后，生成的项目文件

  ![image](https://raw.githubusercontent.com/HankBass/vue-cli-simple/master/src/assets/images/folder.png)


  进入项目
  
  ```
    cd <your project>
  ```

  下载相关依赖

  `npm install`

  启动项目

  `npm run dev`

  之后我们就能看到项目启动了

  ![image](https://raw.githubusercontent.com/HankBass/vue-cli-simple/master/src/assets/images/welcome.png)

  打包项目

  `npm run build`

  #### 项目结构


  ```
    |-- build                            // 项目构建(webpack)相关代码
    |   |-- build.js                     // 生产环境构建代码
    |   |-- check-version.js             // 检查node、npm等版本
    |   |-- utils.js                     // 构建工具相关
    |   |-- vue-loader.conf.js           // webpack loader配置
    |   |-- webpack.base.conf.js         // webpack基础配置
    |   |-- webpack.dev.conf.js          // webpack开发环境配置,构建开发本地服务器
    |   |-- webpack.prod.conf.js         // webpack生产环境配置
    |-- config                           // 项目开发环境配置
    |   |-- dev.env.js                   // 开发环境变量
    |   |-- index.js                     // 项目一些配置变量
    |   |-- prod.env.js                  // 生产环境变量
    |-- src                              // 源码目录
    |   |-- components                   // vue公共组件
    |   |-- router                       // vue的路由管理
    |   |-- App.vue                      // 页面入口文件
    |   |-- main.js                      // 程序入口文件，加载各种公共组件
    |-- static                           // 静态文件，比如一些图片，json数据等
    |-- .babelrc                         // ES6语法编译配置
    |-- .editorconfig                    // 定义代码格式
    |-- .gitignore                       // git上传需要忽略的文件格式
    |-- .postcsssrc                       // postcss配置文件
    |-- README.md                        // 项目说明
    |-- index.html                       // 入口页面
    |-- package.json                     // 项目基本信息,包依赖信息等
  ```

  #### 主要的配置文件

  ```
    "name": "test",
    "version": "1.0.0",
    "description": "",
    "scripts": {
        "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
        "start": "npm run dev",
        "unit": "jest --config test/unit/jest.conf.js --coverage",
        "e2e": "node test/e2e/runner.js",
        "test": "npm run unit && npm run e2e",
        "build": "node build/build.js"
    }

  ```
  当我们执行`npm run dev`时，首选执行的是`webpack.dev.conf.js`
  当我们执行`npm run build`时，首选执行的是`build.js`

  ##### webpack.dev.conf.js

  * 引入配置文件
  * 引入相关插件
  * 启动服务器，并判断是否自动打开默认浏览器
  * 监听端口

```
  'use strict'
  const utils = require('./utils')
  const webpack = require('webpack')
  const config = require('../config')//基本配置的参数
  const merge = require('webpack-merge')// 用于合并webpack的配置文件
  const baseWebpackConfig = require('./webpack.base.conf')//webpack基本配置文件（开发时和运行时公用）
  /**
  * [HtmlWebpackPlugin description]
  * @type {[type]}
  * 这个插件的作用是依据一个简单的模板，帮你生成最终的Html5文件，
  * 这个文件中自动引用了你打包后的JS文件。每次编译都在文件名中插入一个不同的哈希值
  */
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  /*能够更好在终端看到webapck运行的警告和错误*/
  const FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin')
  const portfinder = require('portfinder') // 自动检索下一个可用端口
  const HOST = process.env.HOST//读取系统环境变量的host
  const PORT = process.env.PORT && Number(process.env.PORT)//读取系统环境变量的port
  // 合并baseWebpackConfig配置
  const devWebpackConfig = merge(baseWebpackConfig, {
  module: {
      // 对一些独立的css文件以及它的预处理文件做一个编译
      rules: utils.styleLoaders({ sourceMap: config.dev.cssSourceMap, usePostCSS: true })
  },
    // cheap-module-eval-source-map is faster for development
    devtool: config.dev.devtool,// 添加元信息(meta info)增强调试
    // these devServer options should be customized in /config/index.js
    devServer: {//webpack-dev-server服务器配置
      clientLogLevel: 'warning', // console 控制台显示的消息，可能的值有 none, error, warning 或者 info
      historyApiFallback: true,
      hot: true,//开启热加载
      compress: true,//开启压缩
      host: HOST || config.dev.host,// process.env 优先
      port: PORT || config.dev.port,// process.env 优先
      open: config.dev.autoOpenBrowser,//自动打开浏览器
      overlay: config.dev.errorOverlay// warning 和 error 都要显示
        ? { warnings: false, errors: true }
        : false,
      publicPath: config.dev.assetsPublicPath,
      proxy: config.dev.proxyTable,//代理设置，用于前后端分离
      quiet: true, // necessary for FriendlyErrorsPlugin
      watchOptions: {//启用 Watch 模式。这意味着在初始构建之后，webpack 将继续监听任何已解析文件的更改
        poll: config.dev.poll,//通过传递 true 开启 polling，或者指定毫秒为单位进行轮询。默认为false
      }
    },
    plugins: [//webpack一些构建用到的插件
      new webpack.DefinePlugin({
        'process.env': require('../config/dev.env')
      }),

      /*模块热替换它允许在运行时更新各种模块，而无需进行完全刷新*/
      new webpack.HotModuleReplacementPlugin(),

      new webpack.NamedModulesPlugin(), // 热加载时直接返回更新的文件名，而不是id
      new webpack.NoEmitOnErrorsPlugin(),// 跳过编译时出错的代码并记录下来，主要作用是使编译后运行时的包不出错
      // https://github.com/ampedandwired/html-webpack-plugin
      new HtmlWebpackPlugin({
        // 指定编译后生成的html文件名
        filename: 'index.html',
        // 需要处理的模板
        template: 'index.html',
        // 打包过程中输出的js、css的路径添加到html文件中
        // css文件插入到head中
        // js文件插入到body中，可能的选项有 true, 'head', 'body', false
          inject: true
      }),
    ]
  })

  module.exports = new Promise((resolve, reject) => {
    portfinder.basePort = process.env.PORT || config.dev.port// 获取当前设定的端口
    portfinder.getPort((err, port) => {
      if (err) {
        reject(err)
      } else {
        // process 发布端口
        process.env.PORT = port 
        // 设置 devServer 端口
        devWebpackConfig.devServer.port = port

        // Add FriendlyErrorsPlugin
        devWebpackConfig.plugins.push(new FriendlyErrorsPlugin({// 错误提示插件
          compilationSuccessInfo: {
            messages: [`Your application is running here: http://${devWebpackConfig.devServer.host}:${port}`],
          },
          onErrors: config.dev.notifyOnErrors
          ? utils.createNotifierCallback()
          : undefined
        }))

        resolve(devWebpackConfig)
      }
    })
  })
  ```

  ##### webpack.base.conf.js

  * 配置编译入口和输出路径
  * 模块resolve的规则
  * 配置不同类型模块的处理规则

  ```
    'use strict'
    const path = require('path');
    const utils = require('./utils');
    const config = require('../config');
    const vueLoaderConfig = require('./vue-loader.conf')
    function resolve(dir) {//处理路径
      return path.join(__dirname, '..', dir);
    }

    module.exports = {
      context: path.resolve(__dirname, '../'), // 基础目录
      entry: {
        app: './src/main.js'//入口文件
      },
      output: {
        path: config.build.assetsRoot, // 输出文件，默认'../dist'
        filename: '[name].js',//输出文件名称，
        publicPath: process.env.NODE_ENV === 'production'
        ? config.build.assetsPublicPath // 生产模式publicpath
        : config.dev.assetsPublicPath // 开发模式publicpath
      },
      resolve: { // 解析确定的拓展名，方便模块导入
        extensions: ['.js', '.vue', '.json'], 
        alias: {   // 创建别名
          'vue$': 'vue/dist/vue.esm.js', 
          '@': resolve('src') // 如 '@/components/HelloWorld'
        }
      },
      module: {//模块相关配置，包括loader，plugin等
        rules: [{
            test: /\.vue$/, // vue 要在babel之前
            loader: 'vue-loader',//vue转普通的html
            options: vueLoaderConfig //可选项： vue-loader 选项配置
          },{
            test: /\.js$/, // babel
            loader: 'babel-loader',//es6转es5loader
            include: [resolve('src')]
          },{ // url-loader 文件大小低于指定的限制时，可返回 DataURL，即base64
            test: /\.(png|jpe?g|gif|svg)(\?.*)?$/, // url-loader 图片
            loader: 'url-loader',
            options: { // 兼容性问题需要将query换成options
              limit: 10000, // 默认无限制
              name: utils.assetsPath('img/[name].[hash:7].[ext]') // hash:7 代表 7 位数的 hash
            }
          },{
            test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/, // url-loader 音视频
            loader: 'url-loader',
            options: {
              limit: 10000,
              name: utils.assetsPath('media/[name].[hash:7].[ext]')
            }
          },{
            test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/, // url-loader 字体
            loader: 'url-loader',
            options: {
              limit: 10000,
              name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
            }
          }
        ]
      },
      node: { // 是否 polyfill 或 mock
        setImmediate: false,
        dgram: 'empty',
        fs: 'empty',
        net: 'empty',
        tls: 'empty',
        child_process: 'empty'
      }
    }
  ```

  ##### build.js

  * webpack编译
  * 输出信息

  ```
    'use strict'
    process.env.NODE_ENV = 'production'; // 设置当前环境为生产环境
    const ora = require('ora'); //loading...进度条
    const rm = require('rimraf'); //删除文件 'rm -rf'
    const chalk = require('chalk'); //stdout颜色设置
    const webpack = require('webpack');
    const path = require('path');
    const config = require('../config');
    const webpackConfig = require('./webpack.prod.conf');

    const spinner = ora('正在编译...');
    spinner.start();

    // 清空文件夹
    rm(path.join(config.build.assetsRoot, config.build.assetsSubDirectory), err => {
        if (err) throw err;
        // 删除完成回调函数内执行编译
        webpack(webpackConfig, function (err, stats) {
            spinner.stop();
            if (err) throw err;

        // 编译完成，输出编译文件
            process.stdout.write(stats.toString({
                colors: true,
                modules: false,
                children: false,
                chunks: false,
                chunkModules: false
            }) + '\n\n');

        //error
        if (stats.hasErrors()) {
            console.log(chalk.red('  编译失败出现错误.\n'));
            process.exit(1);
        }

        //完成
        console.log(chalk.cyan('  编译成功.\n'))
        console.log(chalk.yellow(
          '  file:// 无用，需http(s)://.\n'
        ))
      })

    })
  ```

  ##### webpack.prod.conf.js

  * 合并基础的webpack配置
  * 配置webpack的输出
  * 配置webpack插件
  * 配置gzip模式
  * 配置webpack-bundle-analyzer，分析打包后生成的文件结构

  ```
    'use strict'
    const path = require('path');
    const utils = require('./utils');
    const webpack = require('webpack');
    const config = require('../config');
    const merge = require('webpack-merge');
    const baseWebpackConfig = require('./webpack.base.conf');
    const CopyWebpackPlugin = require('copy-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const ExtractTextPlugin = require('extract-text-webpack-plugin');
    const OptimizeCSSPlugin = require('optimize-css-assets-webpack-plugin');

    const env = process.env.NODE_ENV === 'production'
      ? require('../config/prod.env')
      : require('../config/dev.env')

    const webpackConfig = merge(baseWebpackConfig, {
      module: {
        rules: utils.styleLoaders({
          sourceMap: config.build.productionSourceMap, // production 下生成 sourceMap
          extract: true, // util 中 styleLoaders 方法内的 generateLoaders 函数
          usePostCSS: true
        })
      },
      devtool: config.build.productionSourceMap ? config.build.devtool : false,
      output: {
        path: config.build.assetsRoot,
        filename: utils.assetsPath('js/[name].[chunkhash].js'),
        chunkFilename: utils.assetsPath('js/[id].[chunkhash].js')
      },
      plugins: [
        new webpack.DefinePlugin({ 'process.env': env }),
        new webpack.optimize.UglifyJsPlugin({ // js 代码压缩还可配置 include, cache 等，也可用 babel-minify
          compress: { warnings: false },
          sourceMap: config.build.productionSourceMap,
          parallel: true // 充分利用多核cpu
        }),
        // 提取 js 文件中的 css
        new ExtractTextPlugin({
          filename: utils.assetsPath('css/[name].[contenthash].css'),
          allChunks: false,
        }),
        // 压缩提取出的css
        new OptimizeCSSPlugin({
          cssProcessorOptions: config.build.productionSourceMap
          ? { safe: true, map: { inline: false } }
          : { safe: true }
        }),
        // 生成 html
        new HtmlWebpackPlugin({
          filename: process.env.NODE_ENV === 'production'
            ? config.build.index
            : 'index.html',
          template: 'index.html',
          inject: true,
          minify: {
            removeComments: true,
            collapseWhitespace: true,
            removeAttributeQuotes: true
          },
          chunksSortMode: 'dependency' // 按 dependency 的顺序引入
        }),
        new webpack.HashedModuleIdsPlugin(), // 根据模块的相对路径生成一个四位数的 hash 作为模块 id
        new webpack.optimize.ModuleConcatenationPlugin(), // 预编译所有模块到一个闭包中
        // 拆分公共模块
        new webpack.optimize.CommonsChunkPlugin({
          name: 'vendor',
          minChunks: function (module) {
            return (
              module.resource &&
              /\.js$/.test(module.resource) &&
              module.resource.indexOf(
                path.join(__dirname, '../node_modules')
              ) === 0
            )
          }
        }),
        new webpack.optimize.CommonsChunkPlugin({
          name: 'manifest',
          minChunks: Infinity
        }),
        new webpack.optimize.CommonsChunkPlugin({
          name: 'app',
          async: 'vendor-async',
          children: true,
          minChunks: 3
        }),

        // 拷贝静态文档
        new CopyWebpackPlugin([{
            from: path.resolve(__dirname, '../static'),
            to: config.build.assetsSubDirectory,
            ignore: ['.*']
        }])]
    })

    if (config.build.productionGzip) { // gzip 压缩
      const CompressionWebpackPlugin = require('compression-webpack-plugin');

      webpackConfig.plugins.push(
        new CompressionWebpackPlugin({
          asset: '[path].gz[query]',
          algorithm: 'gzip',
          test: new RegExp('\\.(' + config.build.productionGzipExtensions.join('|') + ')$'),
          threshold: 10240, // 10kb 以上大小的文件才压缩
          minRatio: 0.8 // 最小比例达到 .8 时才压缩
        })
      )
    }

    if (config.build.bundleAnalyzerReport) { // 可视化分析包的尺寸
      const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
      webpackConfig.plugins.push(new BundleAnalyzerPlugin());
    }

    module.exports = webpackConfig;
  ```

  ##### config/index.js

  ```
    var path = require('path')
    module.exports = {
        //打包时使用的配置
      build: {
        //webpack的环境
        env: require('./prod.env'),
        //输入的index.html的路径
        index: path.resolve(__dirname, '../dist/index.html'),
        //输出的目标文件夹路径
        assetsRoot: path.resolve(__dirname, '../dist'),
        //输出的子文件夹路径
        assetsSubDirectory: 'static',
        //发布路径
        assetsPublicPath: '/',
        //是否使用SourceMap
        productionSourceMap: true,
        // 是否开启Gzip
        productionGzip: false,
        //Gzip的压缩文件类型
        productionGzipExtensions: ['js', 'css'],
        bundleAnalyzerReport: process.env.npm_config_report
      },
      //开发时使用的配置
      dev: {
        //webpack环境
        env: require('./dev.env'),
        host: 'localhost', // can be overwritten by process.env.HOST
        //端口
        port: 8080,
        //是否自动打开浏览器
        autoOpenBrowser: true,
        //输出的子文件夹路径
        assetsSubDirectory: 'static',
        //发布路径
        assetsPublicPath: '/',
        //配置代理表
        proxyTable: {},
        // CSS Sourcemaps off by default because relative paths are "buggy"
        cssSourceMap: false
      }
    }

  ```

  > 总结：vue-cli给创建vue项目提供了很大的便利。但是同时大量的第三方库的使用，会让打包后的js变的很大，所以还是要熟悉配置,熟悉第三方插件的使用，才可以开发更高效的开发web应用。