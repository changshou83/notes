## 管理依赖

- `pnpm add <pkg>`，安装包
- `pnpm install`，安装所有依赖项
- `pnpm update`，更新依赖包版本
- `pnpm remove`，移除包
- `pnpm link`，使当前本地包可在系统内访问
- `pnpm unlink`，取消链接
- `pnpm import`，从其他包管理器的lock文件生成`pnpm-lock.yaml`

## 查看依赖

- `pnpm audit`，检查已安装包的已知安全问题
- `pnpm list`，以树形结构输出已安装包的版本及其依赖

## 运行脚本

- `pnpm run`，基本等于`npm run <scripts>`，但是不运行npm hooks
- `pnpm test`，`pnpm run test`
- `pnpm start`，`pnpm run start`
- `pnpm create`，基本等于`npm create`
- `pnpm dlx`，调用远程包
- `pnpm exec`，调用本地项目shell脚本
