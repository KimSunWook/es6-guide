*이 문서는 https://github.com/lukehoban/es6features 를 번역한 글 https://itstory.tk/entry/JavaScript-ES6-%EB%AC%B8%EB%B2%95-%EC%A0%95%EB%A6%AC을 정리한 내용입니다.*



# ECMAScript 6 가이드


## Introduction

ECMAScript 2015로도 알려져 있는 ECMAScript 6는 ECMAScript 표준의 가장 최신 버전입니다. ES6는 새로운 언어 기능이 포함된 주요 업데이트이며, 2009년도에 표준화된 ES5 이후로 언어 기능에 대한 첫 업데이트이기도 합니다. 현재 주요 JavaScript 엔진들에서 ES6 기능들을 [구현 중](http://kangax.github.io/es5-compat-table/es6/)에 있습니다.

ECMAScript 6 언어의 전체 스펙을 확인하시려면 [ES6 Standard](http://www.ecma-international.org/ecma-262/6.0/)를 확인하세요.

ES6는 아래의 새로운 기능들을 포함하고 있습니다:
- [arrows](#arrows)
- [classes](#classes)
- [enhanced object literals](#enhanced-object-literals)
- [template strings](#template-strings)
- [destructuring](#destructuring)
- [default + rest + spread](#default--rest--spread)
- [let + const](#let--const)
- [iterators + for..of](#iterators--forof)
- [generators](#generators)
- [unicode](#unicode)
- [modules](#modules)
- [module loaders](#module-loaders)
- [map + set + weakmap + weakset](#map--set--weakmap--weakset)
- [proxies](#proxies)
- [symbols](#symbols)
- [subclassable built-ins](#subclassable-built-ins)
- [promises](#promises)
- [math + number + string + array + object APIs](#math--number--string--array--object-apis)
- [binary and octal literals](#binary-and-octal-literals)
- [reflect api](#reflect-api)
- [tail calls](#tail-calls)

## ECMAScript 6 Features

### Arrows
Arrows(화살표) 함수는 => 문법을 사용하는 축약형 함수입니다. C#, Java 8, CoffeeScript의 해당 기능과 문법적으로 유사합니다. Arrows는 표현식의 결과 값을 반환하는 표현식 본문(expression bodies)뿐만 아니라 상태 블럭 본문(statement block bodies)도 지원합니다. 하지만 일반 함수의 자신을 호출하는 객체를 가리키는 `dynamic this`와 달리 arrows 함수는 코드의 상위 스코프(lexical scope)를 가리키는 `lexical this`를 가집니다.

```JavaScript
// Expression bodies
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);
var pairs = evens.map(v => ({even: v, odd: v + 1}));

// Statement bodies
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// Lexical this
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

화살표 함수의 더 자세한 설명은 [MDN Arrow Functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)를 참고하세요.
printFriends() 함수의 선언 표기법이 궁금하시면 [MDN Object Initializer](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Object_initializer)를 참고하세요.



### Classes
ES6 클래스는 프로토타입 기반 객체지향 패턴을 더 쉽게 사용할 수 있는 대체재입니다. 클래스 패턴 생성을 더 쉽고 단순하게 생성할 수 있어서 사용하기도 편하고 상호운용성도 증가됩니다.

```JavaScript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    this.idMatrix = SkinnedMesh.defaultMatrix();
    this.bones = [];
    this.boneMatrices = [];
    //...
  }
  update(camera) {
    //...
    super.update();
  }
  get boneCount() {
    return this.bones.length;
  }
  set matrixType(matrixType) {
    this.idMatrix = SkinnedMesh[matrixType]();
  }
  static defaultMatrix() {
    return new THREE.Matrix4();
  }
}
```

더 자세한 설명은 [MDN Classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)를 참고하세요.



### Enhanced Object Literals
Object literals are extended to support setting the prototype at construction, shorthand for `foo: foo` assignments, defining methods, making super calls, and computing property names with expressions.  Together, these also bring object literals and class declarations closer together, and let object-based design benefit from some of the same conveniences.

```JavaScript
var obj = {
    // __proto__
    __proto__: theProtoObj,
    // Shorthand for ‘handler: handler’
    handler,
    // Methods
    toString() {
     // Super calls
     return "d " + super.toString();
    },
    // Computed (dynamic) property names
    [ 'prop_' + (() => 42)() ]: 42
};
```

More info: [MDN Grammar and types: Object literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Object_literals)



### Template Strings
Template Strings(ES6 부터는 Template literals라 부름)는 문법적으로 더 편하게 string을 생성할 수 있게 합니다. 이는 Perl, Python 등의 문자열 보간(string interpolation)과 유사합니다. Tagged template literals는 인젝션 공격 방어 혹은 문자열로 부터 상위 데이터 구조체 재조립 등을 위해 string 생성을 커스터마이징이 가능하게끔 해줍니다.

```JavaScript
// Basic literal string creation
`In JavaScript '\n' is a line-feed.`

// Multiline strings
`In JavaScript this is
 not legal.`

// String interpolation
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// Construct an HTTP request prefix is used to interpret the replacements and construction
POST`http://foo.org/bar?a=${a}&b=${b}
     Content-Type: application/json
     X-Credentials: ${credentials}
     { "foo": ${foo},
       "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

더 자세한 설명은 [MDN Template Strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings)를 참고하세요.



### Destructuring
Destructuring는 배열과 객체에 패턴 매칭을 통한 데이터 바인딩을 제공합니다. Destructuring는 할당 실패에 유연하며, 실패 시 `undefined` 값이 자동할당 됩니다. 또한 `foo["bar"]`와 같이 객체의 속성 값도 자동으로 검색하여 바인딩해줍니다.

```JavaScript
// list matching
var [a, , b] = [1,2,3];

// object matching
var { op: a, lhs: { op: b }, rhs: c }
       = getASTNode()

// object matching shorthand
// binds `op`, `lhs` and `rhs` in scope
var {op, lhs, rhs} = getASTNode()

// Can be used in parameter position
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// Fail-soft destructuring
var [a] = [];
a === undefined;

// Fail-soft destructuring with defaults
var [a = 1] = [];
a === 1;
```

More info: [MDN Destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)



### Default + Rest + Spread
파라미터에 기본 값을 설정할 수 있습니다.
Callee-evaluated default parameter values.  Turn an array into consecutive arguments in a function call.  Bind trailing parameters to an array.  Rest replaces the need for `arguments` and addresses common cases more directly.

```JavaScript
function f(x, y=12) {
  // y is 12 if not passed (or passed as undefined)
  return x + y;
}
f(3) == 15
```

가변인자를 사용가능하며, 배열로 치환시켜 줍니다. Rest parameters는 `arguments` 보다 직관성을 제공합니다.
```JavaScript
function f(x, ...y) {
  // y is an Array
  return x * y.length;
}
f(3, "hello", true) == 6
```

함수 호출 시 배열을 일련의 인자에 나누어 주입시켜 줍니다.
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// Pass each elem of array as argument
f(...[1,2,3]) == 6
```

더 자세한 설명은 [Default parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters), [Rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters), [Spread Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)를 참고하세요.



### Let + Const
                                                                                        
블록 유효 범위를 갖는 새로운 변수 선언 방법을 지원합니다. `let`은 `var`와 유사하게 동작합니다. `const`는 재할당 및 재선언이 불가능합니다. `var`의 유효 범위는 전체 외부 함수까지이지만 `let`은 변수를 선언한 블록과 그 내부 블록들에서 유효합니다.

```JavaScript
function f() {
  {
    let x;
    {
      // okay, block scoped name
      const x = "sneaky";
      // error, const
      x = "foo";
    }
    // error, already declared in block
    let x = "inner";
  }
}
```

더 자세한 설명은 [let statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), [const statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)를 참고하세요.



### Iterators + For..Of

Iterator 객체는 CLR의 IEnumerable 혹은 Java의 Iterable처럼 사용자 정의의 반복을 가능하게 해줍니다. `for..of` 반복문이 ES6에서 추가 되었으며 `for..in` 반복문과 달리 iterator 기반의 컬렉션 전용 반복문입니다. for in 반복문과의 차이점은 [for in vs for of](https://itstory.tk/entry/Javascript-for-in-vs-for-of-%EB%B0%98%EB%B3%B5%EB%AC%B8)를 참고하세요.

```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

Iteration은 아래의 duck-type 인터페이스를 기반으로 합니다. (설명을 위해 [TypeScript](http://typescriptlang.org)의 타입 문법을 사용하였습니다)

```TypeScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

더 자세한 설명은 [MDN for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)를 참고하세요.



### Generators

Generators는 `function*`와 `yield` 키워드를 이용하여 iterator 선언을 단순하게 작성할 수 있게 도와줍니다. `function*`로 선언한 함수는 Generator 객체를 반환합니다. Generators는 iterator의 하위 타입이며 `next`와 `throw` 메서드를 가지고 있습니다. 이 메서드들로 인해 `yield` 키워드로 반환된 값은 다시 generator에 주입거나 예외처리를 할 수 있게 되었습니다.

참고: 해당 키워드는 비동기 프로그래밍의 `await` 같은 기능이 가능하게끔 기반이 되었습니다. ES7의 await 제안서를 참고하세요.

```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

generator 인터페이스는 다음과 같습니다. (설명을 위해 [TypeScript](http://typescriptlang.org)의 타입 문법을 사용하였습니다)

```TypeScript
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```

더 자세한 설명은 [MDN Iteration protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)를 참고하세요.



### Unicode

완전한 유니코드를 지원하기 위해 문자열에 새로운 유니코드 리터럴과 정규표현식에 u 모드가 추가되었습니다. 또한 21비트 형식까지 처리하기 위한 신규 API가 추가되었습니다. 이 추가된 기능은 JavaScript로 글로벌 앱을 만들 수 있도록 지원합니다.

```JavaScript
// same as ES5.1
"𠮷".length == 2

// new RegExp behaviour, opt-in ‘u’
"𠮷".match(/./u)[0].length == 2

// new form
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// new String ops
"𠮷".codePointAt(0) == 0x20BB7

// for-of iterates code points
for(var c of "𠮷") {
  console.log(c);
}
```

더 자세한 설명은 [MDN RegExp.prototype.unicode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode)를 참고하세요.



### Modules

언어 차원에서 컴포넌트 정의를 위한 모듈을 지원합니다. 유명한 JavaScript 모듈 로더들(AMD, CommonJS)의 패턴을 적용시켰습니다. 런타임 동작은 호스트에 정의된 기본 로더에 의해 정의됩니다. 묵시적 비동기 형태로 요구되는 모듈들이 정상적으로 로드되기 전까지 코드가 실행되지 않습니다.

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
import * as math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```

`export default`와 `export *` 문법도 제공합니다.

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.log(x);
}
```
```JavaScript
// app.js
import ln, {pi, e} from "lib/mathplusplus";
alert("2π = " + ln(e)*pi*2);
```

더 자세한 내용은 [import statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), [export statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)를 참고하세요.



### Module Loaders

Module Loaders는 다음을 지원합니다.

- 동적 로딍(Dynamic loading)
- 상태 격리(State isolation)
- 전역 네임스페이스 격리(Global namespace isolation)
- 컴파일 훅(Compilation hooks)
- 중첩 가상화(Nested virtualization)

기본으로 사용할 모듈 로더를 설정할 수 있으며, 로더를 새로 생성하여 격리되거나 제한된 맥락에서 코드를 로드할 수 있습니다.

```JavaScript
// Dynamic loading – ‘System’ is default loader
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// Create execution sandboxes – new Loaders
var loader = new Loader({
  global: fixup(window) // replace ‘console.log’
});
loader.eval("console.log('hello world!');");

// Directly manipulate module cache
System.get('jquery');
System.set('jquery', Module({$: $})); // WARNING: not yet finalized
```



### Map + Set + WeakMap + WeakSet

일반 알고리즘을 위한 효율적인 데이터 구조를 제공합니다. WeakMap과 WeakSet는 메모리 누수로 부터 자유롭게 해줍니다. 이들 내 저장된 객체에 다른 참조가 없는 경우, garbage collection 될 수 있습니다.

```JavaScript
// Sets
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Maps
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined // undefined (사용된 곳이 없기 때문)

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
wm.size === undefined // undefined (사용된 곳이 없기 때문)
```

더 자세한 내용은 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set), [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap), [WeakSet](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)를 참고하세요.



### Proxies

프록시(Proxy)를 사용하면 호스트 객체에 다양한 기능을 추가하여 객체를 생성할 수 있습니다. interception, 객체 추상화, 로깅/수집, 값 검증 등에 사용될 수 있습니다.

```JavaScript
// Proxying a normal object
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```JavaScript
// Proxying a function object
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

proxy의 `handler`가 가질 수 있는 트랩(trap)들입니다.

```JavaScript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

더 자세한 내용은 [MDN Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)를 참고하세요.



### Symbols

심볼(Symbol)은 객체 상태의 접근 제어를 가능하게 합니다. Symbol은 새로운 원시 타입으로 이름 충돌의 위험 없이 속성(property)의 키(key)로 사용할 수 있습니다. 옵션 파라미터인 `description`는 디버깅 용도로 사용되며 식별 용도는 아닙니다. Symbol은 고유(unique)하며, `Object.getOwnPropertySymbols`와 같은 reflection 기능들로 접근할 수 있기 때문에 private 하진 않습니다(`for in`나 `Object.keys()`로는 접근 불가).

```JavaScript
var MyClass = (function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

  return MyClass;
})();

var c = new MyClass("hello")
c["key"] === undefined
```

더 자세한 내용은 [ES6 In Depth: 심볼 (Symbol)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)를 참고하세요.



### Subclassable Built-ins

ES6에서 `Array`, `Date`, DOM `Element` 같이 내장 객체들은 상속이 가능합니다. 객체 생성 시 호출되는 `Ctor` 함수는 다음의 2단계를 가집니다.(둘다 가상적으로 실행)

객체 할당을 위해 `Ctor[@@create]` 호출하여
- 새로운 인스턴스의 생성자를 호출해 초기화 진행
- 아시다싶이 `@@create` 심볼은 `Symbol.create`를 통해 만들어졌습니다.

```JavaScript
// Pseudo-code of Array
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // Install special [[DefineOwnProperty]]
        // to magically update 'length'
    }
}

// User code of Array subclass
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// Two-phase 'new':
// 1) Call @@create to allocate object
// 2) Invoke constructor on new instance
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```



### Math + Number + String + Array + Object APIs

core Math 라이브러리, Array 생성 helper, String helper, 복사를 위한 Object.assign 등 많은 라이브러리들이 추가되었습니다.

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".includes("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // Returns a real Array
Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1) // [0,7,7]
[1, 2, 3].find(x => x == 3) // 3
[1, 2, 3].findIndex(x => x == 2) // 1
[1, 2, 3, 4, 5].copyWithin(3, 0) // [1, 2, 3, 1, 2]
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

More MDN info: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number), [Math](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math), [Array.from](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from), [Array.of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/of), [Array.prototype.copyWithin](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin), [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)



### Binary and Octal Literals

2진법 (b), 8진법 (o) numeric 리터럴 형식이 추가되었습니다.

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```



### Promises

Promise는 비동기 프로그래밍을 위한 라이브러리입니다. Promise는 미래에 생성되는 값을 나타내는 일급 객체입니다. Promise는 현존하는 많은 JavaScript 라이브러리에 사용되고 있습니다.

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

더 자세한 내용은 [MDN Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 참고하세요.



### Reflect API

Reflection API는 런타임 시 객체에 대해 작업을 수행할 수 있습니다. 프록시 트랩(proxy traps)와 같은 메타 함수들을 가지고 있습니다. Reflection은 프록시를 구현하는데 유용합니다.

```JavaScript
// No sample yet
```

더 자세한 내용은 [MDN Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)를 참고하세요.



### Tail Calls

마지막에 호출되는 함수가 호출 스택이 초과되게 하지 않습니다. 재귀 알고리즘을 매우 큰 입력 값에서도 안전하게 만듭니다.

```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// Stack overflow in most implementations today,
// but safe on arbitrary inputs in ES6
factorial(100000)
```
