---
layout: post
title: 파이썬 라이브러리 레시피
categories: book
tags: [book, python]
excerpt: 책을 읽고 정리한 내용입니다.
---

[파이썬 라이브러리 레시피](http://www.yes24.com/Product/Goods/25962731) 책을 읽고 정리한 내용입니다.

## 목차

[제1장 텍스트 처리하기](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/1-텍스트-처리하기)
- 1.1 일반적인 문자열 조작하기 - str, string
- 1.2 정규 표현 다루기 - re
- 1.3 Unicode 데이터베이스에 접근하기 - unicodedata

[제2장 수치 처리하기](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/2-수치-처리하기)
- 2.1 기본적인 수치 계산 - 내장 함수, math
- 2.2 고정소수점형 계산하기 - decimal
- 2.3 의사 난수 다루기 - random
- 2.4 통계 계산 - statistics

[제3장 날짜와 시각 처리하기](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/3-날짜와-시각-처리하기)
- 3.1 날짜와 시간 다루기 - datetime
- 3.2 시각 다루기 - time
- 3.3 datetime의 강력한 확장 모듈 - dateutil
- 3.4 전 세계 표준시간대 정보 다루기 - pytz

[제4장 자료형과 알고리즘](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/4-자료형과-알고리즘)
- 4.1 다양한 컨테너이너형 다루기 - collections
- 4.2 힙 큐 이용하기 - heapq
- 4.3 이진 탐색 알고리즘 이용하기 - bisect
- 4.4 수치 배열을 효율적으로 다루기 - array
- 4.5 약한 참조를 통한 객체 관리 - weakref
- 4.6 열거형으로 상수 정의하기 - enum
- 4.7 데이터를 읽기 쉬운 형식으로 출력하기 - pprint
- 4.8 반복자와 조합하여 처리하기 - itertools

[제5장 범용 OS/런타임 서비스](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/5-범용-OS-런타임-서비스)
- 5.1 운영체제의 기능 이용하기 - os
- 5.2 스트림 다루기 - io
- 5.3 인터프리터 관련 정보를 얻고 조작하기 - sys
- 5.4 명령줄 옵션과 인수 다루기 - argparse

[제6장 파일과 디렉터리 접근하기](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/6-파일과-디렉토리-접근하기)
- 6.1 파일 경로 조작하기 - os.path
- 6.2 직관적으로 파일 경로 조작하기 - pathlib
- 6.3 임시 파일과 디렉터리 만들기 - tempfile
- 6.4 파일 이름 매치와 경로 패턴 풀기 - fnmatch, glob
- 6.5 고급 파일 조작 - shutil

[제7장 데이터 압축과 아카이브](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/7-데이터-압축과-아카이브)
- 7.1 zlib 라이브러리로 데이터 압축하기 - zlib
- 7.2 gzip 압축 파일 다루기 - gzip
- 7.3 bzip2 압축 파일 다루기 - bz2
- 7.4 lzma 압축 파일 다루기 - lzma
- 7.5 zip 파일 다루기 - zipfile
- 7.6 tar 파일 다루기 - tarfile

[제8장 특정 데이터 포맷 다루기](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/8-특정-데이터-포맷-다루기)
- 8.1 CSV 파일 다루기 - csv
- 8.2 INI 파일 다루기 - configparser
- 8.3 YAML 다루기 - PyYAML
- 8.4 JSON 다루기 - json
- 8.5 Excel 다루기 - openpyxl
- 8.6 이미지 다루기 - Pillow

[제9장 인터넷상의 데이터 다루기](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/9-인터넷상의-데이터-다루기)
- 9.1 URL 해석하기 - urllib.parse
- 9.2 URL 열기 - urllib.request
- 9.3 인간친화적인 HTTP 클라이언트 - requests
- 9.4 Base16, Base64 등으로 인코딩 - base64
- 9.5 이메일 데이터 다루기 - email

[제10장 HTML과 XML 다루기](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/10-HTML과-XML-다루기)
- 10.1 XML 해석하기 - xml.etree.ElementTree
- 10.2 XML/HTML을 빠르고 유연하게 해석하기 - lxml
- 10.3 간편한 HTML parser 이용하기 - beautifulsoup4

[제11장 테스트와 디버깅](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/11-테스트와-디버깅)
- 11.1 문서 생성과 온라인 도움말 시스템 - pydoc
- 11.2 대화형 실행 예 테스트하기 - doctest
- 11.3 단위 테스트 프레임워크 이용하기 - unittest
- 11.4 mock을 이용한 단위 테스트 - unittest.mock
- 11.5 대화 모드 디버깅하기 - pdb
- 11.6 코드의 실행 시간을 측정하기 - timeit
- 11.7 더 높은 수준의 unittest 기능 이용하기 - pytest
- 11.8 스택 트레이스 다루기 - traceback
- 11.9 로그 출력하기 - logging

[제12장 암호 관련](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/12-암호-관련)
- 12.1 다양한 암호화 다루기 - PyCrypto
- 12.2 SSH 프로토콜 다루기 - paramiko

[제13장 병렬처리](https://github.com/pinstinct/TIL/tree/master/python/python-library-recipe/13-병렬처리)
- 13.1 복잡한 프로세스를 생성하여 병렬처리하기 - multiprocessing
- 13.2 서브 프로세스 관리하기 - subprocess
