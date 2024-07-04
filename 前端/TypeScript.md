## 6.1 介绍
#### **6.1.1什么是TypeScript？**
Typescript是一种由微软开发的自由和开源的编程语言，他是Javascript的超集，可以编译纯Javascript。它可以在任何浏览器、任何计算机和操作系统上面运行。Typescript是为大型应用的开发而设计。
Typescript包括流行的javascript库，并支持javascript代码中调用TypeScript。它可以编译出纯洁、简洁的javascript代码，并且可以在任何浏览器上、Node.js环境中和任何支持EcmaScript的javascript引擎中运行。
Typescript是JavaScript的超集，. js文件可以直接重命名为.ts

#### **6.1.2 TypeScript优势**
**1、支持ES6**
TS是一门支持ES6规范的语言，这套规范发布于2015年，它指出未来一段时间客户端脚本语言的发展方向，也是TS脚本语言的语法成为未来一段时间客户端语言的主流语法。
**2、强大的IDE支持**
**类型检测**：在Typescript里面是运行为变量指定类型的，比如当你为这个变量指定数字类型的值的时候，IDE会做出类型检查，然后告诉你这里可能会有错误，这个特性会减少你在开发阶段犯错误的几率。
**语法提示**：在IDE里面去编写TypeScript的代码时，IDE会根据你当前的上下文，把你能用的类、变量、方法和关键字都给你提示出来，你只要直接去选就可以了，这个特性会大大提升你的开发效率。
**重构**：可以方便的修改变量、方法、类等信息，当发生修改时候，IDE会帮助我们自动引用这个变量、方法、类调用的地方代码，从而提高开发效率。TypeScript 增加了代码的可读性和可维护性，主要体现在：代码补全、接口提示、跳转到定义、重构
**3、强大的框架扩展性**
TypeScript支持各种框架，如：React、Angular、Express、Babel、Vue.js、Asp.Net Core、WeChat、Dojo等框架。

#### **6.1.3 安装**
TypeScript安装有两种方式来获取：npm(Node.js包管理器)、安装Visual Studio的TypeScript插件。
下面针对npm方式进行安装，首先检查是否安装Node.js：

| # 检查node.js 版本，查看是否安装
node -v
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657015494541-a7394a2e-b17c-4cdb-a1d4-2ab072ad13f4.png#id=RqBT1&originHeight=150&originWidth=457&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

# 全局安装 typescript
npm install --g  typescript
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657015494791-cc18a316-8de7-44c7-8e37-70b98d36a4b0.png#id=V9FTW&originHeight=171&originWidth=485&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


# 检查typescript版本
tsc -v |
| --- |


当我们按照上述步骤安装成功后，下面我们新建一个test.ts文件，代码如下：

| var message:string = "Hello World" 
console.log(message) |
| --- |

注：通常我们使用 .ts 作为 TypeScript 代码文件的扩展名。
找到test.ts文件夹目录，然后运行命令行：tsc test.ts，将typescript转换为javascript代码：

