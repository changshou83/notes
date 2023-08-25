
## 构建TS应用

- 项目结构：源码放在`src/`，构建产物放在`dist/`
- 构建目标
	- `target`：目标JS版本
	- `module`：模块系统
	- `lib`：目标环境支持的JS特性，具体实现由polyfill提供
- 项目引用
	- 解决代码量过大时，编译时间过长的问题
	- 将一个TS项目分成几个小项目，每个项目一个tsconfig
		- 子项目：composite:true表示这是一个子项目，references声明依赖模块
		- 根项目：使用references声明子项目依赖
	- 构建：`tsc --build`
- 三斜线指令-`types`指令：只导入模块的类型

### 构建产物

| 文件类型 | 扩展名    | tsconfig                    | 默认生成? |
| -------- | --------- | --------------------------- | --------- |
| JS       | .js       | {emitDeclarationOnly:false} | 是        |
| 源码映射 | .js.map   | {sourceMap:true}            | 否        |
| 类型声明 | .d.ts     | {declaration:true}          | 否        |
| 声明映射 | .d.ts.map | {declarationMap:true}       | 否        |

## 发布到NPM

- 生成源码映射
- 编译为ES5
- 模块格式最好为umd，专注浏览器的UI库可以是esm
- 生成类型声明，让TS用户知晓你的代码中的类型
