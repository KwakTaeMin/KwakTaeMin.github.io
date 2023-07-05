---
layout : single
title : "Jekyll Favicon 만들기 (6)"
categories:
  - jekyll
tags:
  - jekyll
  - blog
  - favicon
  - 파비콘
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-05T5:00:00Z
---

### Favicon 만들기

![img.png](/assets/images/2307/08-1.png) <br>

왼쪽 상단에 크롬 탭에 보면 작은 이미지가 있는데 그것을 보고 파비콘(Favicon)이라고 한다.<br>
해당 페이지에 대한 대표 이미지라고도 할 수 있다. 해당 이미지의 파일을 만들어보도록 한다. <br>
[favicon-generator](https://www.favicon-generator.org/) 사이트를 이용했습니다. <br>
해당 페이지에서 이미지를 올려주면 다음과 같이 화면에서 볼 수 있습니다. <br>

![img.png](/assets/images/2307/08-2.png) <br>

Download the generated favicon 링크를 클릭하여 다운을 받고 밑에있는 부분을 복사하여 둡니다. <br>
다운 받은 파일을 압축을 푼 후 `/assets/` 파일 하위에 넣어둡니다. <br>

복사한 파일을 `_includes/head.html` 파일에 붙여 넣은 후 해당 경로를 수정했다. <br>

```html
<link rel="apple-touch-icon" sizes="57x57" href="assets/apple-icon-57x57.png">
<link rel="apple-touch-icon" sizes="60x60" href="assets/apple-icon-60x60.png">
<link rel="apple-touch-icon" sizes="72x72" href="assets/apple-icon-72x72.png">
<link rel="apple-touch-icon" sizes="76x76" href="assets/apple-icon-76x76.png">
<link rel="apple-touch-icon" sizes="114x114" href="assets/apple-icon-114x114.png">
<link rel="apple-touch-icon" sizes="120x120" href="assets/apple-icon-120x120.png">
<link rel="apple-touch-icon" sizes="144x144" href="assets/apple-icon-144x144.png">
<link rel="apple-touch-icon" sizes="152x152" href="assets/apple-icon-152x152.png">
<link rel="apple-touch-icon" sizes="180x180" href="assets/apple-icon-180x180.png">
<link rel="icon" type="image/png" sizes="192x192"  href="assets/android-icon-192x192.png">
<link rel="icon" type="image/png" sizes="32x32" href="assets/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="96x96" href="assets/favicon-96x96.png">
<link rel="icon" type="image/png" sizes="16x16" href="assets/favicon-16x16.png">
<link rel="manifest" href="assets/manifest.json">
<meta name="msapplication-TileColor" content="#ffffff">
<meta name="msapplication-TileImage" content="/ms-icon-144x144.png">
<meta name="theme-color" content="#ffffff">
```

완성되었다 한층 더 이뻐진 것 같아서 블로그에 더 애정이 갑니다. 파비콘 만들기 끝입니다~ <br>

![img.png](/assets/images/2307/08-3.png)

