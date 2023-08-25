## React

## Vue

1. 下载Vue3源码，然后执行`pnpm run build --sourcemap`，生成带sourcemap的构建产物
2. 建议把源码和demo项目放到同一个目录，先在下载好的源码中执行npm link链接到全局，之后在demo中执行`npm link vue`把软链指向本地包，然后安装调试
3. 为了让源码是可写的，还需要配置`sourceMapPathOverrides`

vite可能还需要对预加载进行禁用：`optimizeDeps: { exclude: ['vue'] }`
调试之后记得`npm unlink`
