---
layout: post
title: "브랜치 작업물 메인브랜치로 머지하는 방법"
author: Minsun
categories: [Development]
tags: [Git]
image:
beforetoc: "메인 브랜치에서 다른 브랜치를 생성하여 작업하다 메인으로 머지하고 싶을 때 사용하는 방법이다."
toc: true
---

1. master브랜치에서 test브랜치를 생성하여 작업하다 master로 merge되길 원하는 상황

```bash
//test 브랜치에서 작업하던 것들을 commit 및 push 마무리하기
git checkout master
git pull origin master
git merge test
//컨플릭트 발생하면 해결
git push origin master
```

2. 브랜치에서 작업하던 것이 완료되어 main branch로 merge되면 사용하지 않는 branch는 삭제하는 것이 좋다.

```bash
//deleting a branch locally
git branch -d <branch name>
//deleting a branch remotely
git push origin --delete <branch name>
```