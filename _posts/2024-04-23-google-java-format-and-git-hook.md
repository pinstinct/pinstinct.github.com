---
layout: post
title: 프로젝트에 google java format & git hook 적용하기
categories: experience
tags: [java, spring, project]
---

프로젝트에 컨벤션이 있으나, 개발자 자율에 맡겨진 상황이다. 이런 경우 공동으로 작업 시, 공백만으로 diff가 발생할 수 있다. 이렇게 되면 개발 내용을 파악하기 어려워진다. 그래서 자동으로 formatting 해주는 도구를 결합하고, 강제로 적용해 코드를 통일할 수 있는 방법을 찾아봤다. 

프로젝트에 자동 formatting을 강제하는 가장 큰 이유는 pull request를 확인할 때, 실제 작업한 내용만 보고 싶기 때문이다. gradle 혹은 IDE에 도구에 결합하는 방법은 개발자가 깜빡해 적용하지 않으면 깨지게 된다. 강제성이 없기 때문이다.

때문에 commit 전에 강제로 변경하는 툴을 찾아봤다. 그런데 자바&스프링 환경에서는 gradle 혹은 IDE로 적용하는 방법이 주류를 이루고 있었다. 하지만 google java format이라는 것을 찾게 됐고, 이것을 pre-commit에 적용한 방법을 기록해보려 한다.

### google java format

우선 자바의 많은 컨벤션 중 google 컨벤션을 적용하기로 하고, 찾아보니 구글에서 만든 프로그램이 있었다. 자바 소스 코드를 [Google Java Style](https://google.github.io/styleguide/javaguide.html)에 맞게 formatting 해주는 프로그램이다.

fomatter를 다운 받아, 아래의 명령을 실행하면 파일들을 formatting 해준다.
```shell
java -jar /path/to/google-java-format-${GJF_VERSION?}-all-deps.jar <options> [files...]
```

이 프로그램을 사용하면 git hook에 적용하기 수월해지므로 선택했다.

#### options

`--help` 옵션을 통해 실행해보면, 아래와 같은 옵션들이 존재한다. 이 중에서 필요한 옵션만 추려봤다. 
- `--replace`: (필수) formatting 변경 내용을 파일에 그대로 적용한다.
- `--set-exit-if-changed`: (필수) formatting 변경 내용이 있다면 exit code 1을 반환한다.
- `--aosp`: (옵션) 4-space 들여쓰기를 하고 싶다면 적용한다.

```shell
  -i, -r, -replace, --replace
    Send formatted output back to files, not stdout.
  --aosp, -aosp, -a
    Use AOSP style instead of Google Style (4-space indentation).
  --fix-imports-only
    Fix import order and remove any unused imports, but do no other formatting.
  --skip-sorting-imports
    Do not fix the import order. Unused imports will still be removed.
  --skip-removing-unused-imports
    Do not remove unused imports. Imports will still be sorted.
  --skip-reflowing-long-strings
    Do not reflow string literals that exceed the column limit.
  --skip-javadoc-formatting
    Do not reformat javadoc.
  --dry-run, -n
    Prints the paths of the files whose contents would change if the formatter were run normally.
  --set-exit-if-changed
    Return exit code 1 if there are any formatting changes.
  --lines, -lines, --line, -line
    Line range(s) to format, like 5:10 (1-based; default is all).
  --offset, -offset
    Character offset to format (0-based; default is all).
  --length, -length
    Character length to format.
```

> [google/google-java-format](https://github.com/google/google-java-format)

### git hook

깃에서는 어떤 이벤트가 생겼을 때, 자동으로 스크립트를 실행할 수 있는 훅을 제공한다. 훅을 적용하기 위해서 `.git/hooks` 디렉토리에 적용하려는 훅 이름의 파일을 저장하면 된다.

commit 전에 자동으로 fomatting하고 싶으므로, `pre-commit` 훅을 사용한다. `pre-commit` 파일을 하나 생성 후 아래의 내용을 저장한다.

```sh
#!/bin/sh
#
# An example hook script to verify what is about to be committed.
# Called by "git commit" with no arguments.  The hook should
# exit with non-zero status after issuing an appropriate message if
# it wants to stop the commit.
#
# To enable this hook, rename this file to "pre-commit".

# .git 디렉토리 기준으로 위치를 탐색 
# 다음과 같이 설정하면 root 디렉토리의 상위 디렉토리에 jar 파일이 존재
GJF_JAR="../google-java-format-1.22.0-all-deps.jar"

# Format Java files using google-java-format
# 자바 파일을 google java format 프로그램을 이용해 formatting
java -jar $GJF_JAR --replace --set-exit-if-changed --aosp $(git diff --cached --name-only | grep '\.java$')

# Check if the formatting changed any files
# exit code 1을 반환하는 경우, commit에 실패하고 안내 메시지를 출력
if [ $? -eq 1 ]; then
    echo "Commit failed: Code formatting changes detected. Please run 'git commit' again after formatting your code."
    exit 1
fi

# If no errors are found, allow the commit
exit 0
```

만약, 훅을 생략하고 싶다면 `git commit --no-verify` 옵션을 추가해 커밋한다.

> [8.3 Git맞춤 - Git Hooks](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-Git-Hooks)
