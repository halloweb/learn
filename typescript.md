
## 1. 什么是typescript
- TypeScript = Type + Script（标准JS）
- TypeScript is a typed superset of JavaScript that compiles to plain JavaScript（TypeScript是一个编译到纯JS的有类型定义的JS超集）
- 最终被编译成 JavaScript 代码，使浏览器可以理解
## 2. 提高开发效率和项目可维护性
强类型约束和静态检查可以在编译期间发现并纠正错误配合VSCode等IDE支持，方便的查看类型推断和引用关系尤其当引用第三方包的时候
## 3. 基础类型
 #### boolean、number、string、Symbol、Array、Null、Undefined
 #### Enum 类型
   1. 数字枚举
   ```typescript
     enum Direction {
        NORTH,
        SOUTH,
        EAST,
        WEST,
     }
     let dir: Direction = Direction.NORTH;
   ```
   默认情况下，NORTH 的初始值为 0，其余的成员会从 1 开始自动增长。换句话说，Direction.SOUTH 的值为 1，Direction.EAST 的值为 2，Direction.WEST 的值为 3。上面的枚举示例代码经过编译后会生成以下代码：
   ```typescript
      "use strict";
      var Direction;
      (function (Direction) {
        Direction[(Direction["NORTH"] = 0)] = "NORTH";
        Direction[(Direction["SOUTH"] = 1)] = "SOUTH";
        Direction[(Direction["EAST"] = 2)] = "EAST";
        Direction[(Direction["WEST"] = 3)] = "WEST";
      })(Direction || (Direction = {}));
      var dir = Direction.NORTH;
   ```
   2. 字符串枚举
   ```typescript
      enum Direction {
          Up = "UP",
          Down = "DOWN",
          Left = "LEFT",
          Right = "RIGHT",
      }
   ```
   3. 常量枚举
   常量枚举只能使用常量枚举表达式，并且不同于常规的枚举，它们在编译阶段会被删除。 常量枚举成员在使用的地方会被内联进来。 之所以可以这么做是因为，常量枚举不允许包含计算成员
   ```typescript
      const enum Directions {
        Up,
        Down,
        Left,
        Right
      }

      let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
      // 生成后的代码为：

      var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
   ```
 #### Any类型 Unknown 类型
  有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 any类型来标记这些变量
  unknown 类型只能被赋值给 any 类型和 unknown 类型本身
#### Void
  void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void
  ```typescript
    function warnUser(): void {}
  ```
#### Never
  never类型表示的是那些永不存在的值的类型。 例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型
  ```typescript
     // 返回never的函数必须存在无法达到的终点
      function error(message: string): never {
          throw new Error(message);
      }

      // 推断的返回值类型为never
      function fail() {
          return error("Something failed");
      }

      // 返回never的函数必须存在无法达到的终点
      function infiniteLoop(): never {
          while (true) {
          }
      }
  ```
  ```typescript
    // 在 TypeScript 中，可以利用 never 类型的特性来实现全面性检查
     type Foo = string | number;
      function controlFlowAnalysisWithNever(foo: Foo) {
        if (typeof foo === "string") {
          // 这里 foo 被收窄为 string 类型
        } else if (typeof foo === "number") {
          // 这里 foo 被收窄为 number 类型
        } else {
          // foo 在这里是 never
          const check: never = foo;
        }
      }
  ```
  ## 4.断言
  ####  “尖括号” 语法
  ``` typescript
    let someValue: any = "this is a string";
    let strLength: number = (<string>someValue).length;

  ```
  #### as 语法
  ```typescript
     let someValue: any = "this is a string";
     let strLength: number = (someValue as string).length;
  ```
  #### 非空断言
  在上下文中当类型检查器无法断定类型时，一个新的后缀表达式操作符 ! 可以用于断言操作对象是非 null 和非 undefined 类型。具体而言，x! 将从 x 值域中排除 null 和 undefined 
  ```typescript
     function myFunc(maybeString: string | undefined | null) {
      // Type 'string | null | undefined' is not assignable to type 'string'.
      // Type 'undefined' is not assignable to type 'string'. 
      const onlyString: string = maybeString; // Error
      const ignoreUndefinedAndNull: string = maybeString!; // Ok
    }
    // 调用函数时忽略 undefined 类型
    type NumGenerator = () => number;

    function myFunc(numGenerator: NumGenerator | undefined) {
      // Object is possibly 'undefined'.(2532)
      // Cannot invoke an object which is possibly 'undefined'.(2722)
      const num1 = numGenerator(); // Error
      const num2 = numGenerator!(); //OK
    }
  ```
  #### 确定赋值断言
     允许在实例属性和变量声明后面放置一个 ! 号，从而告诉 TypeScript 该属性会被明确地赋值
     
