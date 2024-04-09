---
layout: post
title: github의 pull_request_template
categories: experience
tags: [github, project]
---

### Github의 PR 템플릿 기능

프로젝트의 정해진 위치에 `pull_request_template.md` 파일이 있으면, PR 생성 시 자동으로 템플릿이 적용됩니다.

- root 디렉토리에 pull_request_template.md 파일이 있는 경우
- docs/pull_request_template.md 파일이 있는 경우
- .github/pull_request_template.md 파일이 있는 경우
- 여러개의 템플릿을 사용하려면 .github/PULL_REQUEST_TEMPATE 디렉토리에 `*.md` 파일들을 넣는 경우   
  - 쿼리 파라미터를 이용해서 템플릿을 불러오므로 잘 사용하지 않음 

> [Creating a pull request template for your repository](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository)


### PR 템플릿 샘플 

#### 이전 직장에서 사용했던 템플릿 1

```
## 작업 내용 

## 결과물
(이미지 캡쳐)

## 작업 종류
- [ ] 새로운 기능
- [ ] 리팩토링
- [ ] 기능 수정
- [ ] ...

## 배포 전 필요 작업
- [ ] DB Migration
- [ ] ...
```

이미지 캡쳐를 강제해서 불편했습니다. 하지만 한눈에 파악이 쉽고 백엔드가 아닌 다른 분들이 결과물을 보기에는 편했습니다. 그리고 체크리스트가 많아 불편했습니다.


#### 이전 직장에서 사용했던 템플릿 2

```
## 작업 내용 (Content)
- 리뷰어가 중점적으로 봐야 하는 부분을 바로 알 수 있도록 변경된 내용을 나열합니다.
- List up changes so that reviewer can quickly understand the important parts.

## 링크 (Links) - option
- [JIRA 티켓 이름](ex.http://jiraaddress.com/sandworks/SW-450)
- [필요 시: 참고 문서 URL](ex.http://google.com)

## 기타 사항 (Etc)
- PR에 대한 추가 설명이나 작업하면서 고민이 되었던 부분 등을 적어주세요.
- Additional information about this PR or any troubles working on this PR.

## Merge 전 필요 작업 (Checklist before merge)
- [ ] 예) 배포 테스트 사항, 오류 수정 사항 체크 등을 적어주세요.
- [ ] eg) Deploy test, error check ect
```

사용하기 가장 좋았던 템플릿입니다. 개인적으로 작업내용에 AS-IS, TO-BE를 작성해 달라진 점을 명확하게 표시하는 것을 선호하는 편입니다.


#### 좋은 PR 템플릿이란?

우선 PR 자체의 변경사항이 너무 많지 않은게 중요하다고 생각합니다. 양이 많은 PR은 리뷰하기가 쉽지 않기 때문이다. 

또한, 어떤 템플릿이든 고민한 부분이 있어야 함께 논의할 주제가 생기고 리뷰를 하면서 많은 이야기를 나눌 수 있었습니다.
때문에 개인적으로 **고민한 부분 혹은 집중해서 검토할 부분**들이 담긴 PR이 가장 좋았습니다.

아래는 검색하다가 블로그에서 괜찮은 내용이 있어 정리했습니다. PR에 너무 많은 항목이 있으면 PR을 작성하기 부담스럽기 때문에, 중요한 항목으로 구성해 템플릿을 만드는게 좋을 것 같습니다. 

```
- 무슨 이유로 코드를 변경했는지
- 어떤 위험이나 장애가 발견되었는지
- 어떤 부분에 리뷰어가 집중하면 좋을지
- 테스트 계획 또는 완료 사항
```

> [좋은 Pull Request를 만드는 방법과 PR Template 구성](https://2jinishappy.tistory.com/337)
