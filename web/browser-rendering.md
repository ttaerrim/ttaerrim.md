# 브라우저의 렌더링 과정

## 브라우저 렌더링 순서

1. 파싱
2. 렌더 트리 생성
3. layout/reflow
4. repaint

### 파싱

### 렌더 트리 생성

* HTML 문서를 읽으며 DOM 트리를 생성한다.
* CSS 파일을 읽으며 CSSOM 트리를 생성한다.
* DOM 트리와 CSSOM 트리를 연결하며 렌더 트리를 생성한다.



* 여기서 DOM 트리와 렌더 트리는 1:1 매핑되지 않는다.
  * `display: none` 속성이나 `<head>` 태그와 같이 DOM 트리에는 존재하지만 렌더 트리로 만들어질 때는 존재하지 않을 수 있다.

### layout/reflow

### repaint