| ![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657015495273-c7fbcdaa-f440-4524-ae04-fd0754cd61dc.png#id=SwQnI&originHeight=232&originWidth=403&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) |
| --- |

从上面的代码，我们可以想象到TypeScrpt转换为Javascript过程如下图：
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657015495831-d80ec14c-5331-4275-9eec-2cd42c54dbe6.png#id=Cewuh&originHeight=86&originWidth=387&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
#### **6.1.4 Hello World**
第一个TypeScript程序：

| var message:string = "Hello World" 
console.log(message) |
| --- |

以上代码首先通过 tsc 命令编译：

| tsc Test.ts |
| --- |

得到如下 js 代码：

| var hello = "Hello World!";
console.log(hello); |
| --- |

最后我们使用 node 命令来执行该 js 代码。

| node Test.js
打印：Hello World |
| --- |

我们可以同时编译多个 ts 文件：

| tsc file1.ts, file2.ts, file3.ts |
| --- |



## 6.2 基础语法
TypeScript程序由以下几个部分组成：函数、模块、变量、语句和表达式、注释。
#### 6.2.1 保留关键字
| reak | as | catch | switch |
| --- | --- | --- | --- |
| case | if | throw | else |
| var | number | string | get |
| module | type | instanceof | typeof |
| public | private | enum | export |
| finally | for | while | void |
| null | super | this | new |
| in | return | true | false |
| any | extends | static | let |
| package | implements | interface | function |
| new | try | yield | const |
| continue | do |   |   |


#### 6.2.2 注释
| 单行注释 ( // ) − 在 // 后面的文字都是注释内容。
//let name:string="张三";

多行注释 (/* */) − 这种注释可以跨越多行
/*
let name:string="张三";
let age:number=15;
*/ |
| --- |


#### 6.2.3 基础类型
| 名 称 | 关键字 | 说    明 |
| --- | --- | --- |
| 任意类型 | Any | 声明为 any 的变量可以赋予任意类型的值。 |
| 数字类型 | number | 双精度 64 位浮点值。它可以用来表示整数和分数。
let binaryLiteral: number = 0b1010; // 二进制
let octalLiteral: number = 0o744;    // 八进制
let decLiteral: number = 6;    // 十进制
let hexLiteral: number = 0xf00d;    // 十六进制 |
| 字符串类型 | string | 一个字符系列，使用单引号（'）或双引号（"）来表示字符串类型。反引号（`）来定义多行文本和内嵌表达式。
let name: string = "Runoob";
let years: number = 5;
let words: string = `您好，今年是 ${ name } `; |
| 布尔类型 | boolean | 表示逻辑值：true 和 false。
let flag: boolean = true; |
| 数组类型 | 无 | 声明变量为数组。
// 在元素类型后面加上[]
let arr: number[] = [1, 2];
// 或者使用数组泛型
let arr: Array<number> = [1, 2]; |
| 元组 | 无 | 元组类型用来表示已知元素数量和类型的数组，各元素的类型不必相同，对应位置的类型需要相同。
let x: [string, number];
x = ['Runoob', 1];    // 运行正常
x = [1, 'Runoob'];    // 报错
console.log(x[0]);    // 输出 Runoob |
| 枚举 | enum | 枚举类型用于定义数值集合。
enum Color {Red, Green, Blue};
let c: Color = Color.Blue;
console.log(c);    // 输出 2 |
| void | void | 用于标识方法返回值的类型，表示该方法没有返回值。
function hello(): void {
    alert("Hello Runoob");
} |
| null | null | 表示对象值缺失 |
| undefined | undefined | 用于初始化变量为一个未定义的值 |
| never | never | never 是其它类型（包括 null 和 undefined）的子类型，代表从不会出现的值。 |


##### **6.2.3.1布尔**
最基本的数据类型就是简单的true/false值，在JavaScript和TypeScript里叫做boolean（其它语言中也一样）；

| //** 布尔
let isDone: boolean = false; |
| --- |


##### **6.2.3.2数字**
和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是 number。 除了支持十进制和十六进制字面量，Typescript还支持ECMAScript 2015中引入的二进制和八进制字面量。

| //** 数字
let decListeral: number = 6;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
let hexLiteral: number = 0xf00d; |
| --- |

##### **6.2.3.3字符串**
JavaScript程序的另一项基本操作是处理网页或服务器端的文本数据。 像其它语言里一样，我们使用 string表示文本数据类型。 和JavaScript一样，可以使用双引号（ "）或单引号（'）表示字符串。

| //** 字符串 依然都比的单双引号不区分
let names: string = 'qqaaqq'; |
| --- |

你还可以使用模版字符串，它可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围（ `），并且以${ expr }这种形式嵌入表达式.

| //** 模版字符串
let age: number = 38;
let result = `Hello world，this is ${age}`;
console.error(result); |
| --- |



##### **6.2.3.4数组**
TypeScript像JavaScript一样可以操作数组元素。 有两种方式可以定义数组。 第一种，可以在元素类型后面接上[]，表示由此类型元素组成的一个数组：

| let list: number[] = [1, 2, 3];
console.log(list);
let itmes: Array<Object> = [123,321,'张三',{'name':'zhangsan'}];
console.error(itmes); |
| --- |

##### **6.2.3.5元组 Tuple**
元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为string和number类型的元组。

| let x: [string, number, object];
x = ['zhangsan',123,{'name':'zhangsan'}];
console.log(x[0].substr(1)); |
| --- |


##### **6.2.3.6枚举**
默认情况下，从0开始为元素编号。 你也可以手动的指定成员的数值。 例如，我们将上面的例子改成从 3开始编号, 或者，全部都采用手动赋值：

| enum Color{Red = 3,Green,Bule};
let number : Color = Color.Red; //得到索引
let colorName : string = Color[4]; //得到相应值
console.log('number:', number,'name:',colorName); |
| --- |

##### **6.2.3.7任意值**
有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 any类型来标记这些变量：

| //** 任意值
let noType :any = 4;
noType = 'maybe a string instance';
noType = false;
console.log('noType:',noType); |
| --- |

在对现有代码进行改写的时候，any类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。 你可能认为 Object有相似的作用，就像它在其它语言中那样。 但是 Object类型的变量只是允许你给它赋任意值 - 但是却不能够在它上面调用任意的方法，即便它真的有这些方法：

| /** compare any or object
noType = 4;
console.error('noType function:'+noType.toFixed());
console.error('noType function:'+noType.ifItExists());

let prettySure:Object = 4;
// Error: Property 'toFixed' doesn't exist on type 'Object'.
console.error('prettySure function:'+prettySure. toFixed()); |
| --- |


##### **6.2.3.8 空值**
某种程度上来说，void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void：

| // ** 空值 void 在函数中,代表不返回任何类型
function fn() : void{
   console.error('test function return');
}
fn(); |
| --- |

声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null：

| // ** 声明变量后,只能为它赋值undefined和null
let unusable : void = undefined; |
| --- |


##### **6.2.3.9 Null 和 Undefined**
TypeScript里，undefined和null两者各自有自己的类型分别叫做undefined和null。 和 void相似，它们的本身的类型用处不是很大：

| // Not much else we can assign to these variables! 
let u: undefined = undefined; 
let n: null = null; |
| --- |

然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。 这能避免 很多常见的问题。 也许在某处你想传入一个 string或null或undefined，你可以使用联合类型string | null | undefined。 再次说明，稍后我们会介绍联合类型。
注意：我们鼓励尽可能地使用--strictNullChecks，但在本手册里我们假设这个标记是关闭的。

##### **6.2.3.10 Never**
never类型表示的是那些永不存在的值的类型。 例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never类型，当它们被永不为真的类型保护所约束时。
never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。
下面是一些返回never类型的函数：

| // 返回never的函数必须存在无法达到的终点 
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
} |
| --- |


##### **6.2.3.11 类型断言**
有时候你会遇到这样的情况，你会比TypeScript更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。
通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

| //** 类型断言 两种形式<>泛型 和 as
//  两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；
//  然而，当你在TypeScript里使用JSX时，只有 as语法断言是被允许的
let someValue: any = "this is a string";

let strLength :number = (<string>someValue).length;
console.error('strlength:' + strLength);

let strLength_2: number = (someValue as string).length;
console.error('strLength_2:' + strLength_2); |
| --- |


#### 6.2.4 变量声明
##### **6.2.4.1变量声明**
变量是一种使用方便的占位符，用于引用计算机内存地址，可以把变量看做存储数据的容器。
TypeScript变量的命名规则：

1. 变量名称可以包含数字和字母。
2. 除了下划线_和美元符号$符号外，不能包含其他特殊字符，包括空格。
3. 变量不能以数字开头。

变量使用前必须先声明，我们可以使用var来声明变量。
**语法格式：**

| Var [变量名]:[类型]=值; |
| --- |

**案例如下：**

| Var username:string=”zhangsan”; |
| --- |


声明变量的类型及没有初始值，变量默认值为undefined。
**语法格式：**

| Var [变量名]:[类型]; |
| --- |

**案例如下：**

| Var username:string; |
| --- |


声明变量并初始值，但不设置类型，该变量可以是任意类型。
**语法格式：**

| Var [变量名]=值; |
| --- |

**案例如下：**

| Var username=”zhangsan”; |
| --- |


声明变量没有设置类型和初始值，类型可以是任意类型，默认初始值为 undefined：
**语法格式：**

| Var [变量名]; |
| --- |

**案例如下：**

| Var username; |
| --- |

下面用一个案例来说明变量声明：

| var uname:string = "Runoob"; 
var score1:number = 50;
var score2:number = 42.50
var sum = score1 + score2 
console.log("名字: "+uname) 
console.log("第一个科目成绩: "+score1) 
console.log("第二个科目成绩: "+score2) 
console.log("总成绩: "+sum) |
| --- |

注意：变量不要使用 name 否则会与 DOM 中的全局 window 对象下的 name 属性出现了重名。
使用 tsc 命令编译以上代码，得到如下 JavaScript 代码：

| var uname = "Runoob";
var score1 = 50;
var score2 = 42.50;
var sum = score1 + score2;
console.log("名字: " + uname);
console.log("第一个科目成绩: " + score1);
console.log("第二个科目成绩: " + score2);
console.log("总成绩: " + sum); |
| --- |

执行该 JavaScript 代码输出结果为：

| 名字: Runoob
第一个科目成绩: 50
第二个科目成绩: 42.5
总成绩: 92.5 |
| --- |


##### **6.2.4.2 var作用域**
var声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问（我们后面会详细介绍），包含它的代码块对此没有什么影响。 有些人称此为* var作用域或函数作用域*。 函数参数也使用函数作用域。
这些作用域规则可能会引发一些错误。 其中之一就是，多次声明同一个变量并不会报错：

| if(true){
   var a = 5;
}
while (!b) {
   b = !b;
}
var b;
console.log(a,b);//5 true

//** var函数作用域中,声明被提前,赋值留在原地
function f() {
   var a = 1;
   var a = 2; //多次定义未被覆盖
   var b = g();
   a = 3;
   
   return b;
   function g() {//这样定义函数会被提到当前作用域顶部
      return a;
   }
}
console.error(f()); // 2 |
| --- |


##### **6.2.4.3 let块作用域**
当用let声明一个变量，它使用的是词法作用域或块作用域。 不同于使用 var声明的变量那样可以在包含它们的函数外访问，块作用域变量在包含它们的块或for循环之外是不能访问的。

| function fn(input: boolean) {
   let a = 100;
   
   if (input) {
      // Still okay to reference 'a'
      let b = a + 1; //不会声明提前
      return b;
   }
   // Error: 'b' doesn't exist here
   return b;
}
console.error(fn(false)); // undefined |
| --- |


这里我们定义了2个变量a和b。 a的作用域是f函数体内，而b的作用域是if语句块里。
在catch语句里声明的变量也具有同样的作用域规则。
拥有块级作用域的变量的另一个特点是，它们不能在被声明之前读或写。 虽然这些变量始终“存在”于它们的作用域里，但在直到声明它的代码之前的区域都属于 时间死区。 它只是用来说明我们不能在 let语句之前访问它们，幸运的是TypeScript可以告诉我们这些信息。

##### **6.2.4.4重定义及屏蔽**
我们提过使用var声明时，它不在乎你声明多少次；你只会得到1个。

| function f(x) {
    var x;
    var x;
    if (true) {
        var x;
    }
} |
| --- |

在上面的例子里，所有x的声明实际上都引用一个相同的x，并且这是完全有效的代码。 这经常会成为bug的来源。 好的是， let声明就不会这么宽松了。

| let x = 10;
let x = 20; // 错误，不能在1个作用域里多次声明`x` |
| --- |

并不是要求两个均是块级作用域的声明TypeScript才会给出一个错误的警告。

| function f(x) {
    let x = 100; // error: interferes with parameter declaration
}

function g() {
    let x = 100;
    var x = 100; // error: can't have both declarations of 'x'
} |
| --- |

并不是说块级作用域变量不能在函数作用域内声明。 而是块级作用域变量需要在不用的块里声明。

| function f(condition, x) {
    if (condition) {
        let x = 100;
        return x;
    }
    return x;
}

f(false, 0); // returns 0
f(true, 0);  // returns 100 |
| --- |

在一个嵌套作用域里引入一个新名字的行为称做屏蔽。 它是一把双刃剑，它可能会不小心地引入新问题，同时也可能会解决一些错误。 例如，假设我们现在用 let重写之前的sumMatrix函数。

| function sumMatrix(matrix: number[][]) {
    let sum = 0;
    for (let i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (let i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }
    return sum;
} |
| --- |

这个版本的循环能得到正确的结果，因为内层循环的i可以屏蔽掉外层循环的i。
通常来讲应该避免使用屏蔽，因为我们需要写出清晰的代码。 同时也有些场景适合利用它，你需要好好打算一下。

##### **6.2.4.5块级作用域变量的获取**
在我们最初谈及获取用var声明的变量时，我们简略地探究了一下在获取到了变量之后它的行为是怎样的。 直观地讲，每次进入一个作用域时，它创建了一个变量的 环境。 就算作用域内代码已经执行完毕，这个环境与其捕获的变量依然存在。

| function theCityThatAlwaysSleeps() {
    let getCity;
    if (true) {
        let city = "Seattle";
        getCity = function() {
            return city;
        }
    }
    return getCity();
} |
| --- |

因为我们已经在city的环境里获取到了city，所以就算if语句执行结束后我们仍然可以访问它。

| for (let i = 0; i < 10 ; i++) {
    setTimeout(function() {console.log(i); }, 100 * i);
} |
| --- |

当let声明出现在循环体里时拥有完全不同的行为。 不仅是在循环里引入了一个新的变量环境，而是针对 每次迭代都会创建这样一个新作用域。 这就是我们在使用立即执行的函数表达式时做的事，所以在 setTimeout例子里我们仅使用let声明就可以了。

##### **6.2.4.6 const 声明**
const 声明是声明变量的另一种方式。
它们与let声明相似，但是就像它的名字所表达的，它们被赋值后不能再改变。 换句话说，它们拥有与 let相同的作用域规则，但是不能对它们重新赋值。
这很好理解，它们引用的值是不可变的。

| const numLivesForCat = 9;
const kitty = {
    name: "Aurora",
    numLives: numLivesForCat,
}
// Error
kitty = {
    name: "Danielle",
    numLives: numLivesForCat
};
// all "okay"
kitty.name = "Rory";
kitty.name = "Kitty";
kitty.name = "Cat";
kitty.numLives--; |
| --- |

除非你使用特殊的方法去避免, 实际上const变量的内部状态是可修改的。


##### **6.2.4.7 let vs. const**
现在我们有两种作用域相似的声明方式，我们自然会问到底应该使用哪个。 与大多数泛泛的问题一样，答案是：依情况而定。
使用[最小特权原则](https://en.wikipedia.org/wiki/Principle_of_least_privilege)，所有变量除了你计划去修改的都应该使用const。 基本原则就是如果一个变量不需要对它写入，那么其它使用这些代码的人也不能够写入它们，并且要思考为什么会需要对这些变量重新赋值。 使用 const也可以让我们更容易的推测数据的流动。
另一方面，用户很喜欢let的简洁性。 这个手册大部分地方都使用了 let。
跟据你的自己判断，如果合适的话，与团队成员商议一下。

#### 6.2.5 运算符
运算符用于执行程序代码运算，会针对一个以上的操作数项目来进行运算。

| 7+5=12 |
| --- |

以上实例中7、5、12是操作数。运算符+用于加值，运算符=用于赋值。
Typescript主要包含以下几种运算符：算术运算符、逻辑运算符、关系运算符、按位运算符、赋值运算符、三元/条件运算符、字符串运算符、类型运算符。

##### **6.2.5.1算术运算符**
| 运算符 | 描述 | 列子 | x运算结果 | y运算结果 |
| --- | --- | --- | --- | --- |
| + | 加法 | x=y+2 | 7 | 5 |
| - | 减法 | x=y-2 | 3 | 5 |
| * | 乘法 | x=y*2 | 10 | 5 |
| / | 除法 | x=y/2 | 2.5 | 5 |
| % | 取模（余数） | x=y%2 | 1 | 5 |
| ++ | 自增 | x=++y | 6 | 6 |
|  |  | x=y++ | 5 | 6 |
| -- | 自减 | x=--y | 4 | 4 |
|  |  | x=y-- | 5 | 4 |

下面用一个列子来说明：

| **TypeScript代码：**
var num1:number=12;
var num2:number=15;
var result=num1+num2;
sonsole.log(result);

**Tsc编译成JavaScript代码**：
var num1=12;
var num2=15;
var result=num1+num2;
sonsole.log(result); |
| --- |


##### **6.2.5.2关系运算符**
| 运算符 | 描述 | 列子 | x运算结果 | y运算结果 |
| --- | --- | --- | --- | --- |
| == | 等于 | x==8 | false | == |
|  |  | x==5 | true |  |
| != |  不等于 | x!=8 | true | != |
| > |  大于 | x>8 | false | > |
| < |  小于 | x<8 | true | < |
| >= |  大于或等于 | x>=8 | false | >= |
| <= |  小于或等于 | x<=8 | true | <= |
| == | 等于 | x==8 | false | == |
|  |  | x==5 | true |  |

下面用一个列子来说明：

| **执行代码：**
var num1:number = 5;
var num2:number = 9;
console.log("num1 的值为: "+num1); 
console.log("num2 的值为:"+num2);
var res = num1>num2 
console.log("num1 大于n num2: "+res)

**运行结果：**
num1 的值为: 5
num2 的值为:9
num1 大于n num2: false |
| --- |


##### **6.2.5.3逻辑运算符**
| 运算符 | 描述 | 列子 |
| --- | --- | --- |
| && | and 且 | (x<10&#124;&#124;y>1) 为true |
| &#124;&#124; | Or 或 | (x<5&#124;&#124;y==5)为true |
| ! | not 不等于 | !(x==y)为true |

下面用一个列子来说明：

| **执行代码：**
var avg:number = 20; 
var percentage:number = 90; 
var res:boolean = ((avg>50)&&(percentage>80)); 
console.log("(avg>50)&&(percentage>80): ",res);
var a = 10 
var result2 = ( a<10 && a>5)
console.log("( a<10 && a>5) : ",res);

**运行结果：**
(avg>50)&&(percentage>80):  false
( a<10 && a>5):  false |
| --- |


##### **6.2.5.4三元运算符(?)**
三元运算有3个操作数，并且需要判断布尔表达式的值。该运算符的主要是决定哪个值应该赋值给变量。

| 条件语句是否(true or false) ? 条件满足结果1 : 条件满足结果2 |
| --- |

下面看一个案例：

| **执行代码：**
var num1:number=15;
var num2:number=16;
var result=num1>num2?’num1大于num2’ : ‘num1小于等于num2’;
Console.log(‘num1>num2?’+result);

**运行结果：**
num1>num2?num1小于等于num2 |
| --- |


##### **6.2.5.5类型运算符**
**Typeof运算符**
Typeof是一元运算符，返回操作数的数据类型。

| **执行代码：**
var num1=15;
Console.log(typeof num1);  //输出结果：number

var num2=’zhangsan’
Console.log(typeof num2);  //输出结果：string |
| --- |


**instanceof运算符**
instanceof 运算符用于判断对象是否为指定的类型。

| **执行代码：**
var num1=15;
var num2=’zhangsan’
Console.log(num1 instanceof num2 );  //输出结果：false |
| --- |


#### 6.2.6 条件&循环
##### **6.2.6.1条件语句**
条件语句用于基于不同的条件来执行不同的动作。TypeScript 条件语句是通过一条或多条语句的执行结果（True 或 False）来决定执行的代码块。
可以通过下图来简单了解**if**条件语句的执行过程:
![](https://cdn.nlark.com/yuque/0/2022/jpeg/2811620/1657015496312-c7cf10a5-51f3-42e0-b223-fdf556591461.jpeg#id=kccr1&originHeight=321&originWidth=251&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
If条件语句格式：

| if 语句{ 语句块  }
if 语句{ 语句块  } else{ 语句块  }
if 语句{ 语句块  } else if语句{ 语句块  }  else{ 语句块  } |
| --- |

下面用一个列子来说明：

| var num1:number=10;
var num2:number=15;

if(num1<num2)
{
   console.log("num1 小于 num2")
}
else if(num1<num2)
{
   console.log("num1 等于 num2")
}
else
{
   console.log("num1 大于 num2")
} |
| --- |


可以通过下图来简单了解**switch case**条件语句的执行过程:
![](https://cdn.nlark.com/yuque/0/2022/jpeg/2811620/1657015497001-b297712e-dd77-440c-8f9f-e57c586ff91e.jpeg#id=SykQC&originHeight=405&originWidth=302&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
switch case条件语句格式：

| Switch(值){ 
   case 值1:
      break；
   case 值2:
      break；
default 
      break；

} |
| --- |

下面用一个列子来说明：

| var num1:number=10;
var num2:number=15;
var result=num1+num2;

switch(result)
{
   case 10:
     console.log("10");
     break;
   case 15:
     console.log("15");
     break;
   case 25:
     console.log("25");
     break;
   default:
     console.log("未找到");
     break;  
} |
| --- |

注：break是终止当前正在执行switch，不在执行switch内部语句。

##### **6.2.6.2循环语句**
有时候我们可能需要多次执行同一代码块，那么久衍生出一个集合逐个去执行这一代码块。循环语句运行我们多次执行一个语句或者语句组。
**for循环**
TypeScript for 循环用于多次执行一个语句序列，简化管理循环变量的代码。

| for(语句1;语句2;语句3)
{
  执行序列
} |
| --- |

下面我们用一个流程图来描述一下：
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657015497397-fde3a2e8-1acc-4622-b5f1-28a96bb0af89.png#id=zJoon&originHeight=504&originWidth=359&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

| **执行代码：**
for(var i=0;i<=2;i++)
{
   Console.log('i='+i);
}

**执行结果：**
i=0
i=1
i=2 |
| --- |


**for...in循环**
for...in 语句用于一组值的集合或列表进行迭代输出。

| for (var val in list) { 
    //语句 
} |
| --- |

案例：

| **执行代码：**
var j:any; 
var n:any = "a b c" 
 
for(j in n) {
    console.log(n[j])  
}
**执行结果：**
a
b
c |
| --- |


**for…of 、forEach、every 和 some 循环**
此外，TypeScript 还支持 for…of 、forEach、every 和 some 循环。
for...of 语句创建一个循环来迭代可迭代的对象。在 ES6 中引入的 for...of 循环，以替代 for...in 和 forEach() ，并支持新的迭代协议。for...of 允许你遍历 Arrays（数组）, Strings（字符串）, Maps（映射）, Sets（集合）等可迭代的数据结构等。

| **TypeScript for...of 循环**
let someArray = [1, "string", false];
for (let entry of someArray) {
    console.log(entry); // 1, "string", false
}

**TypeScript forEach 循环**
let list = [4, 5, 6];
list.forEach((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
});


**TypeScript every 循环**
let list = [4, 5, 6];
list.every((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
    return true; // Continues
}); |
| --- |


**while 循环**
while 语句在给定条件为 true 时，重复执行语句或语句组。循环主体执行之前会先测试条件。
语句格式：

| while(语句块true false)
{
   语句顺序
} |
| --- |

condition 可以是任意的表达式，当条件为 true 时执行循环。 当条件为 false 时，程序流将退出循环。
![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1657015497902-c6561a04-080f-421f-93f8-c66ac39fa69b.png#id=nkOUG&originHeight=404&originWidth=263&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

| var num:number = 5; 
var factorial:number = 1; 
 
while(num >=1) { 
    factorial = factorial * num; 
    num--; 
} 
console.log("5 的阶乘为："+factorial); |
| --- |

**do ... while 循环**
do...while 循环是在循环的尾部检查它的条件。
语句格式：

| do
{
   语句序列;
}while(语句块 true  flase); |
| --- |

![](https://cdn.nlark.com/yuque/0/2022/jpeg/2811620/1657015498424-9211b3a4-7dce-414d-ba9e-75ac9d5580ed.jpeg#id=yE10C&originHeight=331&originWidth=277&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

| var n:number = 10;
do { 
    console.log(n); 
    n--; 
} 
while(n>=0); |
| --- |


#### 6.2.7 接口
##### **6.2.7.1接口初堪**
TypeScript的核心原则之一是对值所具有的shape进行类型检查。 它有时被称做“鸭式辨型法”或“结构性子类型化”。 在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。
使用接口来描述：必须包含一个label属性且类型为string：

| interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj); |
| --- |

LabelledValue接口就好比一个名字，用来描述上面例子里的要求。 它代表了有一个 label属性且类型为string的对象。 需要注意的是，我们在这里并不能像在其它语言里一样，说传给 printLabel的对象实现了这个接口。我们只会去关注值的外形。 只要传入的对象满足上面提到的必要条件，那么它就是被允许的。
还有一点值得提的是，类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以。
##### **6.2.7.2可选属性**
接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。 可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了。

| //** 通过?来定义可选属性
interface SquareConfig {
   color ?: string;
   width ?: number;
   [propName: string]: any; //还会带有任意数量的其它属性
}

function createSquare(config: SquareConfig): { color: string; area: number; } {
   let newSquare = {color: '#e4393c', area: 100};
   config.color && (newSquare.color = config.color);
   config.width && (newSquare.area = config.width * config.width);
   return newSquare;
}
let mySquare = createSquare({color: "black", height: 900});
console.error(mySquare); |
| --- |

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。 比如，我们故意将 createSquare里的color属性名拼错，就会得到一个错误提示：
// Error: Property 'clor' does not exist on type 'SquareConfig'

##### **6.2.7.3只读属性**
| //** 只读属性
//一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用 readonly来指定只读属性

interface Point {
   readonly x: number;
   readonly y: number;
}

//可以通过赋值一个对象字面量来构造一个Point。 赋值后， x和y再也不能被改变了。
let p1: Point = {x: 10, y: 30};
// p1.x = 300; //error |
| --- |

TypeScript具有ReadonlyArray<T>类型，它与Array<T>相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改：

| let arr: Array<number> = [1, 2, 3, 4];
let only: ReadonlyArray<number> = [...arr];
arr.push(6);
// only.push(7); //Error
// only.length= 10; //Error
// let a: Array<number> = only //Error |
| --- |

上面代码的最后一行，可以看到就算把整个ReadonlyArray赋值到一个普通数组也是不可以的。 但是你可以用类型断言重写：

| let newArr: number[] = only as number[]; |
| --- |


##### **6.2.7.4 readonly vs const**
最简单判断该用readonly还是const的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用const，若做为属性则使用readonly。

##### **6.2.7.5 额外的属性检查**
我们在第一个例子里使用了接口，TypeScript让我们传入{ size: number; label: string; }到仅期望得到{ label: string; }的函数里。 我们已经学过了可选属性，并且知道他们在“option bags”模式里很有用。
然而，天真地将这两者结合的话就会像在JavaScript里那样搬起石头砸自己的脚。 比如，拿 createSquare例子来说：

| interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}
let mySquare = createSquare({ colour: "red", width: 100 }); |
| --- |

注意传入createSquare的参数拼写为*colour*而不是color。 在JavaScript里，这会默默地失败。
你可能会争辩这个程序已经正确地类型化了，因为width属性是兼容的，不存在color属性，而且额外的colour属性是无意义的。
然而，TypeScript会认为这段代码可能存在bug。 对象字面量会被特殊对待而且会经过 额外属性检查，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误。

| // error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 }); |
| --- |

绕开这些检查非常简单。 最简便的方法是使用类型断言：

| let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig); |
| --- |

然而，最佳的方式是能够添加一个字符串索引签名，前提是你能够确定这个对象可能具有某些做为特殊用途使用的额外属性。 如果 SquareConfig带有上面定义的类型的color和width属性，并且还会带有任意数量的其它属性，那么我们可以这样定义它：

| interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
} |
| --- |

我们稍后会讲到索引签名，但在这我们要表示的是SquareConfig可以有任意数量的属性，并且只要它们不是color和width，那么就无所谓它们的类型是什么。
还有最后一种跳过这些检查的方式，这可能会让你感到惊讶，它就是将这个对象赋值给一个另一个变量： 因为squareOptions不会经过额外属性检查，所以编译器不会报错。

| let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions); |
| --- |

要留意，在像上面一样的简单代码里，你可能不应该去绕开这些检查。 对于包含方法和内部状态的复杂对象字面量来讲，你可能需要使用这些技巧，但是大部额外属性检查错误是真正的bug。 就是说你遇到了额外类型检查出的错误，比如“option bags”，你应该去审查一下你的类型声明。 在这里，如果支持传入 color或colour属性到createSquare，你应该修改SquareConfig定义来体现出这一点。

##### **6.2.7.6函数类型**
接口能够描述JavaScript中对象拥有的各种各样的外形。 除了描述带有属性的普通对象外，接口也可以描述函数类型。
为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。
对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。 

| interface SearchFunc {
   (source: string, subString: string): boolean;
}

let newFn: SearchFunc = function (a: string, nb: string) {
   let res = false;
   a && (res = true);
   res = nb.length > 0 ? false : true;
   return res;
}
console.log(newFn('zhangsan', 'qwe')); |
| --- |

数的参数会逐个进行检查，要求对应位置上的参数类型是兼容的。 如果你不想指定类型，Typescript的类型系统会推断出参数类型，因为函数直接赋值给了 SearchFunc类型变量。 函数的返回值类型是通过其返回值推断出来的（此

例是 false和true）。 如果让这个函数返回数字或字符串，类型检查器会警告我们函数的返回值类型与SearchFunc接口中的定义不匹配。

| let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    if (result == -1) {
        return false;
    }
    else {
        return true;
    }
} |
| --- |



##### **6.2.7.7 可索引的类型**
与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如a[10]或ageMap["daniel"]。 可索引类型具有一个 索引签名，它描述了对象索引的类型，还有相应的索引返回值类型。 让我们看一个例子：

| interface StringArray {
   [index: number]: string;
}
let myArray: StringArray = ['Bob', 'Fred'];
let myStr: string = myArray[1]; |
| --- |

上面例子里，我们定义了StringArray接口，它具有索引签名。 这个索引签名表示了当用 number去索引StringArray时会得到string类型的返回值。
共有支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的**子类型**。 这是因为当使用 number来索引时，JavaScript会将它转换成string然后再去索引对象。 也就是说用 100（一个number）去索引等同于使用"100"（一个string）去索引，因此两者需要保持一致。

| class Person {
   name: string;
}
class Students extends Person {
   gender: string;
}
interface NoOkay {
   // [x: number] : Person; //:Numeric index type 'Person' is not assignable to string index type 'Students'                                                                                                                                                                                                                s;
[x: number]: Students; //或者一样
    [x: string]: Students;

}

interface NumberDictionary {
   [index: string]: number;
   length: number;
   // name : string;//错误，`name`的类型不是索引类型的子类型
} |
| --- |

字符串索引签名能够很好的描述dictionary模式，并且它们也会确保所有属性与其返回值类型相匹配。 因为字符串索引声明了 obj.property和obj["property"]两种形式都可以。 下面的例子里， name的类型与字符串索引类型不匹配，所以类型检查器给出一个错误提示：

| interface NumberDictionary {
  [index: string]: number;
  length: number;    // 可以，length是number类型
  name: string       // 错误，`name`的类型不是索引类型的子类型
} |
| --- |

最后，你可以将索引签名设置为只读，这样就防止了给索引赋值：

| interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error! |
| --- |

你不能设置myArray[2]，因为索引签名是只读的。
##### **6.2.7.8类类型**
###### **6.2.7.8.1**实现接口
与C#或Java里接口的基本作用一样，TypeScript也能够用它来明确的强制一个类去符合某种契约。
你也可以在接口中描述一个方法，在类里实现它，如同下面的setNumber方法一样：

| interface ClockInterface {
   currentTime: Date;
}
interface FuncInterface {
   setNumber(n: number);
}
class Orders implements ClockInterface, FuncInterface {
   constructor(h: number, n: string) {
   }
   
   setNumber(n: number) {
      throw new Error('Method not implemented.');
   }
   
   currentTime: Date;
}
new Orders(250, 'zhangsan'); |
| --- |


接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员。

###### **6.2.7.8.2扩展接口（和java一样,接口可以多继承）**
和类一样，接口也可以相互扩展。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。

| interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10; |
| --- |

一个接口可以继承多个接口，创建出多个接口的合成接口。

| interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0; |
| --- |


###### **6.2.7.8.3混合类型**
先前我们提过，接口能够描述JavaScript里丰富的类型。 因为JavaScript其动态灵活的特点，有时你会希望一个对象可以同时具有上面提到的多种类型。

| interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0; |
| --- |


###### **6.2.7.8.4类静态部分与实例部分的区别**

当你操作类和接口的时候，你要知道类是具有两个类型的：静态部分的类型和实例的类型。 你会注意到，当你用构造器签名去定义一个接口并试图定义一个类去实现这个接口时会得到一个错误：

| interface Counter {
   (start: number): string;
   interval: number;
   reset(): void;
}

function getCounter(): Counter {
   let counter = <Counter> function (start: number) {
   };
   counter.interval = 123;
   counter.reset = function () {
   };
   return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0; |
| --- |


| interface ClockConstructor {
    new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
    currentTime: Date;
    constructor(h: number, m: number) { }
} |
| --- |


这里因为当一个类实现了一个接口时，只对其实例部分进行类型检查。 constructor存在于类的静态部分，所以不在检查的范围内。
因此，我们应该直接操作类的静态部分。 看下面的例子，我们定义了两个接口， ClockConstructor为构造函数所用和ClockInterface为实例方法所用。 为了方便我们定义一个构造函数 createClock，它用传入的类型创建实例。

| interface ClockConstructor {
   new (hour: number, minute: number): ClockInterfaces;
}
interface ClockInterfaces {
   tick();
}
function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterfaces {
   return new ctor(hour, minute);
}
class DigitalClock implements ClockInterfaces {
   constructor(h: number, m: number) {
   }
   
   tick() {
      console.log("beep beep");
   }
}
class AnalogClock implements ClockInterfaces {
   constructor(h: number, m: number) {
   }
   
   tick() {
      console.log("tick tock");
   }
}
let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);

digital.tick(); |
| --- |

因为createClock的第一个参数是ClockConstructor类型，在createClock(AnalogClock, 7, 32)里，会检查AnalogClock是否符合构造函数签名。

###### **6.2.7.8.5接口继承类**
当接口继承了一个类类型时，它会继承类的成员但不包括其实现。 就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样。 接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）。
当你有一个庞大的继承结构时这很有用，但要指出的是你的代码只在子类拥有特定属性时起作用。 这个子类除了继承至基类外与基类没有任何关系。 例：

| class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control {
    select() { }
}

class TextBox extends Control {
    select() { }
}

class Image {
    select() { }
}

class Location {
    select() { }
} |
| --- |

在上面的例子里，SelectableControl包含了Control的所有成员，包括私有成员state。 因为 state是私有成员，所以只能够是Control的子类们才能实现SelectableControl接口。 因为只有 Control的子类才能够拥有一个声明于Control的私有成员state，这对私有成员的兼容性是必需的。
在Control类内部，是允许通过SelectableControl的实例来访问私有成员state的。 实际上，SelectableControl就像Control一样，并拥有一个select方法。 Button和TextBox类是SelectableControl的子类（因为它们都继承自Control并有select方法），但Image和Location类并不是这样的。




#### 6.2.8 类
传统的JavaScript程序使用函数和基于原型的继承来创建可重用的组件，但对于熟悉使用面向对象方式的程序员来讲就有些棘手，因为他们用的是基于类的继承并且对象是由类构建出来的。 从ECMAScript 2015，也就是ECMAScript 6开始，JavaScript程序员将能够使用基于类的面向对象的方式。 使用TypeScript，我们允许开发者现在就使用这些特性，并且编译后的JavaScript可以在所有主流浏览器和平台上运行，而不需要等到下个JavaScript版本。
##### **实例化类**
class Greeter{
   private greeting : string;//区分js中不用定义成员变量,
   constructor(message :string){
      this.greeting = message;
   }
   greet(){
      return 'Hello,'+this.greeting;
   }
}
let **_greeters _**= new Greeter('world');**_console_**.error(**_greeters_**);
我们声明一个 Greeter类。这个类有3个成员：一个叫做greeting的属性，一个构造函数和一个greet方法。
你会注意到，我们在引用任何一个类成员的时候都用了this(这是区分js和java,java中本类成员变量this可以省略)。 它表示我们访问的是类的成员。
最后一行，我们使用new构造了Greeter类的一个实例。 它会调用之前定义的构造函数，创建一个 Greeter类型的新对象，并执行构造函数初始化它。
##### **继承**
在TypeScript里，我们可以使用常用的面向对象模式。 当然，基于类的程序设计中最基本的模式是允许使用继承来扩展现有的类。

/** 继承
 * super的使用和this的使用几乎一致,
 * this代表的是本来对象的引用.
 * super代表的是父类对象的引用.
* */class Animal {
   name : string;
   constructor(theName :string){
      this.name = theName;
   }
   move(distanceInMeters :number=0){
      **_console_**.log(`${this.name} moved ${distanceInMeters}m.`);
   }
}
class  Snake extends Animal{
   constructor(name : string){
      super(name);
   }
   move(distanceInMeters = 5){
      super.move(distanceInMeters);
   }
}class Horse extends Animal{
   constructor(name : string){
      super(name);
   }
   move(distanceInMeters = 37){
      **_console_**.error('不想要你的方法', `${this.name} run ${distanceInMeters}米`);
   }
}let **_snake _**= new Snake('蛇');**_snake_**.move(7);
let **_horse_**: Animal = new Horse('马');**_horse_**.move(35);
这个例子展示了TypeScript中继承的一些特征，它们与其它语言类似。 我们使用 extends关键字来创建子类。你可以看到Horse和Snake类是基类Animal的子类，并且可以访问其属性和方法。
包含构造函数的派生类必须调用super()，它会执行基类的构造方法。
这个例子演示了如何在子类里可以重写父类的方法。 Snake类和Horse类都创建了move方法，它们重写了从Animal继承来的move方法，使得move方法根据不同的类而具有不同的功能。 注意，即使 tom被声明为Animal(就是多态嘛,废话那么多)类型，但因为它的值是Horse，tom.move(34)会调用Horse里的重写方法：
##### **公共，私有与受保护的修饰符**
默认为public
在上面的例子里，我们可以自由的访问程序里定义的成员。 如果你对其它语言中的类比较了解，就会注意到我们在之前的代码里并没有使用 public来做修饰；例如，C#要求必须明确地使用public指定成员是可见的。 在TypeScript里，成员都默认为 public。在java中不写默认实private和public之间的一种类型
你也可以明确的将一个成员标记成public。 我们可以用下面的方式来重写上面的 Animal类：
class Animal {
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
###### **理解private**
当成员被标记成private时，它就不能在声明它的类的外部访问。比如：
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // Error: 'name' is private;
TypeScript使用的是结构性类型系统。 当我们比较两种不同的类型时，并不在乎它们从何处而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。
然而，当我们比较带有private或protected成员的类型的时候，情况就不同了。 如果其中一个类型里包含一个private成员，那么只有当另外一个类型中也存在这样一个private成员， 并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。 对于 protected成员也使用这个规则。
下面来看一个例子，更好地说明了这一点：
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // Error: Animal and Employee are not compatible
这个例子中有Animal和Rhino两个类，Rhino是Animal类的子类。 还有一个 Employee类，其类型看上去与Animal是相同的。 我们创建了几个这些类的实例，并相互赋值来看看会发生什么。 因为 Animal和Rhino共享了来自Animal里的私有成员定义private name: string，因此它们是兼容的。 然而 Employee却不是这样。当把Employee赋值给Animal的时候，得到一个错误，说它们的类型不兼容。 尽管 Employee里也有一个私有成员name，但它明显不是Animal里面定义的那个。
###### **理解protected**
protected修饰符与private修饰符的行为很相似，但有一点不同，protected成员在派生类中仍然可以访问。例如：
class Person {
    protected name: string;
    constructor(name: string) { this.name = name; }
}

class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name)
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name); // error
注意，我们不能在Person类外使用name，但是我们仍然可以通过Employee类的实例方法访问，因为Employee是由Person派生而来的。
构造函数也可以被标记成protected。 这意味着这个类不能在包含它的类外被实例化，但是能被继承。比如，
class Person {
    protected name: string;
    protected constructor(theName: string) { this.name = theName; }
}

// Employee can extend Person
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John"); // Error: The 'Person' constructor is protected
###### **readonly修饰符**
你可以使用readonly关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // error! name is readonly.
###### **参数属性**
在上面的例子中，我们不得不定义一个受保护的成员name和一个构造函数参数theName在Person类里，并且立刻给name和theName赋值。 这种情况经常会遇到。 参数属性可以方便地让我们在一个地方定义并初始化一个成员。 下面的例子是对之前 Animal类的修改版，使用了参数属性：
class Animal {
    constructor(private name: string) { }
    move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
注意看我们是如何舍弃了theName，仅在构造函数里使用private name: string参数来创建和初始化name成员。 我们把声明和赋值合并至一处。
参数属性通过给构造函数参数添加一个访问限定符来声明。 使用 private限定一个参数属性会声明并初始化一个私有成员；对于public和protected来说也是一样。
##### **存取器**
TypeScript支持通过getters/setters来截取对对象成员的访问。 它能帮助你有效的控制对对象成员的访问。
下面来看如何把一个简单的类改写成使用get和set。 首先，我们从一个没有使用存取器的例子开始。
class Employee {
    fullName: string;
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    console.log(employee.fullName);
}
我们可以随意的设置fullName，这是非常方便的，但是这也可能会带来麻烦。
下面这个版本里，我们先检查用户密码是否正确，然后再允许其修改员工信息。 我们把对 fullName的直接访问改成了可以检查密码的set方法。 我们也加了一个 get方法，让上面的例子仍然可以工作。
let passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
我们可以修改一下密码，来验证一下存取器是否是工作的。当密码不对时，会提示我们没有权限去修改员工。
对于存取器有下面几点需要注意的：
首先，存取器要求你将编译器设置为输出ECMAScript 5或更高。 不支持降级到ECMAScript 3。 其次，只带有 get不带有set的存取器自动被推断为readonly。 这在从代码生成 .d.ts文件时是有帮助的，因为利用这个属性的用户会看到不允许够改变它的值。
##### **静态属性**
到目前为止，我们只讨论了类的实例成员，那些仅当类被实例化的时候才会被初始化的属性。 我们也可以创建类的静态成员，这些属性存在于类本身上面而不是类的实例上。 在这个例子里，我们使用 static定义origin，因为它是所有网格都会用到的属性。 每个实例想要访问这个属性的时候，都要在 origin前面加上类名。 如同在实例属性上使用 this.前缀来访问属性一样，这里我们使用Grid.来访问静态属性。
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
##### **抽象类**
抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法。
abstract class Animal {
    abstract makeSound(): void;
    move(): void {
        console.log('roaming the earch...');
    }
}
抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含 abstract关键字并且可以包含访问修饰符。
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log('Department name: ' + this.name);
    }

    abstract printMeeting(): void; // 必须在派生类中实现
}

class AccountingDepartment extends Department {

    constructor() {
        super('Accounting and Auditing'); // constructors in derived classes must call super()
    }

    printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }

    generateReports(): void {
        console.log('Generating accounting reports...');
    }
}

let department: Department; // ok to create a reference to an abstract type
department = new Department(); // error: cannot create an instance of an abstract class
department = new AccountingDepartment(); // ok to create and assign a non-abstract subclass
department.printName();
department.printMeeting();
department.generateReports(); // error: method doesn't exist on declared abstract type
##### **高级技巧**
###### **构造函数**
当你在TypeScript里声明了一个类的时候，实际上同时声明了很多东西。 首先就是类的 实例的类型。
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter: Greeter;
greeter = new Greeter("world");
console.log(greeter.greet());
这里，我们写了let greeter: Greeter，意思是Greeter类的实例的类型是Greeter。 这对于用过其它面向对象语言的程序员来讲已经是老习惯了。
我们也创建了一个叫做构造函数的值。 这个函数会在我们使用 new创建类实例的时候被调用。 下面我们来看看，上面的代码被编译成JavaScript后是什么样子的：
let Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();

let greeter;
greeter = new Greeter("world");
console.log(greeter.greet());
上面的代码里，let Greeter将被赋值为构造函数。 当我们调用 new并执行了这个函数后，便会得到一个类的实例。 这个构造函数也包含了类的所有静态属性。 换个角度说，我们可以认为类具有 实例部分与静态部分这两个部分。
让我们稍微改写一下这个例子，看看它们之前的区别：
class Greeter {
    static standardGreeting = "Hello, there";
    greeting: string;
    greet() {
        if (this.greeting) {
            return "Hello, " + this.greeting;
        }
        else {
            return Greeter.standardGreeting;
        }
    }
}

let greeter1: Greeter;
greeter1 = new Greeter();
console.log(greeter1.greet());

let greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";

let greeter2: Greeter = new greeterMaker();
console.log(greeter2.greet());
这个例子里，greeter1与之前看到的一样。 我们实例化 Greeter类，并使用这个对象。 与我们之前看到的一样。
再之后，我们直接使用类。 我们创建了一个叫做 greeterMaker的变量。 这个变量保存了这个类或者说保存了类构造函数。 然后我们使用 typeof Greeter，意思是取Greeter类的类型，而不是实例的类型。 或者更确切的说，"告诉我 Greeter标识符的类型"，也就是构造函数的类型。 这个类型包含了类的所有静态成员和构造函数。 之后，就和前面一样，我们在 greeterMaker上使用new，创建Greeter的实例。
###### **把类当做接口使用**
如上一节里所讲的，类定义会创建两个东西：类的实例类型和一个构造函数。 因为类可以创建出类型，所以你能够在允许使用接口的地方使用类。
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};



#### 6.2.10 枚举


#### 6.2.11 函数


#### 6.2.12 泛型

#### 6.2.13 模板



## 6.3 基础语法

