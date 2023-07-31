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
github에서 아래와 같이 private repository를 만들어 줍니다. 

![img.png](/assets/images/2307/18-1.png#center)

```shell
git submodule add https://github.com/KwakTaeMin/chat-config.git  
```

뒤에 폴더도 지정할 수 있으며 저 같은 경우에는 application.yml을 /chat-config 로 옮긴 후 resource 폴더에있는 application.yml은 .gitignore 에 추가하여
private repository에서만 수정하여 옮겨주는 방식으로 수정하였습니다. 
    
이렇게 해도 git history 상 application.yml 파일을 확인하면 기록에 남아 내용이 무엇이였는지 확인이 가능하다   
그렇기에 특정 파일에 대한 git 기록을 제거하는 방법은 

```shell
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch <path to file>' --prune-empty --tag-name-filter cat -- --all
```

이와같이 명령어를 입력한다. 이렇게 진행하게 되면 다음과 같은 경고 이후에 해당 파일에 대한 모든 기록을 삭제하게 됩니다. 

```shell
WARNING: git-filter-branch has a glut of gotchas generating mangled history
         rewrites.  Hit Ctrl-C before proceeding to abort, then use an
         alternative filtering tool such as 'git filter-repo'
         (https://github.com/newren/git-filter-repo/) instead.  See the
         filter-branch manual page for more details; to squelch this warning,
         set FILTER_BRANCH_SQUELCH_WARNING=1.


Proceeding with filter-branch...

Rewrite 4aea94bc1440e691066ef0f1190f905be54a2dc4 (5/15) (1 seconds passed, remaining 2 predicted)    rm 'src/main/resources/application.yml'
Rewrite 756c431543f9f709417e8461bb0dd357e3a030f0 (5/15) (1 seconds passed, remaining 2 predicted)    rm 'src/main/resources/application.yml'
Rewrite cdeddf980e2ab11c6f737a7db7f6cf5d9953a26a (5/15) (1 seconds passed, remaining 2 predicted)    rm 'src/main/resources/application.yml'
Rewrite c395b952811e632337ad0e090217543321f5f92e (5/15) (1 seconds passed, remaining 2 predicted)    rm 'src/main/resources/application.yml'
Rewrite d024cf649e35f3cf19aeedd9085210a5b6d7cd96 (5/15) (1 seconds passed, remaining 2 predicted)    rm 'src/main/resources/application.yml'
Rewrite 5f0327c01ccde18de93b244d3828697689a1b2e3 (5/15) (1 seconds passed, remaining 2 predicted)    rm 'src/main/resources/application.yml'
Rewrite cb5fbfb6c83870ceccdc717f038d8b12dc7db94e (11/15) (1 seconds passed, remaining 0 predicted)    rm 'src/main/resources/application.yml'
Rewrite 83c88dedbdd8367d505a55ec21b0ba02d970ca21 (11/15) (1 seconds passed, remaining 0 predicted)    rm 'src/main/resources/application.yml'
Rewrite 048f4345534be8c97282093a6a8d1ffbe9f1c98e (11/15) (1 seconds passed, remaining 0 predicted)    rm 'src/main/resources/application.yml'
Rewrite 8688717299d482cbef9675db9e1abc337bb4ae58 (11/15) (1 seconds passed, remaining 0 predicted)    
Ref 'refs/heads/master' was rewritten
Ref 'refs/remotes/origin/master' was rewritten

```

![img.png](/assets/images/2307/18-2.png#center)

intelliJ에서는 이렇게 표시됩니다. (`gittoolbox` plugins이 있어야 보이긴합니다.) submodule이 있어서 따로 git 관리를 할 수 있습니다.   
submodule은 private하게 한후 중요한 정보 민감한 정보를 관리하고 메인 프로젝트엔 코드만 관리 할 수 있도록 할 수 있습니다.

이렇게 public한 github 공간에서 민감한 정보를 관리하고 다루는 방법에 대해 알아보았습니다. 

### 참조
- [Git-submodule로-민감정보-안전하게-관리하기-yd2ngn0g](https://velog.io/@pjh612/Git-submodule%EB%A1%9C-%EB%AF%BC%EA%B0%90%EC%A0%95%EB%B3%B4-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-yd2ngn0g)
- [Git 특정 파일 기록 삭제하기](https://velog.io/@dngur9801/git-%ED%8A%B9%EC%A0%95-%ED%8C%8C%EC%9D%BC-%ED%9E%88%EC%8A%A4%ED%86%A0%EB%A6%AC-%EC%82%AD%EC%A0%9C%ED%95%98%EA%B8%B0)