```typescript
  let x: number;
  initialize();
  // Variable 'x' is used before being assigned.(2454)
  console.log(2 * x); 
  // Error
  function initialize() {
    x = 10;
  }
  // 通过 let x!: number 确定赋值断言, TypeScript 编译器就会知道该属性会被明确地赋值
  let x!: number;
``` 
  ## 5.类型守卫
     类型保护是可执行运行时检查的一种表达式，用于确保该类型在一定的范围内。换句话说，类型保护可以保证一个字符串是一个字符串，尽管它的值也可以是一个数值。类型保护与特性检测并不是完全不同，其主要思想是尝试检测属性、方法或原型，以确定如何处理值。目前主要有四种的方式来实现类型保护：
  #### 1. in关键字
  用来遍历枚举类型
  ```typescript
     type Keys = "a" | "b" | "c"

      type Obj =  {
        [p in Keys]: any
      } // -> { a: any, b: any, c: any }

  ```
  #### 2. typeof 关键字

  ypeof 类型保护只支持两种形式：typeof v === "typename" 和 typeof v !== typename，"typename" 必须是 "number"， "string"， "boolean" 或 "symbol"。 但是 TypeScript 并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护
  #### 3. instanceof 关键字
  ```typescript
     let padder: Padder = new SpaceRepeatingPadder(6);

      if (padder instanceof SpaceRepeatingPadder) {
        // padder的类型收窄为 'SpaceRepeatingPadder'
      }
  ```
  ## 6. 联合类型和交叉类型
  ```typescript
     // 联合 |
     const sayHello = (name: string | undefined) => {
        /* ... */
      };
     // 交叉类型 &
     type PartialPointX = { x: number; };
      type Point = PartialPointX & { y: number; };

      let point: Point = {
        x: 1,
        y: 1
      }
  ```
  ## 7. 泛型
  设计泛型的关键目的是在成员之间提供有意义的约束，这些成员可以是：类的实例成员、类的方法、函数参数和函数返回值。
  泛型（Generics）是允许同一个函数接受不同类型参数的一种模板。相比于使用 any 类型，使用泛型来创建可复用的组件要更好，因为泛型会保留参数类型。
  #### 1. 泛型接口
  ```typescript
      interface GenericIdentityFn<T> {
        (arg: T): T;
      }
  ```
  #### 2. 泛型类
  ```typescript
    class GenericNumber<T> {
      zeroValue: T;
      add: (x: T, y: T) => T;
    }

    let myGenericNumber = new GenericNumber<number>();
    myGenericNumber.zeroValue = 0;
    myGenericNumber.add = function (x, y) {
      return x + y;
    };
    
  ```
  #### 3. 泛型工具类型
  1. typeof
    用来获取一个变量声明或对象的类型
    ```typescript
        interface Person {
          name: string;
          age: number;
        }
        const sem: Person = { name: 'semlinker', age: 33 };
        type Sem= typeof sem; // -> Person
    ```
  2. keyof
    该操作符可以用于获取某种类型的所有键，其返回类型是联合类型。
  ```typescript
      interface Person {
      name: string;
      age: number;
    }
    type K1 = keyof Person; // "name" | "age"
    type K2 = keyof Person[]; // "length" | "toString" | "pop" | "push" | "concat" | "join" 
    / * JavaScript 在执行索引操作时，会先把数值索引先转换为字符串索引
    type K3 = keyof { [x: string]: Person };  // string | number

  ```
  3. infer
  ```typescript
     // infer R 就是声明一个变量来承载传入函数签名的返回值类型，简单说就是用它取到函数返回值的类型方便之后使用。
      type ReturnType<T> = T extends (
        ...args: any[]
      ) => infer R ? R : any;

  ```
  4. extends
   // 有时候我们定义的泛型不想过于灵活或者说想继承某些类等，可以通过 extends 关键字添加泛型约束。
   ```typescript
      interface Lengthwise {
        length: number;
      }

      function loggingIdentity<T extends Lengthwise>(arg: T): T {
        console.log(arg.length);
        return arg;
      }
      // T[K]表示对象T的属性K所表示的类型，在上述例子中，T[K][] 表示变量T取属性K的值的数组
      function getValues<T, K extends keyof T>(person: T, keys: K[]):T[K][] {
       return keys.map(key => person[key]);
      }
      interface Person {
          name: string;
          age: number;
      }

      const person: Person = {
          name: 'musion',
          age: 35
      }

      getValues(person, ['name']) // ['musion']
      getValues(person, ['gender']) // 报错：
    // Argument of Type '"gender"[]' is not assignable to parameter of type '("name" | "age")[]'.
    // Type "gender" is not assignable to type "name" | "age".
    // 这里有T和K两种类型，根据类型推断，第一个参数person就是person，类型会被推断为Person。而第二个数组参数的类型推断（K extends keyof T），keyof关键字可以获取T，也就是Person的所有属性名，即['name', 'age']。而extends关键字让泛型K继承了Person的所有属性名，即['name', 'age']。这三个特性组合保证了代码的动态性和准确性，也让代码提示变得更加丰富了

   ```
   5. Partial
   Partial<T> 的作用就是将某个类型里的属性全部变为可选项 ?
   有个局限性，就是只支持处理第一层的属性
   ```typescript
      type Partial<T> = {
        [P in keyof T]?: T[P];
      };
      // DeepPartial
      type DeepPartial<T> = {
           // 如果是 object，则递归类型
          [U in keyof T]?: T[U] extends object
            ? DeepPartial<T[U]>
            : T[U]
      };

      type PartialedWindow = DeepPartial<T>; // 现在T上所有属性都变成了可选啦

   ```
  6. Required将类型的属性变成必选
  7. Readonly
  8. Pick 从某个类型中挑出一些属性出来
  ```typescript
     type Pick<T, K extends keyof T> = {
       [P in K]: T[P];
      };
  ```
  9. ReturnType 用来得到一个函数的返回值类型
  ```typescript
    type Func = (value: number) => string;
    const foo: ReturnType<Func> = "1";
  ```
  10. Exclude
  Exclude<T, U> 的作用是将某个类型中属于另一个的类型移除掉。
  11. Extract
       Extract<T, U> 的作用是从 T 中提取出 U
  ```typescript
     type Extract<T, U> = T extends U ? T : never;
     type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
    type T1 = Extract<string | number | (() => void), Function>; // () =>void
  ```
  12. Omit
   Omit<T, K extends keyof any> 的作用是使用 T 类型中除了 K 类型的所有属性，来构造一个新的类型
  13. NonNullable
   NonNullable<T> 的作用是用来过滤类型中的 null 及 undefined 类型。
  14. Parameters
   Parameters<T> 的作用是用于获得函数的参数类型组成的元组类型。
   ```typescript
      type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any
      ? P : never;

      type A = Parameters<() =>void>; // []
   ```
