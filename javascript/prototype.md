# Prototype

✔️ 자바스크립트는 프로토타입 기반 객체지향 프로그래밍 언어

{% hint style="info" %}
클래스는?

* ES6에서 도입된 클래스는 프로토타입 기반 패턴의 syntax sugar라고 볼 수 있다
* 클래스와 생성자 함수는 모두 프로토타입 기반의 인스턴스를 생성
  * 클래스: 생성자 함수보다 엄격하고 더 많은 기능을 제공
* 클래스를 syntax sugar보다 새로운 객체 생성 메커니즘으로 보는 관점…… 도 있다
{% endhint %}

### 객체지향 프로그래밍

* 속성: 특징이나 설질을 나타냄
* 추상화: 다양한 속성 중 필요한 속성만 간추려 표현하는 것
*   객체: 속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료 구조

    ```jsx
    const person = {
    	name: "Lee",
    	address: "Incheon"
    }
    ```
*   객체지향 프로그래밍: 객체의 집합으로 프로그래밍을 표현하려는 패러다임

    * 프로퍼티: 객체의 상태를 나타내는 데이터
    * 메서드: 객체의 상태 데이터를 조작할 수 있는 동작

    ```jsx
    const circle = {
    	radius: 5, // 반지름
    	getDiameter() {
    		return 2 * this.radius;
    	}
    }
    ```

    * 객체는 독립적으로 볼 수도 있지만 다른 객체와 관계성을 가질 수도 있다
    * 객체와 메시지를 주고받거나 데이터를 처리할 수도 있다
    * 다른 객체의 프로퍼티나 메서드를 상속받아 사용할 수도 있다

### 상속과 프로토타입

✔️ 상속: 어떤 객체의 프로퍼티나 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것

* 기존 코드를 재사용하여 중복 제거하기 위해 사용할 수 있다
*   생성자 함수로 인스턴스를 사용할 때 동일한 내용을 가지는 메소드가 중복 생성되는 것은 메모리를 낭비하고 퍼포먼스에 악영향을 끼칠 수 있다

    → **프로토타입 기반 상속**을 통해 중복을 제거할 수 있음

    ```jsx
    function Square(length) {
    	this.length = length;
    }

    Square.prototype.getArea = function() {
    	return this.length * this.length;
    }

    const square1 = new Square(3);
    const square2 = new Square(5);

    console.log(square1.getArea === square2.getArea) // true
    ```

    * `Square.prototype.getArea`
      * prototype에 메서드를 추가하면 Square 생성자 함수로 만들어진 모든 인스턴스가 메서드를 사용할 수 있다
      * 프로토타입은 Square 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다
      * Square 생성자 함수가 생성하는 모든 인스턴스는 하나의 `getArea` 메서드를 공유한다

    → 자신의 상태를 나타내는 `length` 프로퍼티만 개별적으로 소유하고 내용이 동일한 메소드는 상속으로 공유하여 사용하는 형식

    🌟 **코드의 재사용 관점에서 유리함**

### 프로토타입 객체

✔️ 프로토타입 객체(또는 줄여서 프로토타입): 어떤 객체의 상위 객체 역할을 하는 객체로서 객체 간 상속을 구현하기 위해 사용됨

* 공유 프로퍼티/메서드를 제공
* 하위(자식) 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 사용 가능
* 모든 객체는 `[[Prototype]]` 이라는 내부 슬롯을 가짐
  * 내부 슬롯의 값은 대부분 프로토타입의 참조
  * null인 경우도 있는데 그 객체는 프로토타입이 없음
  * 객체 생성 방식에 따라 프로토타입이 결정되고 `[[Prototype]]`에 저장됨
    * 객체 리터럴의 프로토타입: Object.prototype
    * 생성자 함수로 만든 객체의 프로토타입: 생성자 함수의 prototype에 바인딩되어 있는 객체
*   객체와 프로토타입과 생성자 함수는 연결되어 있음

    ![193441027-50ca26c8-f44c-4073-8da1-183758695daf.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/84ad46d5-32ec-4902-aa6d-09f1c52af182/53f590aa-275d-4483-b8da-5763443fe6b0/193441027-50ca26c8-f44c-4073-8da1-183758695daf.png)

#### `__proto__` 접근자 프로퍼티

`__proto__` 접근자 프로퍼티를 통해 `[[Prototype]]` 내부 슬롯에 간접적으로 접근할 수 있다

![스크린샷 2023-09-14 16.58.14.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/84ad46d5-32ec-4902-aa6d-09f1c52af182/93ab8610-2497-417a-bd12-2c1c6d7451ee/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA\_2023-09-14\_16.58.14.png)

* 접근자 프로퍼티는 `[[Get]]`, `[[Set]]` 프로퍼티 어트리뷰터로 구성된 프로퍼티
* `__proto__` 로 프로토타입에 접근했을 때는 내부적으로 `[[Get]]`이 호출된 것이고,
* `__proto__` 접근자 프로퍼티로 새로운 프로토타입을 할당하면 내부적으로 `[[Set]]` 이 호출되는 것

`**__proto__` 를 통해 프로토타입에 접근하는 이유\*\*

* 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서
*   프로토타입 체인은 단방향 링크드 리스트로 구현되어야 한다

    ![download.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/84ad46d5-32ec-4902-aa6d-09f1c52af182/1c71e352-52d8-42eb-954c-0c18e4f2178b/download.png)
