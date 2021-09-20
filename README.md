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

- 타입스크립트는 자바스크립트의 상위집합입니다. 모든 자바스크립트 프로그램은 타입스크립트 프로그램이지만, 타입스크립트는 별도의 문법을 가지고 있으므로 반대의 경우는 성립하지 않습니다.<ul>
<details>
<summary>예시 코드</summary>
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

- 타입스크립트의 타입 구문은 코드의 '의도'가 무엇인지 알려주어 동작과 의도가 다른 부분을 찾을 수 있도록 도와줍니다.<ul>
<details>
<summary>예시 코드</summary>
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

- 타입스크립트의 타입 체커를 통과하면서도 런타임 오류를 발생시키는 코드가 존재할 수 있습니다. 타입 시스템은 정적 타입의 정확성을 보장해 주지는 않습니다.<ul>
<details>
<summary>예시 코드</summary>
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

- 타입스크립트 설정은 커맨드 라인으로도 할 수 있지만, 협업을 위해서 되도록 tsconfig.json을 사용하는 것이 좋습니다.<ul>
<details>
<summary>예시 코드</summary>
<div markdown="1"><br/>
  
```cli
$ tsc --init

// tsconfig.json 생성
```
  
</div>
</details>
</ul>

- 자바스크립트 프로젝트를 타입스크립트로 전환하는 경우가 아니라면 `noImplicitAny`를 설정하는 것이 좋습니다.<ul>
<details>
<summary>예시 코드</summary>
<div markdown="1"><br/>
  
**나쁜 예:** 
```ts
// tsConfig: {"noImplicitAny":false}

function add(a, b) {
  return a + b;
}
```
  
**좋은 예:**  
```ts
// tsConfig: {"noImplicitAny":true}

function add(a, b) {
          // ~    Parameter 'a' implicitly has an 'any' type
          //    ~ Parameter 'b' implicitly has an 'any' type
  return a + b;
}
```
  
</div>
</details>
</ul>

- "undefined는 객체가 아닙니다"와 같은 런타임 오류를 방지하기 위해 `strictNullChecks`를 설정하는 것이 좋습니다.<ul>
<details>
<summary>예시 코드</summary>
<div markdown="1"><br/>
  
**나쁜 예:**
```ts
// tsConfig: {"noImplicitAny":true,"strictNullChecks":false}

const x: number = null;  // OK, null is a valid number
```

**좋은 예:**
```ts
// tsConfig: {"noImplicitAny":true,"strictNullChecks":true}

const x: number = null;
//    ~ Type 'null' is not assignable to type 'number'
```
  
</div>
</details>
</ul>

- 위의 모든 체크를 설정하고 싶다면 strict 설정을 할 수 있습니다. strict 설정은 대부분의 오류를 잡아냅니다.<ul>
<details>
<summary>예시 코드</summary>
<div markdown="1"><br/>
  
```ts
// tsConfig
    
{"strict": true}
```
  
</div>
</details>
</ul>

### 아이템 3 코드 생성과 타입이 관계없음을 이해하기

- 타입 시스템과 컴파일은 무관합니다. 또한, 타입 오류가 있는 코드도 컴파일은 가능합니다.
- 런타임에는 타입 체크가 불가능합니다.
- 타입 연산은 런타임 성능에 영향을 주지 않습니다.
- 런타임 타입은 선언된 타입과 다를 수 있습니다.
- 타입스크립트 타입으로는 함수를 오버로드할 수 없습니다.

### 아이템 4 구조적 타이핑에 익숙해지기

- 자바스크립트가 덕 타이핑(duck typing) 기반이므로 이를 모델링하기 위해 타입스크립트가 구조적 타이핑을 사용함을 이해해야 합니다.
- 클래스 역시 구조적 타이핑 규칙을 따르기 때문에 클래스의 인스턴스가 예상과 다를 수 있습니다.
- 구조적 타이핑을 사용하면 유닛 테스팅을 쉽게 할 수 있습니다.

### 아이템 5 any 타입 지양하기

- any타입에는 타입 안정성이 없습니다.
- any는 함수 시그니처를 무시해 버립니다.
- any 타입에는 언어 서비스가 적용되지 않습니다.
- any 타입은 코드 리팩토링 때 버그를 감춥니다.
- any는 타입 설계를 감춰버립니다.
- any는 타입시스템의 신뢰도를 떨어뜨립니다.

**[⬆ 상단으로](#목차)**
