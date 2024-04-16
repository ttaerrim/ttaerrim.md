---
description: >-
  최근 Next.js를 사용하면서 App Router와 Page Router를 모두 다뤄보게 되었습니다. 두 가지 방식을 간단히 비교해 보고
  그 차이점을 정리합니다.
---

# Next.js의 App Router vs. Page Router

<pre><code><strong>최근 Next.js를 사용하면서 App Router와 Page Router를 모두 다뤄보게 되었습니다.
</strong>
두 가지 방식을 간단히 비교해보고 그 차이점을 알아보려 합니다.

## 프로젝트 생성

### Page Router

```bash
.
├── README.md
├── next-env.d.ts
├── next.config.mjs
├── package-lock.json
├── package.json
├── pages
│   ├── _app.tsx
│   ├── _document.tsx
│   ├── api
│   │   └── hello.ts
│   └── index.tsx
├── public
│   ├── favicon.ico
│   ├── next.svg
│   └── vercel.svg
└── styles
│   └── globals.css
└── tsconfig.json
```

`pages` 디렉토리 하위에 라우팅이 작성되어 있습니다.

글로벌 스타일로 사용되는 css 파일이 `styles` 디렉토리 하위에 작성되어 있고, 초기 프로젝트를 만들 때 사용되는 파비콘과 svg 파일이 `public` 디렉토리 하위에 있습니다.

### App Router

```bash
.
├── README.md
├── app
│   ├── favicon.ico
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx
├── next-env.d.ts
├── next.config.mjs
├── package-lock.json
├── package.json
├── public
│   ├── next.svg
│   └── vercel.svg
└── tsconfig.json
```

`app` 디렉토리 하위에 라우팅이 작성되어 있습니다.

글로벌 스타일로 사용되는 css 파일과 파비콘 또한 `app` 디렉토리 하위에 위치합니다.

## Routing

### Page Router

`pages` 디렉토리 하위에 작성합니다.

- /about이라는 라우팅을 만들고 싶다면 `/pages/about.ts` 파일을 생성합니다. (`/pages/about/index.ts`로 생성해도 동일하게 작동)

- 동적 라우팅은 `/pages/posts/[id].ts`와 같이 작성합니다.

  - 라우팅이 `/posts/1`, `/posts/2`와 같이 만들어집니다.

```bash
└── pages
    ├── _app.tsx
    ├── _document.tsx
    ├── api
    │   └── hello.ts
    ├── about.tsx    // /about
    ├── posts
    │   └── [id].ts  // /posts/1
    └── index.tsx
```

### App Router

`app` 디렉토리 하위에 작성합니다.

- /about이라는 라우팅을 만들고 싶다면 `/app/about/page.js` 파일을 생성합니다. 꼭 `page.js` 파일을 사용해야 라우팅을 사용할 수 있습니다.

- 라우팅마다 레이아웃을 생성할 수 있습니다.

  - `/app/about/layout.js`은 `/about/*`의 레이아웃으로 사용할 수 있습니다.

- `loading.js`, `error.js` 등의 페이지를 사용할 수 있습니다.

- `page.js`에서 `throw new Error`를 사용해 `error.js` 페이지로 넘길 수 있습니다.

- 중첩 라우팅을 사용하면서 괄호를 사용하면 URL 경로에 포함시키지 않을 수도 있습니다.
  - `/app/(home)/about` → /about
  - `/app/(home)/blog` → /blog
  - `/app/(shop)/account` → /account

```bash
└── app
    ├── favicon.ico
    ├── globals.css
    ├── layout.tsx
    ├── page.tsx
    ├── about           // /about
    │   ├── layout.tsx
    │   └── page.tsx
    └── (home)
        └── blog        // /blog
            ├── layout.tsx
            └── page.tsx
```

## Data Fetching

### Page Router

`getServerSideProps`, `getStaticProps`와 같은 메소드를 사용합니다.

```typescript
import Image from 'next/image';
import { GetServerSideProps } from 'next';
import type { Response } from '../types';

export const getServerSideProps = (async () => {
  const res = await fetch('http://localhost:3000/api/data');
  const response: Response = await res.json();
  return { props: { data: response.data } };
}) satisfies GetServerSideProps&#x3C;{ data: Response['data'] }>;

export default function Home({ data }: { data: Response['data'] }) {
  return (
    &#x3C;main>
      {data?.map((item) => (
        &#x3C;div key={item.dataNo}>
          &#x3C;p>{item.dataContent}&#x3C;/p>
          &#x3C;p>{item.dataTitle}&#x3C;/p>
          &#x3C;p>{item.dataNo}&#x3C;/p>
          &#x3C;p>{item.dataLink}&#x3C;/p>
          &#x3C;Image src={item.imageUrl} alt={item.dataTitle} width={50} height={50} />
        &#x3C;/div>
      ))}
    &#x3C;/main>
  );
}
```

위 코드는 `getServerSideProps`를 사용한 예시입니다.

### App Router

App Router에서는 기본적으로 모든 컴포넌트에서의 data fetching 작업이 서버 컴포넌트에서 이루어집니다.

따라서 Page Router처럼 별도의 메소드를 사용할 필요 없이 fetch API나 라이브러리를 사용하여 서버로부터 데이터를 가져오면 됩니다.

Next.js에서 사용되는 fetch는 fetch Web API를 확장하여 캐싱이나 revalidate 등과 같은 기능을 추가로 제공합니다.

```typescript
import Image from 'next/image';
import { Response } from '../types';

async function getData() {
  const res = await fetch('http://localhost:3001/api/data');
  return res.json();
}

export default async function Home() {
  const res: Response = await getData();
  const data = res.data;
  return (
    &#x3C;main className={`flex min-h-screen flex-col items-center justify-between p-24`}>
      {data?.map((item) => (
        &#x3C;div key={item.dataNo}>
          &#x3C;p>{item.dataContent}&#x3C;/p>
          &#x3C;p>{item.dataTitle}&#x3C;/p>
          &#x3C;p>{item.dataNo}&#x3C;/p>
          &#x3C;p>{item.dataLink}&#x3C;/p>
          &#x3C;Image src={item.imageUrl} alt={item.dataTitle} width={50} height={50} />
        &#x3C;/div>
      ))}
    &#x3C;/main>
  );
}
```

App Router에서 클라이언트 사이드에서 작업하고 싶다면 파일 상단에 'use client'를 작성해 줍니다.

```typescript
export default function Home() {
  const [data, setData] = useState&#x3C;Response['data']>([]);

  async function getData() {
    const res = await fetch('http://localhost:3001/api/data');
    const response: Response = await res.json();
    setData(response.data);
  }

  useEffect(() => {
    getData();
  }, []);

  return (
    // 생략
  );
}
```

Page Router도 마찬가지로 클라이언트 사이드에서 데이터를 불러오고 싶다면 useEffect 훅을 사용하면 됩니다.
</code></pre>

