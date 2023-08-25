
| 方式               | tsconfig                                | 安全性 |
| ------------------ | --------------------------------------- | ------ |
| 导入无类型的JS     | {allowJs:true}                         | 较差   |
| 导入并检查JS       | {allowJs:true,checkJs:true}             | 尚可   |
| 导入并检查JSDoc    | {allowJs:true,checkJs:true,strict:true} | 极好   |
| 导入带类型声明的JS | {allowJs:true,strict:true}              | 极好   |
| 导入TS             | {allowJs:true,strict:true}              | 极好   |
