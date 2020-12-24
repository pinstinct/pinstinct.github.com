---
layout: post
title: AWS IAM/S3를 이용한 정적 파일 배포
categories: experience
tags: [python, django, project, aws, deploy]
---

# Deploy static files using AWS S3

## AWS IAM
AWS 루트 사용자가 필요하지 않은 작업에 루트 사용자를 사용하지 않은 것을 권고하고 있다. 그러므로 IAM 사용자를 생성하고, 그룹에 추가한다.
- 루트 사용자 계정을 통해 IAM 콘솔에 접속
- Users > Add user 선택
- Username 입력
- AWS Management Console access 체크 후 password 설정
- Next > Add user to group 선택
- Create group 입력
- 정책 목록에서 AdministratorAccess 체크 후 Create group 선택
- Next > Create User

> [IAM 관리자 및 그룹 만들기](http://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/getting-started_create-admin-group.html)


## AWS CLI

> [AWS Command Line Interface란 무엇입니까?](http://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-welcome.html)

### 설치
Python용 패키지 관리자 `pip`를 통해 설치한다.
```shell
# 프로젝트 폴더에서 진행
$ pip install awscli

# 설치 확인
$ aws --version
aws-cli/1.11.158 Python/3.6.0 Darwin/16.7.0 botocore/1.7.16
```

### Configuration
```shell
$ aws configure
AWS Access Key ID [None]: YOUR_KEY_ID
AWS Secret Access Key [None]: YOUR_ACCESS_KEY
Default region name [None]: ap-northeast-2
Default output format [None]: json
```

## Serving static files
> [Using Amazon S3 to Store your Django Site's Static and Media Files]( https://www.caktusgroup.com/blog/2014/11/10/Using-Amazon-S3-to-store-your-Django-sites-static-and-media-files/)

### boto3 설치
Boto는 파이썬을 위한 AWS SDK이다.

```shell
$ pip install boto3
```

### AWS S3 Bucket 생성
```python
$ python
>>> import boto3
>>> session = boto3.Session(profile_name='default')
>>> client = session.client('s3')
>>> client.create_bucket(Bucket='patentstart', CreateBucketConfiguration={'LocationConstraint': 'ap-northeast-2'})
{'ResponseMetadata': {'...'}}
```

> [Boto3 create_bucket](https://boto3.readthedocs.io/en/latest/reference/services/s3.html#S3.Client.create_bucket)

### AWS S3 Bucket 설정
#### 1. Enable web access
- 생성한 S3 bucket 클릭
- 속성 탭 > 정적 웹 사이트 호스팅
- "이 버킷을 사용하여 웹 사이트를 호스팅합니다." 체크 후 저장

#### 2. CORS
- 생성한 S3 bucket 클릭
- 권한 탭 > CORS 구성
- 다음과 같이 구성(default)되어 있는지 확인

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
<CORSRule>
    <AllowedOrigin>*</AllowedOrigin>
    <AllowedMethod>GET</AllowedMethod>
    <AllowedMethod>HEAD</AllowedMethod>
    <AllowedHeader>Authorization</AllowedHeader>
</CORSRule>
</CORSConfiguration>

```

#### 3. Public permissions
- 생성한 S3 bucket 클릭
- 권한 탭 > 버킷 정책

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::patentstart/*"
        }
    ]
}
```

위와 같이 설정하면, `http://<BUCKETNAME>.s3-website-<REGION>.amazonaws.com/<FILENAME>`로 방문한 모든 사용자들이 접근할 수 있게 된다.

> [웹 사이트 액세스에 필요한 권한](http://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/WebsiteAccessPermissionsReqd.html)

> [액세스 관리 (액세스 정책 옵션)](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/access-control-overview.html#access-control-resources-manage-permissions-basics)

### django-storages 설치

```shell
pip install django-storages
```


```python
# settings.py

# manages static files
# 장고는 파일을 어디에 어떻게 저장할 것 인지 결정을 위임
# 장고의 기본 파일 저장은 DEFAULT_FILE_STORAGE 설정
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'

# manages media files
# collectstatict 실행 시, 저장될 공간을 STATICFILES_STORAGE 설정
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
```

> [DEFAULT_FILE_STORAGE](https://docs.djangoproject.com/en/1.11/topics/files/#file-storage)

> [STATICFILES_STORAGE](https://docs.djangoproject.com/ko/1.11/howto/static-files/deployment/#serving-static-files-from-a-cloud-service-or-cdn)

```python
# default setting
AWS_STORAGE_BUCKET_NAME = 'BUCKET_NAME'
AWS_ACCESS_KEY_ID = 'YOUR_ACCESS_KEY_ID'
AWS_SECRET_ACCESS_KEY = 'YOUR_SECRET_ACCESS_KEY'
AWS_S3_SIGNATURE_VERSION = 's3v4'

# Tell django-storages the domain to use to refer to static files.
AWS_S3_CUSTOM_DOMAIN = '{}.s3.amazonaws.com'.format(AWS_STORAGE_BUCKET_NAME)
```


> [django-storages](https://django-storages.readthedocs.io/en/latest/)


> [버킷 엑세스](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/UsingBucket.html#access-bucket-intro)

> [AWS 리전 및 엔드포인트 - S3](http://docs.aws.amazon.com/ko_kr/general/latest/gr/rande.html#s3_region)

```shell
$ python manage.py collectstatict
$ python manage.py runserver --nostatic
```

### Custom django-storages
```python
# storages.py
from django.conf import settings
from storages.backends.s3boto3 import S3Boto3Storage

class StaticStorage(S3Boto3Storage):
    location = settings.STATICFILES_LOCATION

class MediaStorage(S3Boto3Storage):
    location = settings.MEDIAFILES_LOCATION
```

```python
# settings.py
STATICFILES_LOCATION = 'static'
STATICFILES_STORAGE = 'config.storages.StaticStorage'

MEDIAFILES_LOCATION = 'media'
DEFAULT_FILE_STORAGE = 'config.storages.MediaStorage'
```
