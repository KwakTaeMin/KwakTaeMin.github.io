---
layout : single
title : "Jekyll 폰트(font) 변경하기 (9)"
categories:
  - jekyll
tags:
  - jekyll
  - blog
  - 폰트
  - font
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-06T3:00:00Z
---

### Jekyll 폰트(font) 변경하기

- [구글 폰트](https://fonts.google.com/)
- [눈누](https://noonnu.cc/)

![img.png](/assets/images/2307/10-1.png)   
   
눈누를 이용하여 블로그 폰트(font)를 변경하는 방법을 알아보자.   
눈누에 접속하게되면 여러가지 무료 폰트가 제공된다. 원하는 폰트를 고른 후에 하나를 클릭합니다.   
저 같은 경우에는 스위트를 골라 진행해보도록하겠습니다.   

![img_1.png](/assets/images/2307/10-2.png)
스위트를 보면 화면 오른쪽에 *웹 폰트로 사용* 의 내용을 복사합니다.
```scss
@font-face {
    font-family: 'SUITE-Regular';
    src: url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_2304-2@1.0/SUITE-Regular.woff2') format('woff2');
    font-weight: 400;
    font-style: normal;
}
```
위 해당 내용을 복사하여 `_sass/minimal-mistakes.scss` 제일 하단에 붙여 넣습니다.   
여러 @import 파일이 있는데 가장 파일 하단에 넣어주면 아무 문제 없습니다.    
그리고나서 `_sass/minimal-mistakes/_variables.scss` 파일을 열어봅니다.   
```sass
$sans-serif: -apple-system, BlinkMacSystemFont, "Roboto", "Segoe UI",
"Helvetica Neue", "Lucida Grande", Arial, sans-serif !default;
```

해당 부분에 웹 폰트로 추가한 `font-famliy` 이름을 추가해주시면 됩니다.   
제가 추가 설정한 폰트의 `font-famliy` 이름은 `SUITE-Reqular` 입니다.    
자신이 추가한 폰트 이름을 주의깊게 확인하시고 등록하시는 것이 현명합니다.   


```sass
$sans-serif: -apple-system, BlinkMacSystemFont, "SUITE-Regular", "Roboto", "Segoe UI",
"Helvetica Neue", "Lucida Grande", Arial, sans-serif !default;
```

이렇게 해당 파일에 추가한 후에 git에 push후 배포 완료가 되었다면 폰트가 정상적으로 변경되었을 것입니다.

![img.png](/assets/images/2307/10-3.png)


### 참조 
- [테디노트 블로그 내 글 검색기능 추가하기](https://www.youtube.com/watch?v=AONVKTeeaWY&ab_channel=%ED%85%8C%EB%94%94%EB%85%B8%ED%8A%B8TeddyNote)