---
layout : single
title : "Github 민감한 정보 SubModule로 관리하기"
categories:
  - git
tags:
  - git
  - github
  - submodule
  - repository
  - private
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-27T1:00:00Z
---

## Github 민감한 정보 SubModule로 관리하기 

![img.png](/assets/images/2307/17-1.png#center)

개인적인 프로젝트를 개발하고 있는데 뜬금 없이 이런 메일이 와있었다. 무슨 내용인가 봤더니 Google OAuth2.0 Client Id, Client Secret을 
github 공간에 너무 자연스럽게 보여주고있었다. 이와 마찬가지로 mysql db정보나 id password 도 노출될 수 있기에 이러한 정보를 관리 할 수 있는 방법이 필요했다. 
사실 `.gitignore` 에다가 간단히 처리하려고 했으나 생각해보니 노트북에서도 사용할 때 필요함으로 commit을 해야만 하는 상황이다. 그럼으로 뭔가 조치를 취해야했다. 

### Private Repository 개설하기 

public github repository에 민감한 정보를 commit push 하지 않고 private repository에 민간한 정보를 두고 private repository를 submodule로 
pull 하여 따로 관리하는 것이다. 그럼 민감한 정보는 보여지지 않고 private 하게 관리할 수 있으니 안전하게 관리 할수 있다.   






### 참조
- [Git-submodule로-민감정보-안전하게-관리하기-yd2ngn0g](https://velog.io/@pjh612/Git-submodule%EB%A1%9C-%EB%AF%BC%EA%B0%90%EC%A0%95%EB%B3%B4-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-yd2ngn0g)
