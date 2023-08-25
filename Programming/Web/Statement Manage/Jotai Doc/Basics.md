# Basics

## Comparison

Jotai 的诞生是为了解决 React 中额外的重新渲染问题。

原生解决方案(useContext+useState)的缺点：
- Provider Hell
- Dynamic Addition/Deletion

需要 `useContextSelector+Memorization` 去解决

### 与其他比较

- Jotai与useContext：Jotai是useContext的直接替代品
- Jotai与Zustand
	- Jotai自下而上(由原子状态组成)，Zustand自上而下(由中心存储仓库组成)
	- 将Jotai类比成useContext+useState，Zustand类比成Redux
