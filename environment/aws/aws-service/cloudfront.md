---
description: 'CloudFront, S3'
---

# CloudFront

## TOC
- [1. CloudFront](#1-cloudfront)
- [2. CloudFront Distribution](#2-cloudfront-distribution)
- [3. CloudFront-S3](#3-cloudfront-s3)
- [4. Invalidation](#4-invalidation)
---	

## 1. CloudFront

AWS에서 지원하는 CND 서비스로 주로 전송속도 향상과 비용절감을 위해 사용한다.

EC2나 S3버킷은 동작되는 장소가 리전에 한정되지만 `CloudFront`의 엣지 로케이션(캐쉬 서버)는
현재 시점(2018.05) 기준으로 전세계에 총 **105개**가 존재하며 한국에만 **4개**의 엣지 로케이션이 있다.

사용자가 맨 처음 CloudFront 엣지 로케이션에 접속했을 때, 원하는 파일이 있으면 엣지 로케이션에서 바로 파일을 받게 된다. 
만약 파일이 없으면 에지 로케이션이 원본 파일이 있는 오리진(원본파일서버)에 접속하여 파일을 가져온 뒤 사용자에게 전달한다.

### 다른 CND 서비스와 다른점

- 동적 컨텐츠 전송(Dynamic Content Delivery)를 지원
    -  URL 규칙에 따라서 정적페이지는 캐시, 동적페이지는 EC2 인스턴스로 접속하도록 구성가능
    -  `HTTP/GET` 외에도 `HEAD`, `POST`, `PUT`, `DELETE`, `OPTIONS`, `PATCH` 메서드를 지원하고, `POST`, `PUT`, `DELETE`, `OPTIONS`, `PATCH` 메서드는 캐시되지 않고 곧바로 오리진에 전달된다
    -  HTTP 쿠키를 지원
-  동영상 전송을 위한 라이브 스트리밍 프로토콜을 지원한다. (`Microsoft Smooth Streaming`, `Adobe RTMP Streaming` 등...)

## 2. CloudFront Distribution

CloudFront 배포(Distribution)는 가장 기본적인 단위로 S3의 버킷과 같다고 생각하면 되고,
배포 하나당 독립적인 도메인을 가진다.

### CloudFront가 지원하는 오리진

- S3
- EC2
- ELB
- AWS 이외의 모든 웹서버


## 3. CloudFront-S3

### 배포 생성

### img1

- Web : 일반적인 웹 서버 방식 
- RTMP : 동영상 실시간 스트리밍 프로토콜

`Web`을 선택하면 CloudFront 설정 창이 나온다.


### Origin Option

### img2

- `Origin Domain Name` : 오리진 도메인 이름으로, 클릭하면 사용할 수 있는 목록이 표시된다.
- `Origin Path` : 선택한 오리진의 하위 경로를 지정해줄수 있다 (선택사항)
- `Origin ID` : 오리진을 구분하는 ID으로 `Origin Domain Name`설정시 자동으로 설정됨
- `Restrict Bucket Access` : `S3 버킷`에 `CloudFront`만 접근할 수 있도록 설정하는 옵션으로 S3와 연동시 **Yes**를 선택한다.
- `Origin Access Identity` : 오리진에 접근할 식별자로 생성하거나 기존에 생성되어있는 식별자를 선택한다.
- `Grant Read Permissions on Bucket` : `CloudFront`가 `S3`에서 파일을 읽을 수 있는 권한을 `Bucket Policy`에 설정해준다. **Yes**선택시 다른 모든 접속은 제한되고 `CloudFront`에서만 접근가능하다. 마찬가지로 S3와 연동할경우 **Yes**를 선택해준다.
- `Origin Custom Headers` : 사용자 설정 헤더 (선택사항)


### Default Cache Behavior Settings

### img3

- `Path Pattern` : 파일을 가져올 규칙으로 여기서는 수정할수 없고 배포 생성뒤에 추가가능하다.
- `Viewer Protocol Policy` : CloudFont 로 보여질 프로토콜 정책
- `Allowed HTTP Methods` : 허용하는 메소드 종류로 일반적으로 파일을 읽기만 할때는 `GET`,`HEAD` 만으로 충분하다. (동적 콘텐츠 전송시에만 모든 메소드 사용으로 선택)
- `Cache Based on Selected Request Headers` : 헤더 값에 따라 객체를 캐시할지 여부 (None선택)
- `Object Caching` : 파일의 캐시 유지시간 설정
    - `Use Origin Cache Headers` : 오리진 HTTP 헤더의 캐시설정(Cache-Control)을 따르고, 만약 캐시 설정이 없으면 24시간으로 설정된다.
    - `Customize` : 사용자 설정 캐쉬시간으로 mininum, maximum, default TTL을 지정할수 있다.
- `Forward Cookies` : 오리진 쿠키 전달여부로 **None**으로 해야 캐시 성능이 향상된다.
- `Query String Forwarding and Caching` : CloudFront에서 오리진으로 쿼리를 전달하고, 쿼리에 따라 파일을 구분하고 싶을때 설정. 마찬가지로 **None**으로 해야 캐시 성능이 향상된다.

나머지 설정은 전부 **No**로 설정한다.


### Distribution Settings
- `Price Class` : 엣지 로케이션 사용범위에 따른 요금 수전
    - `Use Only US and Europe`: 미국과 유럽의 에지 로케이션만 사용
    - `Use Only US, Europe and Asia`: 미국과 유럽, 아시아의 에지 로케이션만 사용
    - `Use All Edge Locations`: 모든 에지 로케이션을 사용 (가장 비쌈)
- `Alternate Domain Names` : `Route53`에서 도메인 연결시 이 부분을 설정해야하고 도메인이 여러개일 경우 줄바꿈으로 구분하고 최대 10개까지 설정할 수 있다. (예 : image.theminda.com)
- `SSL Certificate`: HTTPS 프로토콜을 사용하기 위한 인증서 설정으로, 별도의 구매한 SSL 인증서가 없다면 기본값 그대로 사용한다.
- `Default Root Object` : 배포 도메인 최상위URL 접속시 기본적으로 보여질 파일 설정가능 (예: index.html)
- `logging` : CloudFront 접속로그 설정으로 **off**로 설정한다.
- `Distribution State` : 배포 생성후 활성/비활성 상태 설정값

배포가 완료되기까지 보통 20분 내외의 시간이 소요되며 모두 배포가 완료되면 리스트 창의 `Status`란이 `Deployed`로 변경된다.

### img4

### 배포 및 연동 완료 후 cache 확인하기

### img5

연동된 파일에 접근하여 개발자도구를 열어보면 HTTP 헤더를 통해 확인할 수 있다.

## 4. Invalidation

오리진 HTTP Header의 캐시 설정 및 기본 유지시간인 24시간 이전에 내용을 갱신하고 싶을 경우
무효화(`Invalidation`) 기능을 사용해야 한다.

### img6

### 신청순서

1. 배포 리스트 화면에서 원하는 배포 선택후 상단의 Distribution Settings 버튼 클릭 
2. Invalidations 탭 클릭
3. Create Inbalidation 버튼 클릭
4. 원하는 특정 파일/폴더 경로를 지정해준후 Invalidate 버튼 클릭

무효화 요청은 한번에 최대 **1,000개** 까지 처리가 가능하고 동시에 3개까지 생성 가능하다.
대략 20분 정도의 시간이 소요되며, 이러한 설정때문에 보통은 파일명을 변경하여 업로드 하는 방식으로 사용한다.

