---
layout: post
title: form-data를 전송할 때 발생한 문제 해결
categories: experience
tags: [http, network]
excerpt: Content-Type multipart/form-data를 정의해 요청했을 때, 서버에서 데이터를 받을 수 없었던 문제를 해결합니다.
---

클라이언트가 서버와 통신할 때, 헤더의 `Content-Type`을 포함해 호출합니다. `application/json`으로 보낼땐 문제가 없었는데, `multipart/form-data` 보내니 서버에 데이터가 오지 않았습니다.

문제를 해결하기 위해 [문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)를 찾아봤습니다. `Content-Type: multipart/form-data` 헤더를 보낼 때는 `boundary`를 필수로 붙여 요청해야 했습니다.

### `boundary`는 무엇인가?
`multipart/form-data` 설정은 데이터를 바이너리로 전송합니다. 그러면 서버는 필드마다 데이터의 끝이 어디인지, 다음은 어디서 시작하는지 알기 위해 구분자가 필요합니다. 그것이 바로 `boundary` 값입니다.

### 문제는 왜 생긴것인가?
`Content-Type`을 오버라이드해서 사용하게 되면, `boundary` 값을 명시해줘야 합니다. 때문에, `Content-Type`을 지워주면 브라우저에서 알아서 처리해 요청합니다.

> [refrence](https://muffinman.io/uploading-files-using-fetch-multipart-form-data/)