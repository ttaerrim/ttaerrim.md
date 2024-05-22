# satisfies 연산자

```typescript

type Product = {
  id: number;
  name: string;
  type: "PURCHASE" | "SUBSCRIBE";
}
const updateProduct = (product: Product) => { console.log(product) }
const tempProduct = { id: '1', name:'product-name', type:'PURCHASE'}
updateProduct(tempProduct)
```

다음 코드에서 tempProduct의 id가 number 타입이 아닌 string 타입이기 때문에, updateProduct에 tempProduct를 인자로 넘길 때 에러가 발생한다. Product 타입이 가지는 프로퍼티가 많아질 경우, 타입과 객체를 일일이 비교하는 일은 까다로워질 수 있다.

이럴 때 `satisfies`키워드를 사용하면 `tempProduct`의 프로퍼티 중 어떤 값이 `Product` 타입을 만족시키지 않는지 에러를 표시해 준다.

<div>

<figure><img src="../.gitbook/assets/스크린샷 2024-05-22 오후 6.20.04.png" alt=""><figcaption><p>as-is</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/스크린샷 2024-05-22 오후 6.20.14.png" alt=""><figcaption><p>to-be</p></figcaption></figure>

</div>
