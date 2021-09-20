# effective-typescript

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
### 아이텝 1 타입스크립트와 자바스크립트의 관계 이해하기

- 타입스크립트는 자바스크립트의 상위집합입니다. 모든 자바스크립트 프로그램은 타입스크립트 프로그램이지만, 타입스크립트는 별도의 문법을 가지고 있으므로 반대의 경우는 성립하지 않습니다.

```js
// JavaScript
function greet(who: string) { // SyntaxError: Unexpected token :
  console.log('Hello', who);
}
```
<br/>

- 타입스크립트의 타입 구문은 코드의 '의도'가 무엇인지 알려주어 동작과 의도가 다른 부분을 찾을 수 있도록 도와줍니다.

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
<br/>

- 타입스크립트의 타입 체커를 통과하면서도 런타임 오류를 발생시키는 코드가 존재할 수 있습니다. 타입 시스템은 정적 타입의 정확성을 보장해 주지는 않습니다.

```ts
const names = ['Alice', 'Bob'];
console.log(names[2].toUpperCase());
// TypeError: Cannot read property 'toUpperCase' of undefined
```
<br/>

**[⬆ 상단으로](#목차)**

### 아이텝 2 타입스크립트 설정 이해하기

**안좋은 예:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**좋은 예:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ 상단으로](#목차)**

### 아이텝 3 코드 생성과 타입이 관계없음을 이해하기

**안좋은 예:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**좋은 예:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ 상단으로](#목차)**

### 아이텝 4 구조적 타이핑에 익숙해지기

**안좋은 예:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**좋은 예:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ 상단으로](#목차)**

### 아이텝 5 any 타입 지양하기

**안좋은 예:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**좋은 예:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ 상단으로](#목차)**


