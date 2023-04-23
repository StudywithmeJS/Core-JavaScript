# 불변값과 가변값

## 1. 기본형 데이터와 참조형 데이터

### 1) 상수와 불변값

- 불변값과 상수를 같은 개념으로 오해하기 쉬움
- 변수와 상수를 구분 짓는 변경 가능성의 대상은 변수 영역의 메모리 (한번 데이터 할당이 이뤄진 변수 공간에 다른 데이터를 재할당 할 수 있는지 여부)
- `불변성 여부를 구분할 때 변경 가능성의 대상은 데이터 영역`
  - 기본형 데이터인 숫자, 문자열, boolean, null, undefined, Symbol 모두 불변값
    ```js
    var a = "abc";
    a = a + "def"; // 데이터 영역의 'abc'가 'abcdef' 로 변경 되는 것이 아닌
    // 새로운 문자열 'abcdef'를 만들어 그 주소를 변수 a에 저장
    ```
    ![](https://velog.velcdn.com/images/ses2201/post/06161e98-3179-415b-9d0e-9972a80030fc/image.jpg)

### 2) 가변값

- 참조형데이터를 변수에 할당하는 과정
  - 기본형 데이터와의 차이는 '객체의 변수(프로퍼티) 영역'이 별도로 존재한다는 점.
  - `데이터 영역에 저장된 값은 모두 불변값이나, 변수영역에는 다른 값을 얼마든지 대입할 수 있음.` 이부분 때문에 참조형데이터는 불변하지 않다, `가변값이다` 라고 하는 것임
    ```js
    var obj1={
      a: 1;
      b: 'bbb';
    }
    obj1.a = 2; // 새로운 객체가 만들어져서 할당되는 것이 아닌, 객체 변수 영역의 값이 변경
    ```
    ![](https://velog.velcdn.com/images/ses2201/post/794a3059-f2c4-497f-8ade-b17df9e24214/image.jpg)

### 3) 변수복사비교

```js
var a = 10;
var b = a;
var obj1 = { c: 10, d: "ddd" };
var obj2 = obj1;

b = 15;
obj2.c = 20;

console.log(a === b); // false
console.log(obj1 === obj2); // true
```

변수 a와 b는 서로 다른 주소를 바라보게 되었기 때문에, a === b를 console로 찍었을 때, false가 나온다. 반면 obj1과 obj2는 객체 안의 프로퍼티 값이 바뀌었을 뿐, 여전히 같은 값을 바라보고 있기 때문에 true가 나온다.

---

## 2. 불변객체

### 1) 어떤 상황에서 불변객체가 필요할까?

- 값으로 전달받은 객체에 변경을 가하더라도 원본 객체는 변하지 않아야 하는 경우가 발생

- 문제상황)

  ```js
  var user = {
    name: "Jaenam",
    gender: "male",
  };

  var changeName = function (user, newName) {
    var newUser = user;
    newUser.name = newName;
    return newUser;
  };

  var user2 = changeName(user, "Jung");

  console.log(user.name, user2.name); // Jung Jung
  console.log(user === user2); // true
  ```

- 해결방법)

  ```js
  var user = {
    name: "Jaenam",
    gender: "male",
  };

  var copyObejct = function (user) {
    var result;
    result = { ...user };
    return result;
  };

  var user2 = copyObejct(user);
  user2.name = "Jung";

  console.log(user.name, user2.name); // Jaenam Jung
  console.log(user === user2); // false
  ```

  <br>

### 2) 얕은복사와 깊은 복사

- `얕은 복사`는 바로 아래 단계의 값만 복사하는 방법이고,
  `깊은 복사`는 내부의 모든 값들을 하나하나 찾아서 전부 복사하는 방법.

- **얕은 복사**

  ```js
  var user = {
    name: "Jaenam",
    urls: {
      portfolio: "https://github.com/abc",
      blog: "http://blog.com",
      facebook: "http://facebook.com/abc",
    },
  };

  var user2 = copyObejct(user);
  user2.name = "Jung";
  user.urls.portfolio = "http:portfolio.com";
  console.log(user.name === user2.name); // false
  console.log(user.urls.portfolio === user2.urls.portfolio); // true
  ```

  user객체에 직접 속한 프로퍼티에 대해 복사해서 완전히 새로운 데이터가 만들어진 반면, 한 단계 더 들어간 urls의 내부 프로퍼티들은 기존 데이터를 그대로 참조함.

- **깊은 복사**

  - 객체의 깊은 복사를 수행하는 범용함수

    ```js
    var copyObjectDeep = function (target) {
      var result = {};
      if (typeof target === "object" && target !== null) {
        for (var prop in target) {
          result[prop] = copyObjectDeep(target[prop]);
        }
      } else {
        result = target;
      }
      return result;
    };
    ```

    target이 객체인 경우에는 내부 프로퍼티들을 순회하며 copyObjectDeep 함수를 `재귀적으로 호출`.

  - JSON을 활용한 간단한 깊은 복사

    ```js
    var copyObjectViaJSON = function (target) {
      return JSON.parse(JSON.stringify(target));
    };
    ```

    [JSON과 JS Object의 차이점](https://velog.io/@kysung95/%EA%B0%9C%EB%B0%9C%EC%83%81%EC%8B%9D-JSON%EA%B3%BC-JavaScript-Object%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)
