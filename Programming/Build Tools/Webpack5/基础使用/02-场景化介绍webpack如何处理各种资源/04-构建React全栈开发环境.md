- 使用Babel加载JSX文件
	- 步骤
		- 安装依赖：`npm i -D webpack webpack-cli babel-loader @babel/core @babel/preset-react`
		- 配置Webpack：`module: { rules: [{ test: /\.jsx$/, loader: 'babel-loader', options: { presets: ['@babel/preset-react'] } }] }`
- 运行应用：使用`html-webpack-plugin`和`webpack-dev-server`
- 复用其他编译工具
- SSR
	- [示例](https://github.com/Tecvan-fe/webpack-book-samples/tree/main/react-ssr)
	- 成熟方案
		- Nextjs
		- Remix
- CRA
	- React团队推出的针对React的Webpack上层应用
	- 使用`npm run eject`暴露webpack配置已进行定制化，或者使用一些包：`customize-cra`或`react-app-rewired`