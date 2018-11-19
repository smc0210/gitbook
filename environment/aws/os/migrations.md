---
description: 소스코드 및 DB 데이터 AWS 환경으로 이전
---

# Migrations

## 1. 소스이관

`git hub`저장소에 올라가 있는 소스를 ec2 웹서버로 이관한다.

### 1-1. EC2 에서 git 설정

[CentOS 셋팅](https://docs.devvisdom.com/environment/aws/os/centos#5)문서의 `git` 설치 항목을 참고하여 ec2 웹서버에 `1.7.4` 버전 이상의 `git`이 설치되어 있다는 가정하에 진행한다.

### 1-2. deploy key 설정

편의상 `root` 계정으로 `~/.ssh`에 각 계정별(개발자) `ssh`키를 생성한 후 `Git` 원격저장소(`Github`, `CodeCommit`등) 에 등록해준다.

`ssh`키를 생성할 경로는 `home/minda/.ssh` 가 아닌 `root`의 `~/.ssh` 루트로 설정해야 `ssh`등록이 가능하다.

```bash
#root 계정으로 전환
sudo -i
#각자의 github 계정정보로 ssh-key 생성
ssh-keygen -t rsa -C "username@email.com"
```

위와 같이 `ssh`키를 생성할경우 저장할 경로를 묻는데, 편의상 각 사용자의 계정을 따서 `ssh`키를 만든다.
> 예) wisdom_rsa

###### img1

위와 같이 뜨면 `/root/.ssh/{username}_rsa`형식으로 지정해주고 키를 생성한다 **(기존 파일에 덮어씌어지지 않도록 경로지정 및 파일명 지정 필수)**

이후 생성된 파일중 공개키(`username_rsa.pub`)를 복사하여

```bash
cat username_rsa.pub
```

###### img2

github 저장소의 `Setting` -> `Deploy keys` 에 등록해준다.

###### img3

### 1-3. Git 저장소에서 소스 가져오기

`Document` 경로로 이동한 후 원격 저장소의 소스를 `clone`한다.
> 기존에 테스트 용도로 생성되어 있는 `www`경로가 존재할 경우 삭제한 후
> `git`저장소 `clone`시 폴더명을 `www`로 명시하여 진행한다.

```bash
cd /home/minda
rm -rf www #www 폴더가 존재할 경우만
git clone git@github.com:theminda/minda-web.git www
```

기본적으로 `master` 브랜치를 가져오지만 테스트 과정 상에서 별도의 `branch`로 작업을 할 경우는 `remote branch`를 갱신해준다.

```bash
git remote update
```

원하는 `branch`로 `checkout`옵션을 `-t`로 하여 로컬 작업트리를 생성함과 동시에 리모트 작업트리를 변경해준 후
소스를 다시 가져온다

```bash
git checkout -t origin/develop
git pull
```

## 2. DB 데이터 이관

기본적인 사항은 AWS 공식 메뉴얼을 참조
[MariaDB 인스턴스로 데이터 가져오기 AWS 메뉴얼](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/MariaDB.Procedural.Importing.html)


진행순서는 다음과 같다.
1. 온프레미스환경 DB에서 dump
2. dump된 파일의 용량에 따라 압축
3. 파일을 로컬로 다운로드 -> EC2로 업로드 (SFTP 사용)
4. RDS 인스턴스에서 이관

### 2-1. 기존 DB에서 RDS로 데이터 이관

DB 덤프 (cli에서 하던 workbench 같은 툴로 하던 아무거나)

```bash
mysqldump -u db_user -p --databases db_name --single-transaction --compress --order-by-primary > origin_dump.sql
```

용량에 따라서 압축
```bash
tar -zcvf origin_dump.tar.gz origin_dump.sql
```

`filezilla` 같은 툴을 통해 `SFTP`로 EC2에 접속하여 덤프파일을 업로드 해준다 이때 S3에서 가져오는 방법등 다양한 방법이 있으나 일단은 테스트 용이므로 직접 업로드

EC2로 업로드 되면 압축해제

```bash
tar -zxvf origin_dump.tar.gz origin_dump.sql
```
EC2에서 RDS mysql 접속

```bash
mysql -h <rds.endpoint> -P 3306 -u <db_master_user> -p
```

> rds의 endpoint는 AWS manage console의 RDS -> instance -> 인스턴스 set detail 로 접근 후
> 스크롤을 조금만 내리면 Connect 부분에 Endpoint가 있다

이관 시작
```bash
source path/origin_dump.sql
```

RDS와 커넥트가 안될경우 하기 관련 패키지 설치여부를 점검해본다
```bash
mysql55-devel
mysql-devel
```

### 2-2. RDS 옵션

RDS 커넥션을 확인해본 후 정상적으로 된다면 `character`관련 설정을 확인해본다

```sql
show variables like 'c%'
```

기존 온프레미스 환경에서 설치한 `Databse`와 달리 RDS는 비관리형 데이터베이스 이므로 언어셋 설정등이 조금 다르다

`/etc/my.cnf`파일등에서 설정하는것이 아닌 `Aws Management Console` 에서 설정을 해준다.

###### img4

상기 이미지를 참고하여 현재 RDS에서 사용하고 있는 `Parameter groups`를 선택한다.

> 현재 사용하고 있는 `Parameter groups`는 `RDS instance`의 상세 페이지에서도 접근 가능하다.


###### img5

상단 검색창에서 옵션값 필터링이 가능하며 캐릭터셋 관련 설정들을 맞게 바꿔준 후 `Save Changes`를 해준다.

그 후 다시 `instances`메뉴로 가서 상태값이 `available`로 변경되길 기다린 후 확인해본다

###### img6

> `filesystem`등은 확인 필요..

`Parameter Grouups`를 변경해도 `character_set_database`와 `collation_database`는 변경되지 않으므로

해당 내용은 RDS 쿼리로 해결한다.


```sql
ALTER DATABASE v2 CHARACTER SET = ‘utf8’ COLLATE = ‘utf8_general_ci’;
```

잘 적용되었는지 `workbench`등의 툴로 확인해본다

###### img7

### 2-3. 기존 DB에서 function 이관

기존 `function`을 새로운 `RDS`에서 쿼리문으로 생성하려고 하면 `DECLARE`에서 문법 오류가 난다.
RDS 서버에 접속하여 하기 변수의 값을 확인해 본다.

```sql
show global variables like 'log_bin_trust_function_creators'
```

###### img8

상기 이미지와 같이 `OFF`로 되어있다면 `Aws Managemnet Console`의 `Parameter Groups`에서 해당 설정값을 `1`로 변경후 `Save Changes`를 눌러 적용시킨다.


이때 주의할 점은 함수 생성 시작과 끝의 쿼리문에 반드시 `DELIMITER`를 붙여줘야 에러가 나지 않는다


```sql
DELIMITER $$
CREATE FUNCTION ....
	BEGIN ...
		(생략)
	RETURN ...
END$$
DELIMITER ;
```