*   이런 식의 순환 참조하는 비정상적인 프로토타입 체인이 만들어지면 프로토타입 체인의 종점이 존재하지 않기 때문에 프로토타입 체인에서 프로퍼티를 검색할 때 무한 루프에 빠지게 됨

    → 체크 없이 프로토타입을 교체할 수 없도록 `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하고 교체하도록 구현되어 있다
* `__proto__` 접근자 프로퍼티를 코드 내에서 사용하는 것은 권장되지 않음
  * 모든 객체가 `__proto__` 접근자 프로퍼티를 사용할 수 있는 것은 아니기 때문
  * `__proto__` 대신 `Object.getPrototypeOf` 메서드나 `Object.setPrototypeOf` 메서드를 사용하는 것을 권장함

#### 함수 객체의 prototype 프로퍼티

✔️ 함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성할 객체(인스턴스)의 프로토타입을 가리킴

* non-constructor인 화살표 함수와 ES6 메서드 축약 표현으로 정의한 메서드는 prototype 프로퍼티를 소유하지 않음
  *   메서드 축약 표현

      ```jsx
      const obj = {
        name: 'Lee',
      	// ES5 메소드 표현
        sayHi: function() {
          console.log('Hi! ' + this.name);
        }
        // 메소드 축약 표현
        sayHello() {
          console.log('Hello! ' + this.name);
        }
      };
      ```
* 모든 객체가 가지고 있는 `__proto__` 접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 동일한 프로토타입을 가리킴

#### 프로토타입의 constructor 프로퍼티와 생성자 함수

* 모든 프로토타입이 가지는 constructor 프로퍼티는 prototype 프로퍼티로 자신을 참조하고 있는 생성자 함수를 가리킨다
*   생성자 함수가 생성될 때, 즉 함수 객체가 생성될 때 연결된다

    ```jsx
    function Person(name) {
    	this.name = name;
    }

    const me = new Person('Lee');
    console.log(me.constructor === Person) // true;
    ```
* me 객체에는 constructor 프로퍼티가 없지만 me 객체의 프로토타입인 Person.prototype에 constructor 프로퍼티를 상속받아 사용할 수 있다

### 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

*   new 연산자로 생성자 함수를 호출하지 않고 생성하는 리터럴 표기법 방식

    ```jsx
    const obj = {};
    const add = function(a, b) { return a + b; };
    const arr = [1, 2, 3];
    const regexp = /is/ig;
    ```
*   리터럴 표기법으로 생성한 객체의 프로토타입은 constructor 프로퍼티가 가리키는 생성자 함수가 객체를 생성한 생성자 함수가 아닐 수도 있다

    ```jsx
    const obj = {};
    console.log(obj.constructor === Object) // true
    ```

    * 객체 리터럴로 생성한 객체는 Object 생성자 함수로 생성되는 것일까?
      *   Object 생성자 함수에 인수를 전달하지 않거나 Undefined 또는 nul을 인수로 전달하면서 호출하면 내부적으로는 추상 연산 `OrdinaryObjectCreate` 를 호출해 Object.prototype을 프로토타입으로 가지는 빈 객체를 생성함

          ```jsx
          // 인수가 전달되지 않았을 때
          let obj = new Object();
          console.log(obj)
          ```
* 동일한 점: Object 생성자 함수 호출과 객체 리터럴은 추상 연산 `OrdinaryObjectCreate`를 호출하여 빈 객체를 생성하는 점에서 동일하게 평가되지만
*   다른 점: new.target의 확인이나 프로퍼티를 추가하는 처리 등 세부 내용은 다르다

    **→ 객체 리터럴에 의해 생성된 객체는 Object 생성자 함수가 생성한 객체가 아니다**
* Function 생성자 함수로 만든 함수는 렉시컬 스코프를 만들지 않고 전역 함수인 것처럼 스코프를 생성하며 클로저도 만들지 않는다
* 함수 선언문과 함수 표현식을 평가하여 함수 객체를 생성한 것은 Function 생성자 함수가 아니다
*   하지만 constructor 프로퍼티를 확인해 보면?

    ```jsx
    function foo() {}
    console.log(foo.constructor === Function) // true
    ```
* 리터럴 표기법에 의해 생성된 객체도 가상의 생성자 함수를 가진다
* **프로토타입과 생성자 함수는 언제나 쌍으로 존재한다**
*   객체 리터럴이나 함수 리터럴로 생성한 객체, 함수는 Object/Function 생성자 함수로 생성한 것과는 미묘한 차이가 있긴 하지만 객체/함수로서 동일한 특성을 가진다

    **→ 프로토타입의 constructor 프로퍼티를 통해 연결되어 있는 생성자 함수를 리터럴 표기법으로 생성한 객체를 생성한 생성자 함수로 생각해도 괜찮다!**

### 프로토타입의 생성 시점

✔️ 프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다

* 프로토타입과 생성자 함수는 [언제나 쌍으로](https://www.notion.so/Prototype-4c499d6ce0984c61a23fb1bd97753ec0?pvs=21) 존재하기 때문!

생성자 함수의 종류

* 사용자 정의 생성자 함수
* 빌트인 생성자 함수

#### 사용자 정의 생성자 함수와 프로토타입 생성 시점

✔️ 생성자 함수로 호출할 수 있는 함수, 즉 constructor는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 함께 생성됨

* 생성된 프로토타입의 프로토타입은 언제나 Object.prototype

#### 빌트인 생성자 함수와 프로토타입 생성 시점

> Object, String, Number, Function, Array, RegExp, Date, Promise 등

✔️ 빌트인 생성자 함수도 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성됨

* 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성됨
* 객체가 생성되기 이전에 생성자 함수와 프로토타입이 존재

### 객체 생성 방식과 프로토타입의 결정

객체의 생성 방식

* 객체 리터럴
* Object 생성자 함수
* 생성자 함수
* Object.create 메서드
* 클래스(ES6)
