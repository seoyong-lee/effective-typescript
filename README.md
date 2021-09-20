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

## **변수(Variables)**
### 의미있고 발음하기 쉬운 변수 이름을 사용하세요

**안좋은 예:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**좋은 예:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ 상단으로](#목차)**
