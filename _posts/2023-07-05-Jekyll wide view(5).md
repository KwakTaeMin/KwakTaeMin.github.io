---
layout : single
title : "Jekyll wide view (5)"
categories:
  - jekyll
tags:
  - jekyll
  - blog
  - wideview
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-05T4:00:00Z
---
### Wide View로 만들기

너무 답답한 나머지 넓게 넓게 보고 싶었다.<br>
scss 파일을 뒤적 거리다 보니 해당 파일에서 x-large size px 고정 값이 들어있어 넓은 모니터로 채워지는 사이즈로 변경하였다<br>
`_sass/minimal-mistakes/_variables.scss` 해당 경로에서 <br>
`$x-large: 2000px !default;` 해당 부분만 사이즈를 2000px로 변경하였더니 넓어져서 보기 좋아졌다 <br>
다른 테마는 각각 scss 파일에서 찾아봐야될 것 같다

~~~scss
$small: 600px !default;
$medium: 768px !default;
$medium-wide: 900px !default;
$large: 1024px !default;
$x-large: 2000px !default;
$max-width: $x-large !default;
~~~