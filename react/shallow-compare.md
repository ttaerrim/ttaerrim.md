# 리액트에서의 동등 비교

objectIs.js

```javascript
/**
 * inlined Object.is polyfill to avoid requiring consumers ship their own
 * https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is
 */
 
// Object.is의 폴리필
function is(x: any, y: any) {
  return (
    (x === y && (x !== 0 || 1 / x === 1 / y)) || (x !== x && y !== y) // eslint-disable-line no-self-compare
  );
}

const objectIs: (x: any, y: any) => boolean =
  // $FlowFixMe[method-unbinding]
  typeof Object.is === 'function' ? Object.is : is;

export default objectIs;
```

shallowEqual.js

```javascript
import is from './objectIs';
import hasOwnProperty from './hasOwnProperty';

/**
 * Performs equality by iterating through keys on an object and returning false
 * when any key has values which are not strictly equal between the arguments.
 * Returns true when the values of all keys are strictly equal.
 */
function shallowEqual(objA: mixed, objB: mixed): boolean {
  if (is(objA, objB)) {
    return true;
  }

  if (
    typeof objA !== 'object' ||
    objA === null ||
    typeof objB !== 'object' ||
    objB === null
  ) {
    return false;
  }

  const keysA = Object.keys(objA);
  const keysB = Object.keys(objB);

  // A와 B의 크기가 다르다면 false
  if (keysA.length !== keysB.length) {
    return false;
  }

  // A의 키가 B에 있는지 확인, 같은 키가 같은 값을 가지는지 확인
  for (let i = 0; i < keysA.length; i++) {
    const currentKey = keysA[i];
    if (
      !hasOwnProperty.call(objB, currentKey) ||
      !is(objA[currentKey], objB[currentKey])
    ) {
      return false;
    }
  }

  return true;
}

export default shallowEqual;
```

`shallowEqual`은 객체 간 얕은 비교(첫 번째 깊이까지)를 수행하지만, 두 번째 깊이까지는 비교할 수 없다.

1 depth의 얕은 비교만 수행하는 이유는 다음과 같다. 리액트에서는 props를 기준으로 리렌더링을 수행하기 때문에 일반적으로는 얕은 비교로 충분하다. 그러나 props에 객체를 넘겨 준다면 리액트 렌더링이 예상치 못하게 작동하는 경우가 있다.&#x20;

{% code fullWidth="false" %}
```jsx
import React from "react";
import { useState, memo, useEffect } from "react";

const Component = memo((props) => {
  useEffect(() => {
    console.log("Component has been rendered!");
  });

  return <h1>{props.counter}</h1>;
});

const DeeperComponent = memo((props) => {
  useEffect(() => {
    console.log("DeeperComponent has been rendered!");
  });

  return <h1>{props.counter.counter}</h1>;
});
export default function App() {
  const [, setCounter] = useState(100);

  function handleClick() {
    setCounter((prev) => prev + 1);
  }

  return (
    <div>
      <Component counter={100} />
      <DeeperComponent counter={{ counter: 100 }} />
      <button onClick={handleClick}>+</button>
    </div>
  );
}
```
{% endcode %}

이 코드에서 DeepComponent 함수는 shallowEqual 함수가 2 depth까지 비교하지 못하기 때문에 memo가 작동하지 않는 모습을 볼 수 있다.
