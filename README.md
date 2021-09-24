# effective-typescript
- Dan VanderKam의 Effective TypeScript에 대한 정리글입니다.

## 목차

1. [타입스크립트 알아보기](#타입스크립트-알아보기)
2. [타입스크립트의 타입 시스템](#타입스크립트의-타입-시스템)
3. [타입 추론](#타입-추론)
4. [타입 설계](#타입-설계)
5. [any 다루기](#any-다루기)
6. [타입 선언과 @types](#타입-선언과-@types)
7. [코드를 작성하고 실행하기](#코드를-작성하고-실행하기)
8. [타입스크립트로 마이그레이션하기](#타입스크립트로-마이그레이션하기)

## **타입스크립트 알아보기**
### 아이템 1 타입스크립트와 자바스크립트의 관계 이해하기

<ul>
<details>
<summary>타입스크립트는 자바스크립트의 상위집합입니다. 모든 자바스크립트 프로그램은 타입스크립트 프로그램이지만, 타입스크립트는 별도의 문법을 가지고 있으므로 반대의 경우는 성립하지 않습니다.</summary>
<div markdown="1"><br/>
  
```js
// JavaScript
function greet(who: string) { // SyntaxError: Unexpected token :
  console.log('Hello', who);
}
```
자바스크립트에선 위와 같은 타입스크립트 문법을 사용할 수 없습니다. 그러나 타입스크립트에서는 자바스크립트의 모든 문법을 사용할 수 있습니다.
<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>타입스크립트의 타입 구문은 코드의 '의도'가 무엇인지 알려주어 동작과 의도가 다른 부분을 찾을 수 있도록 도와줍니다.</summary>
<div markdown="1"><br/>
  
```ts
interface State {
  name: string;
  capital: string; // 의도
}

const states: State[] = [
  {name: 'Alabama', capital: 'Montgomery'},
  {name: 'Alaska',  capitol: 'Juneau'}, // 동작
                 // ~~~~~~~~~~~~~~~~~ Did you mean to write 'capital'?
  {name: 'Arizona', capital: 'Phoenix'},
  // ...
];
```
명시적으로 states를 선언하면 코드 작성자의 의도를 타입스크립트에게 알려줄 수 있고(capital), 만약 잘못된 동작(capitol)이 발생하였다면 타입스크립트는 에러와 함께 해결 방법을 추천해줍니다.<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>타입스크립트의 타입 체커를 통과하면서도 런타임 오류를 발생시키는 코드가 존재할 수 있습니다. 타입 시스템은 정적 타입의 정확성을 보장해 주지는 않습니다.</summary>
<div markdown="1"><br/>
  
```ts
const names = ['Alice', 'Bob'];
console.log(names[2].toUpperCase());
// TypeError: Cannot read property 'toUpperCase' of undefined
```
타입은 정확하지만 존재하지 않는 인덱스에 접근하려 시도하였기 때문에 에러가 발생합니다.<br/><br/>
  
</div>
</details>
</ul>

### 아이템 2 타입스크립트 설정 이해하기

<ul>
<details>
<summary>타입스크립트 설정은 커맨드 라인으로도 할 수 있지만, 협업을 위해서 되도록 tsconfig.json을 사용하는 것이 좋습니다.</summary>
<div markdown="1"><br/>
  
```cli
$ tsc --init
```
위 명령어를 입력하면 루트 디렉토리에 tsconfig.json 파일을 만들어줍니다.<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>자바스크립트 프로젝트를 타입스크립트로 전환하는 경우가 아니라면 `noImplicitAny`를 설정하는 것이 좋습니다.</summary>
<div markdown="1"><br/>
  
**나쁜 예:** 
```ts
// tsConfig: {"noImplicitAny":false}

function add(a, b) {
  return a + b;
}
```
`noImplicitAny`가 해제된 경우 함수의 타입이 설정되지 않아도 자동적으로 any로 추론됩니다.
  
**좋은 예:**  
```ts
// tsConfig: {"noImplicitAny":true}

function add(a, b) {
          // ~    Parameter 'a' implicitly has an 'any' type
          //    ~ Parameter 'b' implicitly has an 'any' type
  return a + b;
}
```
`noImplicitAny`가 설정된 경우 명시적으로 any 타입을 설정하지 않는 이상 추론된 결과가 any라면 에러가 발생합니다.<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>"undefined는 객체가 아닙니다"와 같은 런타임 오류를 방지하기 위해 `strictNullChecks`를 설정하는 것이 좋습니다.</summary>
<div markdown="1"><br/>
  
**나쁜 예:**
```ts
// tsConfig: {"noImplicitAny":true,"strictNullChecks":false}

const x: number = null;  // OK, null is a valid number
```
`strictNullChecks`을 설정하지 않는 경우 null과 undefined가 허용됩니다.

**좋은 예:**
```ts
// tsConfig: {"noImplicitAny":true,"strictNullChecks":true}

const x: number = null;
//    ~ Type 'null' is not assignable to type 'number'
```
`strictNullChecks`을 설정한 경우 null과 undefined를 명시적으로 허용하지 않는 이상 오류를 발생시킵니다. 이러한 방식은 코드 작성을 어렵게 하지만 "undefined는 객체가 아닙니다"와 같은 null과 undefined와 관련  런타임 오류를 잡아내는 데 많은 도움을 줍니다.<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>위의 모든 체크를 설정하고 싶다면 strict 설정을 할 수 있습니다. strict 설정은 대부분의 오류를 잡아냅니다.</summary>
<div markdown="1"><br/>
  
```ts
// tsConfig
    
{"strict": true}
```
`noImplicitAny`와 `strictNullChecks`는 모두 strict 설정 안에 포함되어있습니다.<br/><br/>
  
</div>
</details>
</ul>

### 아이템 3 코드 생성과 타입이 관계없음을 이해하기

<ul>
<details>
<summary>타입 시스템과 컴파일은 무관합니다. 또한, 타입 오류가 있는 코드도 컴파일은 가능합니다.</summary>
<div markdown="1"><br/>
  
```ts
// test.ts
let x = 'hello';
x = 1234;
  
// $ tsc test.ts
// test.ts:2:1 - error TS2322: Type 'number' is not assignable to type 'string'.
// 2 x = 1234;
  
// test.js
var x = 'hello';
x = 1234;  
```
타입 오류가 발생하는 ts 파일에 tsc 명령어(타입스크립트 파일을 자바스크립트 파일로 컴파일)를 사용하면 오류에도 불구하고 자바스크립트 파일이 문제 없이 생성되는 것을 볼 수 있습니다.<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>런타임에는 타입 체크가 불가능합니다.</summary>
<div markdown="1"><br/>
    
**나쁜 예:** 
```ts
interface Square {
  width: number;
}
interface Rectangle extends Square {
  height: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) {
                    // ~~~~~~~~~ 'Rectangle' only refers to a type,
                    //           but is being used as a value here
    return shape.width * shape.height;
                    //         ~~~~~~ Property 'height' does not exist
                    //                on type 'Shape'
  } else {
    return shape.width * shape.width;
  }
}
```
만약 위와 같이 작성하면 instanceof 체크는 런타임에 일어나지만, Rectangle은 타입이기 때문에 컴파일 과정에서 사라져 오류를 발생시킵니다.
  
  **좋은 예:** 
```ts
interface Square {
  kind: 'square';
  width: number;
}
interface Rectangle {
  kind: 'rectangle';
  height: number;
  width: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape.kind === 'rectangle') {
    shape;  // Type is Rectangle
    return shape.width * shape.height;
  } else {
    shape;  // Type is Square
    return shape.width * shape.width;
  }
}
```
위와 같이 런타임에 접근 가능한 타입 정보를 명시적으로 저장하는 '태그' 기법을 사용하면 오류를 발생시키지 않습니다.
  
**좋은 예:** 
```ts
class Square {
  constructor(public width: number) {}
}
class Rectangle extends Square {
  constructor(public width: number, public height: number) {
    super(width);
  }
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) {
    shape;  // Type is Rectangle
    return shape.width * shape.height;
  } else {
    shape;  // Type is Square
    return shape.width * shape.width;  // OK
  }
}
```
타입을 클래스로 만드는 방법을 사용할 수도 있습니다. 이러한 방식을 이용하면 Rectangle은 타입이자 값으로 모두 사용할 수 있으므로 오류를 발생시키지 않습니다.<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>타입 연산은 런타임에 영향을 주지 않습니다. 따라서 타입은 런타임 성능과도 무관합니다.</summary>
<div markdown="1"><br/>
    
**나쁜 예:** 
```ts
// TypeScript  
function asNumber(val: number | string): number {
  return val as number;
}

// Javascript  
function asNumber(val) {
  return val;
}
```
만약 string이나 number 타입인 값을 항상 number 타입으로 정제하기 위해 위와 같이 작성하였다면 이는 아무런 기능을 하지 못합니다. as number는 타입 연산이고 런타임 동작과는 무관하기 때문입니다.
  
**좋은 예:** 
```ts
function asNumber(val: number | string): number {
  return typeof(val) === 'string' ? Number(val) : val;
}
```
값을 제대로 정제하기 위해서는 자바스크립트 연산을 통해 변환을 수행해야 합니다. 이처럼 타입과 타입 연산자는 자바스크립트 변환 시점에 제거되기 때문에 런타임의 성능에 아무런 영향을 주지 않습니다.
<br/><br/>
  
</div>
</details>
</ul>
  
<ul>
<details>
<summary>런타임 타입은 선언된 타입과 다를 수 있습니다.</summary>
<div markdown="1"><br/>
    
```ts
interface LightApiResponse {
  lightSwitchValue: boolean;
}
async function setLight() {
  const response = await fetch('/light');
  const result: LightApiResponse = await response.json();
  setLightSwitch(result.lightSwitchValue);
}
```
위와 같이 /light를 요청하면 그 결과로 LightApiResponse를 반환하는 함수의 경우 lightSwitchValue를 boolean으로 설정하였지만 실제 응답의 타입이 boolean으로 보장되는 것은 아닙니다.<br/><br/>
  
</div>
</details>
</ul>

<ul>
<details>
<summary>타입스크립트 타입으로는 함수를 오버로드할 수 없습니다.</summary>
<div markdown="1"><br/>
    
```ts
function add(a: number, b: number) { return a + b; }
      // ~~~ Duplicate function implementation
function add(a: string, b: string) { return a + b; }
      // ~~~ Duplicate function implementation
```
C++과 같은 언어는 동일한 이름에 매개변수만 다른 여러 버전의 함수를 허용하는 '함수 오버로딩'이 가능하지만, 타입스크립트는 타입과 런타임의 동작이 무관하기 때문에 이러한 '함수 오버로딩'이 불가능합니다.<br/><br/>
  
</div>
</details>
</ul>

### 아이템 4 구조적 타이핑에 익숙해지기 (준비중)

- 자바스크립트가 덕 타이핑(duck typing) 기반이므로 이를 모델링하기 위해 타입스크립트가 구조적 타이핑을 사용함을 이해해야 합니다.
- 클래스 역시 구조적 타이핑 규칙을 따르기 때문에 클래스의 인스턴스가 예상과 다를 수 있습니다.
- 구조적 타이핑을 사용하면 유닛 테스팅을 쉽게 할 수 있습니다.

### 아이템 5 any 타입 지양하기

<ul>
<details>
<summary>any타입에는 타입 안정성이 없습니다.</summary>
<div markdown="1"><br/>
    
```ts
let age: number;
age = '12';
// ~~~ Type '"12"' is not assignable to type 'number'
age = '12' as any;  // OK
  
age += 1;  // OK; at runtime, age is now "121"
```
타입 단언문(as any)을 사용하면 string 타입을 할당할 수 있으나, 타입 체커는 계속해서 number 타입으로 판단하기 때문에 1을 더하면 "121"라는 잘못된 값이 됩니다.<br/><br/>

</div>
</details>
</ul>

<ul>
<details>
<summary>any는 함수의 약속을 무시해 버립니다.</summary>
<div markdown="1"><br/>
    
```ts
function calculateAge(birthDate: Date): number {
  // ...
}

let birthDate: any = '1990-01-19';
calculateAge(birthDate);  // OK
```
birthData 매개변수는 string이 아닌 Date 타입이어야 합니다. any 타입을 사용하면 calculateAge의 타입을 무시하게 됩니다.<br/><br/>

</div>
</details>
</ul>

<ul>
<details>
<summary>any 타입에는 언어 서비스가 적용되지 않습니다.</summary>
<div markdown="1"><br/>
    
```ts
interface Person {
  firstName: string;
  last: string;
}
const formatName = (p: Person) => `${p.firstName} ${p.last}`;
const formatNameAny = (p: any) => `${p.first} ${p.last}`;
```
타입스크립트 언어 서비스는 자동완성 기능과 적절한 도움말을 제공합니다. 위의 경우처럼 Person 타입의 first가 firstName으로 변경되어도 any 타입의 심볼은 자동으로 반영되지 않습니다.<br/><br/>

</div>
</details>
</ul>

- any 타입은 코드 리팩토링 때 버그를 감춥니다.
- any는 타입 설계를 감춰버립니다.
- any는 타입시스템의 신뢰도를 떨어뜨립니다.

**[⬆ 상단으로](#목차)**

## **타입스크립트의 타입 시스템**
### 아이템 6 편집기를 사용하여 타입 시스템 탐색하기
- 편집기를 사용하면 타입 시스템의 동작 원리와 타입 추론에 대한 개념을 잡을 수 있습니다.

### 아이템 7 타입이 값들의 집합이라고 생각하기
- 타입을 값의 집합으로 생각하면 이해하기 편합니다. 
- 타입스크립트 타입은 엄격한 상속 관계가 아니라 겹쳐지는 집합으로 표현됩니다.

### 아이템 8 타입 공간과 값 공간의 심벌 구분하기
- 타입스크립트 코드에서 타입과 값을 구분하는 방법을 터득해야 합니다. 모든 값은 타입을 가지지만, 타입은 값을 가지지 않습니다. 
- type과 interface는 타입 공간에만 존재합니다.
- class와 enum은 타입과 값으로 모두 사용될 수 있습니다.

### 아이템 9 타입 단언보다는 타입 선언을 사용하기
- 타입 단언(as Type) 보다 타입 선언(: Type)을 사용해야 합니다.
- 화살표 함수의 반환 타입을 명시하는 방법을 터득해야 합니다.
- 타입스크립트보다 타입 정보를 더 잘 알고 있는 상황에서는 타입 단언문과 null 아님 단언문을 사용하면 됩니다.

### 아이템 10 객체 래퍼 타입 피하기
- 타입스크립트 객체 래퍼 타입은 지양하고, 대신 기본형 타입을 사용해야 합니다.

### 아이템 11 잉여 속성 체크의 한계 인지하기
- 

### 아이템 12 함수 표현식에 타입 적용하기

### 아이템 13 타입과 인터페이스의 차이점 알기

### 아이템 14 타입 연산과 제너릭 사용으로 반복 줄이기

### 아이템 15 동적 데이터에 인덱스 시그니처 사용하기

### 아이템 16 number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기

### 아이템 17 변경 관련 오류 방지를 위해 readonly 사용하기

### 아이템 18 매핑된 타입을 사용하여 값을 동기화하기

**[⬆ 상단으로](#목차)**
