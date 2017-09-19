---
layout: post
title: Frontend package manager
categories: patentstart
tags: [python, django, project]
---

## Frontend package manager
> [NPM vs. Bower vs. Browserify vs. Gulp vs. Grunt vs. Webpack](https://stackoverflow.com/questions/35062852/npm-vs-bower-vs-browserify-vs-gulp-vs-grunt-vs-webpack)

django 프로젝트에서 정적파일 관리만 필요하기 때문에 **bower**를 선택했다.

```shell
$ brew install bower

$ bower init
# ...
? set currently installed components as dependencies? Yes
? would you like to mark this package as private which prevents it from being accidentally published to the registry? No
cidentally published to the registry? No

$ bower install jquery --save
$ bower install materialize --save
```
