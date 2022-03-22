## 运行
https://www.typescriptlang.org/play


#### 函数类型表达式
`( ParameterList ) => Type`

```
let add: (a: number, b: number) => number;
add = (a, b) => { return a + b }

——————
let add: (a: number, b: number) => number = function(a, b) {return a + b};

-----
let add = (a: number, b:number): number => { return a + b }

-----
function add(a: number, b:number): number {
    return a+b
}
```

#### 调用签名（call-signatures）
函数本质上是一个对象，所以可以拥有属性。而函数类型表达式不支持声明函数。
```
function test(param: number): void { console.log(param) }

test.attr = 4

test(7); //7
console.log(test.attr); //4
```

如果想声明一个带属性的函数，可以在对象类型里写一个调用签名。
`{ ( ParameterList ): Type }`
简单来说：
一个函数类型表达式 == 只有一个调用签名（call-signatures）的对象类型表达式。

```
写成调用签名的话就是
let add: { (a: number, b:number):number }
add = (a, b) => { return a + b }
```

上面带属性的函数就可以写为
```
function test(param: number) { console.log(param) }
test.attr = 4

// 把test赋给instance，因为使用的函数类型声明
let instance: (param: number) => void = test
console.log(instance.attr) //会报错Property 'attr' does not exist on type '(param: number) => void'.


// 把test赋给instance，因为使用调用签名
let instance: {(param: number): void, attr: number }= test
console.log(instance.attr) //4 
```

文档里例子
```
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}

function test(a: number) { return true }
test.description = "test"
let instance: DescribableFunction = test

doSomething(instance) //"test returned true" 
```



### 参考文档
https://www.coolcou.com/typescript/typescript-function-type/typescript-structure-signature.html

### 相关翻译
函数章节的翻译
https://www.typescriptlang.org/docs/handbook/2/functions.html#call-signatures
https://juejin.cn/post/7031097525515845645
