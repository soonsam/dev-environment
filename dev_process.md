Trello, Github, Slack을 활용한 개발 프로세스
======================
개발 흐름 정의
---
![사진 1][dev_process]
[dev_process]: http://www.mimul.com/pebble/default/images/blog/Projects/dev_process.png

개발 프로세스(Trello, Github, Slack)
---
#### 1. Trello Card 만들기
1.1 기본적인 Trello 흐름
+   `Trello에서 개발해야 할 기능을 [To Do(Story)]라는 이름의 리스트에 카드로 만든다.`
+   `해당 스토리(카드)를 개발자가 구현에 들어가면 [Doing(WIP)] 리스트에 카드를 옮긴다.`
+   `리뷰에 들어가면 [Review(Sprint1)] 리스트로 옯기고`
+   `개발 브랜치가 병합하여 테스트를 완료하면 [Done(Sprint1)] 리스트에 카드를 옮기고 해당 기능을 클로즈한다.`

	![사진 2][trello]

1.2 Trello카드 내용은 Description란에는 이슈 링크를 걸어주거나 wiki 링크를 걸어줘 해당 스토리의 정보를 알 수 있도록 해준다. 그리고 Spec을 참조하여 Checklist를 추가해 완료조건을 기술해 개발해가면서 하나씩 처리해 나간다.

![사진 3][trello_card]

1.3 카드 처리 및 이동시 Slack Alert를 줘 실시간으로 처리가 가능하다.

![사진 4][trello_alert]

[trello]: http://www.mimul.com/pebble/default/images/blog/Projects/trello.png
[trello_card]: http://www.mimul.com/pebble/default/images/blog/Projects/trello_card.png
[trello_alert]: http://www.mimul.com/pebble/default/images/blog/Projects/trello_alert.png

#### 2. Branch 만들고 Pull Request, Merge 하기
2.1 소스 리모트와 동기화
```
> git checkout master && git pull origin master && git fetch -p origin
```
2.2 브랜치명을 만들고 브랜치로 이동
```
> git checkout -b dev_standard
```
2.3 작업 후 커밋
```
> git commit -a -m "[VOY-201] README git 사용법 추가"
# 커밋 작성은 issue ID를 넣고 내용은 구체적으로 제시한다.
```
2.4 master 현행화
```
> git checkout master
> git pull origin master && git fetch -p origin
```
2.5 작업을 완료 후 master branch로 변경하여 remote에 새로운 변경 사항을 반영
```
> git checkout dev_standard
> git rebase master
```
++변경사항이 있다면 working branch에서 rebase를 수행한다. 없으며 merge를 수행. rebase 중 충돌이 발생하면 아래 수행하고 아니면 넘어감.++
```
> git add .
> git rebase --continue
```
2.6 Pull Request 요청하기 전에 Trello 카드도 [Review] 리스트로 옮기고 Checklist 하나를 더 만들어서 Review 내용(리뷰 담당자 포함)을 기술하면 Trello 알림을 통해 담당자가 리뷰어임을 알게 된다. 아니면 Trello 카드를 만들때 Review Checklist를 만들어서 리뷰 대상자를 등록한다.

2.7 Pull Request 요청
```
> git push origin dev_standard
```
- push 후 Github의 repository로 이동해서 Compare & pull request 버튼 클릭하고 코멘트(To close VOY-201(jira issue 번호), 혹은 github issue 사용하면 #1234로) 남기고 Create pull request 버튼 클릭한다.

![사진 5][create_pullrequest]

- 이때 Pull Request 정보가 Slack을 통해 담당자에게 보내지게 되고 리뷰를 수행한다.

![사진 6][slack_pullrequest]

- 리뷰 수행 후 수정사항이 있으면 수정 후 Pull Request를 다시 보내고, 수정 사항이 없으면 Github에서 Merge pull request를 클릭하고 Confirm merge 버튼을 클릭해서 merge를 완료한다.

- Github에서 Delete branch 버튼을 클릭하거나 아래 로컬에서 커맨드로 원격 브랜치를 삭제한다.
```
> git push origin :dev_standard
```

2.8 Review 후 수정사항이 있는 경우 수정한 다음 2.7을 재 수행한다.

2.9 로컬 master 동기화
```
> git checkout master && git pull origin master && git fetch -p origin
```
- 로컬 브랜치 삭제
```
> git branch -d dev_standard
```

[slack_pullrequest]: http://www.mimul.com/pebble/default/images/blog/Projects/slack_pullrequest.png
[create_pullrequest]: http://www.mimul.com/pebble/default/images/blog/Projects/create_pullrequest.png

#### 3. 배포하기
- 테스트 코드를 돌리고, jenkins나 자체 배포도구를 활용하여 운영 서버에 소스를 배포한다.

#### 4. 배포후 확인
- 기능 테스트를 눈으로 확인하면서 화면의 깨짐, 데이터의 정확성, 브라우저 호환성 등을 점검한다.
- Selenium 도구를 통해 브라우저단에서 테스트를 할 수 있는데 이 Selenium이 구동한 브라우저의 결과 화면을 아이컨텍해서 봄으로써 어느 정도 테스트 자동화를 할 수 있다.

#### 5. Trello 카드 Done 리스트로 이동
- 배포후 확인에서 이상이 없다면 Trello의 카드를 [Done(Sprint1 - 날짜기간)] 리스트에 이동시키고, 이슈를 close한다.