## 总结
  #### 避免类似接口重复定义
  ```typescript
    interface Person { 
      firstName: string; 
      lastName: string;
    }

    interface PersonWithBirthDate extends Person { 
      birth: Date;
    }
    // 或
    type PersonWithBirthDate = Person & { birth: Date };

    // 
    const INIT_OPTIONS = {
      width: 640,
      height: 480,
      color: "#00FF00",
      label: "VGA",
    };

    interface Options {
      width: number;
      height: number;
      color: string;
      label: string;
    }
    // 对于 Options 接口来说，你也可以使用 typeof 操作符来快速获取配置对象的「形状」
    type Options = typeof INIT_OPTIONS;   
  ```
  #### type和interface
  ```typescript
    interface Animal {

      name: string

    }

   // 可以继续在原有属性基础上，添加新属性：color

    interface Animal {

      color: string

    }

    type Animal = {

      name: string

    }
    // type类型不支持属性扩展
    // Error: Duplicate identifier 'Animal'

    type Animal = {

      color: string

    }
  ```
## 练习题
1. SetOptional
  ```typescript
    type Foo = {
      a: number;
      b?: string;
      c: boolean;
    }

    type Simplify<T> = {
      [P in keyof T]: T[P]
    }

    type SetOptional<T, K extends keyof T> = 
      Simplify<Partial<Pick<T, K>> & Pick<T, Exclude<keyof T, K>>>

    // 测试用例
    type SomeOptional = SetOptional<Foo, 'a' | 'b'>;
    // type SomeOptional = {
    // 	a?: number; // 该属性已变成可选的
    // 	b?: string; // 保持不变
    // 	c: boolean;
    // }
  ```
  2. SetRequired
  ```typescript
    type Foo = {
      a?: number;
      b: string;
      c?: boolean;
    }

    type Simplify<T> = {
      [P in keyof T]: T[P]
    }

    type SetRequired<T, K extends keyof T> = Simplify<Pick<T, Exclude<keyof T, K>> & Required<Pick<T, K>>>

    // 测试用例
    type SomeRequired = SetRequired<Foo, 'b' | 'c'>;
    // type SomeRequired = {
    // 	a?: number;
    // 	b: string; // 保持不变
    // 	c: boolean; // 该属性已变成必填
    // }
 
  ```
  3. ConditionalPick 工具类型，支持根据指定的 Condition 条件来生成新的类型
  ```typescript
     interface Example {
        a: string;
        b: string | number;
        c: () => void;
        d: {};
      }

      type ConditionalPick<T, K> = {
        [P in keyof T as (T[P] extends K ? P : never)]: T[P]
      }

      // 测试用例：
      type StringKeysOnly = ConditionalPick<Example, string>;
      //=> {a: string}

  ```
  ## [typescript练习题](https://github.com/semlinker/awesome-typescript/issues)


  ## react
  #### useState<T>
   ```typescript
      // 大部分情况下，TS 会自动为你推导 state 的类型
      // user会自动推导为类型: {name: string, age: number}
      const [user] = React.useState({ name: 'sj', age: 32 })
      const userInfo: typeof user = {name: 'aaa', age: 12}
      // 但是，一些状态初始值为空时（null），需要显示地声明类型
      type User = {
        name: string
        age: number
      }
     const [user, setUser] = React.useState<User | null>(null)
   ```
   #### 获取未导出的Type
   ```typescript
      

      // 获取参数类型

      import { Button } from 'library' // 但是未导出props type

      type ButtonProps = React.ComponentProps<typeof Button> // 获取props

      type AlertButtonProps = Omit<ButtonProps, 'onClick'> // 去除onClick

      const AlertButton: React.FC<AlertButtonProps> = props => (

        <Button onClick={() => alert('hello')} {...props} />

      )

      // 获取返回值类型

      function foo() {

        return { baz: 1 }

      }


      type FooReturn = ReturnType<typeof foo> // { baz: number }

   ```

