# git 명령어
## 환경 설정
### 전역 설정 정보 조회하기
 * git config - -global - -list
### 전역 설정 정보 삭제시키기
 * git config --global --unset-all user.email 이메일 주소를 등록
 * git config --global --unset-all user.name  사용자명을 등록
### 터미널에 표시되는 메시지에 칼라를 표시
 * git config --global color.ui "auto"
---
## 기본적인 명령어
### 현재 git의 버전을 확인
 * git --version
### 새로운 저장소 초기화하기
 * git init
### 현재까지 작업한 내용 저장하기
 * git commit -m “<메시지>”
### staging에 올리는 작업과 저장을 동시에 하기
 * git commit -a -m "<메시지>"
### 지정한 내용의 로그메시지를 다시사용하여 기존내용을 수정하기
 * git commit -C HEAD -a --amend
### 저장되지 않은 변경사항을 조회하기
 * git status
### 새로운 파일 git에 등록시키기(staging)
 * git add <파일>
### staging영역과 현재 작업트리의 차이점 보여주기
 * git diff
### staging영역과 저장소의 차이점 보기
 * git diff --cached
### 저장소, staging영역, 작업트리의 차이점을 모두보기
 * git diff HEAD
### 기존에 존재하는 파일을 새파일로 이동하기
 * git mv <파일명> <새파일명>
### 저장을 하지 않은 파일의 변경내용을 취소하고 이전 저장상태로 되돌리기
 * git checkout -- <파일명>
---
## 원격저장소
### 저장소 복제하기
 * git clone <저장소 url>
### 원격 저장소에서 합치지 않고 지역 브랜치로 변경 사항 가져오기
 * git fetch <원격 저장소>
### 원격 저장소에서 변경 사항을 가져와 현재 브랜치에 합치기
 * git pull <원격 저장소>
### 새로운 로컬 브랜치를 원격 저장소에 푸싱하기
 * git push <원격 저장소> <지역 브랜치>
### 추가한 원격저장소의 목록을 확인하기
 * git remote
### 새로운 원격 저장소 추가하기
 * git remote add <원격 저장소> <저장소 url>
### 해당 원격 저장소의 정보를 확인
 * git remote show <원격 저장소>
### 원격 저장소를 제거하기
 * git remote rm <원격 저장소>
---
## Brandh와 Tag
### 현재 존재하는 브랜치를 확인하기
 * git branch
### 원격 저장소의 브랜치를 확인하기
 * git branch -r
### 원래의 브랜치명에서 새로운 브랜치명을 만들기
 * git branch <새로운 브랜치명> <원래의 브랜치명>
### 브랜치명의 새로운 브랜치를 만들기
 * git branch <브랜치명>
### 존재하는 브랜치를 새로운브랜치로 변경하기
 * git branch -m <존재하는 브랜치명> <새로운 브랜치명>
### 현재 존재하는 태그 목록 확인
 * git tag
### 브랜치명의 현재 시점에 태그명으로 된 태를 붙이기
 * git tag <태그명> <브랜치명>
### 해당 브랜치나 태그로 작업트리를 변경하기
* git checkout <브랜치명>/<태그명>
### 원래의 브랜치명에서 새로운브랜치명으로 새로운 브랜치를 만들면서 체크아웃을 하기
* git checkout -b <새로운 브랜치명> <원래의 브랜치명>
### 브랜치명의 변경사항을 현재 브랜치에 적용하기
 * git rebase <브랜치명>
### 브랜치명의 브랜치를 현재 브랜치로 합치기
 * git merge <브랜치명>
### 브랜치명의 모든 커밋을 하나의 커밋으로 만들기
 * git merge --squash <브랜치명>
### 커밋명의 특정 커밋만을 선택해서 현재 브랜치에 커밋으로 만들기
 * git cherry-pick <커킷명>
---
## 로그관리
### 커밋로그 보기
 * git log
### 출력할 커밋로그의 갯수를 지정하기
 * git log -<갯수>
### 커밋로그를 출력 형식을 바꿔서 보여주기
 * git log --pretty=oneline
 * git log --pretty=format:"%h %s"
### 커밋로그의 변경된 내용을 같이 보여주기
 * git log -p
### 커밋로그의 브랜치 트리 보기
 * git log --graph
### 해당 커밋명의 로그를 보기
 * git log <커밋명>
### 긴 줄 앞에 커밋명과 커밋한 사람들의 정보보기
 * git blame <파일명>
### 범위를 지정하여 정보 보기
 * git blame -L <지정범위>, <지정범위> <파일명>
### 반복되는 패턴을 찾아 복사하거나 이동된 내용 찾기
 * git blame -M <파일명>
### 기존의 커밋으로 변경한 내용을 취소해서 새로운 커밋을 만들기
 * git revert <커밋명>
### 이전 커밋을 수정하기 위해서 사용
 * git reset <커밋명>
### 대화형모드로 커밋 순서를 변경하거나 합치는 등의 작업하기
 * git rebase -i <커밋 범위>
---
## 서브모듈
### 연관된 하위모듈을 확인하기
 * git submodule
### 새로운 하위모듈을 해당경로에 추가하기
 * git submodule add <저장소 주소> <서브모듈경로>
### 서브모듈을 초기화 하기
 * git submodule init <서브모듈경로>
### 서브모듈의 변경사항을 적용하기
 * git submodule update <서브모듈경로>
---
## 기타
### 해당 브랜치나 태그를 압축파일로 만들기
 * git archive --format=tar --prefix=<폴더명>/<브랜치 혹은 태그> | gzip > <파일명>.tar.gz
 * git archive --format=tar --prefix=<폴더명>/<브랜치 혹은 태그> > <파일명>.zip
### 설정에 merge.tool의 값에 있는 머지툴을 찾아 실행하기
 * git mergetool
### 저장소의 로그를 최적하 하기
 * git gc
### 저장소내에 루트디렉토리를 알려주기
 * git rev-parse --show-toplevel
