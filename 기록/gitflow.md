# GIT FLOW

[참고링크_블로그](https://k39335.tistory.com/82)

[우아한형제_기술블로그](https://woowabros.github.io/experience/2017/10/30/baemin-mobile-git-branch-strategy.html)

<br><br>
### <b>GIT WORKFLOW</b>

master, <b>develop, feature</b>, release, hotfix 5개 종류의 branch로 나누어 관리  

처음에는 master & develop branch가 존재. develop branch에는 상시로 버그를 수정한 커밋들이 추가  

새로운 기능 추가 작업이 있는 경우 feature branch를 생성함.  

develop에서 release 브랜치를 생성.  

QA를 진행하면서 발생한 버그들은 release branch에 수정되고, 통과되면 master와 develop으로 merge  

마지막으로 출시된 master branch에서 version tag를 추가


 - feature  
 새로운 기능이 추가되어야 할 때 사용되는 branch. parent branch는 develop.
 개발이 완료되었을 시 develop branch로 merge  
 
 - develop  
 product로 release할 준비가 된 안정적인 branch. master로 merge하기 전 개발된 모든 feature가 develop에 merge  
 
 - release  
 develop에서 feature들을 merge하고 release해야할 시점에 생성할 branch. 
 dev에서 release로 merge. release 완료되면 release cycle 진행  
 
 - hotfix
 release된 product에서 발생한 버그 수정 시 사용하는 branch.
 수정 완료 후에는 수정사항을 반영하기 위해 master와 dev 모두에 merge  
 
 - master
 product로 release하는 branch. 모든 변경사항이 결국은 master로 merge되어야 함.
 
 
 <br><br>
 ### <b>FEATURE</b>
 
 `git flow init`을 입력하게 되면 develop branch가 생성된다.  
 
 release를 위한 feature 개발하고자 할 때, feature branch 생성해야 함!  
 
 `git flow feature start [branch_name]`  
 
 기능을 끝내면, `git flow feature finish [branch_name]`  
 
 finish를 통해 develop branch로 자동으로 merge하게 됨. 작업이 끝난 feature branch 삭제
 
 <br>
 
 `git flow feature publish [branch_name]`을 통해 remote에 게시할 수 있음.
 
 feature branch를 develop에 merge하고, 해당 feature branch를 삭제하고 develop branch로 돌아감
 
 `git flow feature pull origin [branch_name]`을 통해 remote에서 받아올 수 있음.
 
 <br><br>
 ### <b>RELEASE</b>
 
 ```git
 git flow release start <version>
 # local 저장소에만 branch를 생성했기 때문에 다른 release commit 반영 불가
 
 git flow release publish <version>
 # 다른 commit 도 반영하고 싶다면 release branch를 생성하여 게시
 
 git flow release finish <version>
 # release가 완료되면 release version으로 태그를 달고 master branch에 merge한다.
 # release 변경 사항을 develop에도 반영하고, release branch는 삭제한다.
 
 git push --tags
 ```
 
 <br><br>
 ### <b>HOTFIX</b>
 
 현재 출시된 제품에 문제가 생겨서 즉시 대응할 때 필요함. master branch에서 hotfix branch따서 시작.
 
 ```
 git flow hotfix start <version> [BASENAME]
 # option으로 BASENAME으로 시작점을 지정할 수 있다.
 
 git flow hotfix finish <version>
 # 핫픽스를 완료하면 develop branch & master branch에 merge 된다.
 ```
 
 
 
