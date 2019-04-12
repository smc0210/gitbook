# Git Snippet

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

