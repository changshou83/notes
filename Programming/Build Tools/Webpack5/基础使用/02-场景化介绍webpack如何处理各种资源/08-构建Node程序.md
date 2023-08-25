- 用webpack构建node程序的必要性不大
- 出于学习目的，了解webpack构建node程序的方法和注意事项
	1.  需要 Webpack 的 [`target`](https://webpack.js.org/configuration/target/) 值设置为 `node` ，这能让 Webpack 忽略 `fs/path` 等原生 Node 模块；
	2.  需要使用 [`externals`](https://webpack.js.org/configuration/externals/) 属性过滤 `node_modules` 模块，简单起见，也可以直接使用 `webpack-node-externals` 库；
	3.  需要使用 [`node`](https://webpack.js.org/configuration/node/) 属性，正确处理 `__dirname`、`__filename` 值。
- 有BUG(使用动态Require语句会将目录文件全部打包)，建议尽量不要使用 Webpack 构建 Node 应用