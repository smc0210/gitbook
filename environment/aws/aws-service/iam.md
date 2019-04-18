---
description: 'IAM, Users, Roles'
---

# IAM

## TOC

* [1. IAM](iam.md#1-iam)
* [2. Dashboard](iam.md#2-dashboard)
* [3. Groups](iam.md#3-groups)
* [4. Users](iam.md#4-users)
  * [4-1. Users Permmisions](iam.md#4-1-users-permmisions)
  * [4-2. Users Security credentials](iam.md#4-2-users-security-credentials)
* [5. Roles](iam.md#5-roles)

## 1. IAM

AWS의 모든 리소스에 대한 액세스를 안전하게 제어할 수 있는 서비스로, 크게 사용자 인증 / 권한 /역할등을 설정하여 사용하는 서비스 이다.

일반적으로 기업 혹은 서비스를 시작시 완전한 액세스 권한을 지닌 단일 로그인 자격을 얻게되는데

> 예\) dev@company.com 계정

일반적으로 해당 계정\(이하 root 계정\)은 사용하지 않고 용도별, 사용자별로 User를 생성하거나, Role을 만들어서 사용한다.

보안강화를 위해 패스워드 방식과 액세스키, 멀티팩터인증\(MFA\)등 다양한 방식을 제공하며, 액세스키 를 생성 혹은 사용하지 않는다고 해도 실제로는 AWS 관리콘솔과 SDK, CLI 에서 하는 모든 작업에는 내부적으로 payload 키값을 전달하여 권한을 인증받으며 사용된다.

> MFA는 어떤 기기 혹은 앱을 사용해도 무방하지만 편의상 `google OTP`를 주로 사용한다.
>
> `root`계정과 `iam`사용자 로그인 차이

### img1

`root`계정의 경우 메일주소를 입력하고, `IAM`사용자의 경우 자동할당된 숫자로 된 `id`를 입력하여 로그인한다. \(숫자로 생성되는 아이디를 wisdom으로 alias 설정해놔서 id란에 wisdom 이라고 입력시 로그인된다.\)

### img2

사용자 이름에 `iam`계정의 아이디를 적어주고\(보통은 email이지만 이는 설정값에 따라 다르다.\)

## 2. Dashboard

### img3

`root` 계정이 아닌 `iam`사용자 계정으로 로그인시 필요한 id의 `alias`설정을 Customize 버튼을 눌러서 설정가능 하다.

> 단 단일 사용자별 alias가 아닌 전체 iam 계정 사용자 도무에게 적용된다.

`Security Status`는 `root`계정의 가장 기본적인 5가지 보안조건을 보여주며, 5개 항목은 필수적으로 셋팅해야한다

## 3. Groups

모든 `IAM`은 권한을 다르게 부여할 수 있지만 편의상 비슷한 책임, 비슷한 권한의 `IAM`을 묶는 용도로 사용한다.

각 그룹에는 사용자가 속할 수도 있지만 `Role` 혹은 `App`, `Instance`등의 AWS 리소스가 속할 수도 있다.

### img4

## 4. Users

`User`라는 이름과는 다르게 단순한 사용자 뿐만 아니라\(메일이 할당된\), SDK 전용계정, 혹은 특정 사용목적 \(instance 에서 cloudwach로 로그를 보내는 작업등...\)에 맞게 생성하여 사용가능하다.

> `user` 목록

### img5

> 예

* {id}@company.com : 사용자 계정
* php-sdk : 파일 저장 관련
* web-cli : EC2 인스턴스 내부의 `aws cli` 사용

> 참고\) 상세페이지 최상단에 User ARN 이라는 url이 존재하는데 이는 AWS 리소스간 통신을 하거나 제어를 할때 사용되는 해당 리소스의 고유한 id라고 생각하면 된다. 이를테면 php-sdk 의 `arn : arn:aws:iam::227292009533:user/php-sdk` 은 php-sdk의 `Primary`

### 4-1. Users Permmisions

User 상세페이지의 첫번째 탭\(Permissions\)에서 permission을 할당 할수 있다. AWS 에서 이미 많들어 놓은 권한을 선택하거나 다른 User의 권한을 그대로 가져오거나, Group할당할 수 있다.

> Aws에서 이미 많들어 놓은 권한 예시
>
> * AmazonEC2FullAccess : EC2에 대한 모든 권한
> * AmazonEC2ReadOnlyAccess : EC2 읽기권한
> * AmazonEC2ReportsAccess : EC2 레포트 권한

기본적으로 모든 AWS 리소스간 통신은 JSON으로 통신하고 권한 역시 마찬가지이다. 보다 자세한 권한 \(이를테면 특정 IP allow /deny , S3의 특정 Bucket만 Read/Write 권한 부여\)도 부여가 가능하고 Json 방식으로 직접 작성할 수 도 있다.

> Json 작성양식을 사용자는 당연히 모르기때문에 policy generater 를 제공하며 하기 이미지의 우하단 Add inline policy 버튼을 눌러서 확인할 수 잇다.

### img6

### 4-2. Users Security credentials

User 상세페이지의 세번째 탭\(Permissions\)에서 보안 관련 설정등을 확인할 수 있다.

* Console password : 비밀번호 설정 \(이메일로 생성한 개발자별 user의 경우 당연히 비밀번호가 필요하고, php-sdk등의 user는 어차피 액세스키로 통신하므로 비밀번호가 필요없다.\)
* Assigned MFA device : OTP 인증으로 이중인증을 사용할 경우 활성화 해서 사용

> Access keys 실질적으로 아마존의 모든 서비스상의 통신\(단순히 웹상의 아마존 관리 콘솔에서 메뉴에서 서비스에 접근하는 것 조차도\)은 액세스키를 이용한다.

access key 를 생성하지 않은 user 계정도 실제로 권한이 있는 메뉴에 접근도 가능하고 정상적으로 작동하는걸 볼수 있는데, 이는 AWS 내부적으로 access key와 secret key를 생성하여 통신하기 때문에 가능한 것으로 `php-sdk` 나 `web-cli`처럼 instance에 access key와 secret key를 입력하여 사용하는 경우는 반드시 생성을 해줘야 사용가능하다.

#### Access key의 특징

* User별 최대 2개의 Accesskey를 보유할 수 있고, 언제든 비활성화 활성화 할 수 있다.
* Access key는 public key라고 생각하면되고, secret key라는 private key가 존재한다.
* private key를 확인하려면 Create access key를 눌러서 생성하는 시점에서 단 한번만 csv파일로 다운받을 수 있고 오직 이때만 다운로드 및 확인 가능하다. \(access key와 secret key가 담긴 csv 파일을 분실 했을경우 재생성 말고는 방법이 없다.\)

### img7

## 5. Roles

AWS 뿐만 아니라 정보보안 개론에서 중요한 개념중 하나인 **최소권한의 원칙**을 달성하기 위한 방법중 하나를 제공해주는 서비스로, 특정 용도\(트러블슈팅\)를 수행할 사용자를 지정하여 사용할 수 있다. 이때 `Role`을 부여 받은 사용자는 기존의 자신이 속한 그룹의 권한은 모두 없어지고 주어진 `Role`에 해당하는 권한만 가지게 된다.

`Role` 은 사용자 혹은 AWS 리소스에 **맡긴다**라고 생각하면 되는데, `Role`의 모자아이콘을 연상해서 생각해보면 편하다.

#### `Role`의 대표적 특징들

* 사용자 뿐만 아니라 그룹에도 `Role`을 지정할수 있고, 특정 리소스\(특정서버, 특정 RDS, 특정 서비스\)에도 지정해줄 수 있다.
* 별도로 `Role`을 detach 하지 않은 한 영구적으로 지속된다.

#### Worst case

소스상에 액세스키를 하드코딩하여 입력하지 않고 리소스에 역할을 부여해서 사용하는 방식이 가장 좋은 방법으로, 예를들면 민다의 파일 업로드의 경우가 액세스키를 설정파일에서 하드코딩으로 입력해놓은 상태이고, 보다 안전하고 권장되는 방법은 운영중인 서버들에게 `php-sdk` user가 가지고 있는 권한을 그대로 가지고 있는 `Role`을 생성하여 서버들에게 직접 할당하는 방법이다.

#### Best case

현재 운영-스테이징-개발 서버에 부여되었는 `Role` 인 `tocloudwatchlogs`의 경우가 위에서 언급한 가장 안전하고 권장되는 방법중 하나이다.

```javascript
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",            // 허용정책
            "Action": [
                "logs:CreateLogStream",        // logStream 생성권한
                "logs:DescribeLogStreams",
                "logs:PutLogEvents"        // log쓰기 권한
            ],
            "Resource": "arn:aws:logs:*:*:*"// 모든 log
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",    
            "Action": "s3:*",            // 모든 S3 액션에 대해 (CRUD)
            "Resource": [
                "arn:aws:s3:::wisdom-ec2-log/*",    // wisdom-ec2-log 버킷과
                "arn:aws:s3:::wisdomelblog/*"    // wisdomelblog 버킷의 모든 파일에 대한
            ]
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": "logs:CreateLogGroup",
            "Resource": "arn:aws:logs:*:*:*"
        }
    ]
}
```

