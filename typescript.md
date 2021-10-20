## 1. 什么是typescript
- TypeScript = Type + Script（标准JS）
- TypeScript is a typed superset of JavaScript that compiles to plain JavaScript（TypeScript是一个编译到纯JS的有类型定义的JS超集）
- 最终被编译成 JavaScript 代码，使浏览器可以理解
## 2. 提高开发效率和项目可维护性
强类型约束和静态检查可以在编译期间发现并纠正错误配合VSCode等IDE支持，方便的查看类型推断和引用关系尤其当引用第三方包的时候
## 3. 基础类型
 #### boolean、number、string、Symbol、Array
 #### Enum 类型
   1. 数字枚举
   ```typescript
     enum Direction {
        Up = 1,
        Down,
        Left,
        Right
     }
   ```
