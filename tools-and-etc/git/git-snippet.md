# Git Snippet

순서상관없이 그냥 생각 안날때 참고할 커맨드 목록

## 설정

```bash
# 설정된 내용 확인
git config --list

# 설정
git config --global user.name "user"
git config --global user.email "test@email.com"

# 삭제
git config --unset user.name

# 글로벌 설정 삭제
git config --unset --global user.name
```

## 상태 변경

### 원격 저장소 URL 변경하기

`git remote set-url` 명령어를 사용해서 원격 저장소 주소를 변경 가능

```bash
$ git remote -v
     # View existing remotes
origin  https://github.com/user/repo.git (fetch)
origin  https://github.com/user/repo.git (push)

$ git remote set-url origin https://github.com/user/repo2.git

# Change the 'origin' remote's URL
$ git remote -v

# Verify new remote URL
origin  https://github.com/user/repo2.git (fetch)
origin  https://github.com/user/repo2.git (push)
```

### cache 삭제

gitignore 파일 꼬였거나,,어쨌든 꼬였을때

```bash
git rm -r --cached .
```

