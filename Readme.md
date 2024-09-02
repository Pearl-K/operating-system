# 1. 활동 관련 규칙
## 1.1 매주 활동 내역
1. 매주 월요일 오후 6시-6시 30분까지 회의 시간으로 비울 것
2. 매주 월요일 아침까지 해당 주차 챕터 관련 학습 내용 및 이슈 질문 답변 업로드
3. 매주 월요일 아침에 사다리타기로 당첨된 1명 월요일 회의 시간에 이야기한 내용과 이슈 정리
4. 남이 만든 자료라도 부족한 부분 있으면 채워주고 실제 개발하면서 볼 수 있는 예시와 함께 최대한 고민해보기

# 2. 파일 변경 관련 규칙
## 2.1 Github 내 프로젝트 클론

메인 프로젝트 fork!
![img.png](img/readme_main/clone_image.png)

fork 생성! Owner를 본인으로 놓고 생성하면 됨!
![img.png](img/readme_main/fork_image.png)

## 2.2 인텔리제이 IDE 접속 및 커맨드

따로 fork해서 dev나 뭐 아무 이름 브랜치 생성해서 PR 보내고 카톡 남기면 됨. 개인적으로 확인하고 PR 내용 보고 병합하겠음.
```shell
데이터 가져오기
> git clone [복사URL]
> git checkout -b dev
> git pull
글 작성 후
> git add -A
하단 커밋 메시지 규칙: [작성자] : 변경 주차, 간단 이유
> git commit -m [커밋 메시지]
> git push origin
```

## 2.3 PR 날리기

1. 직접 깃허브에 본인 깃허브 디렉토리로 이동

2. Pull-requests로 이동, New pull request 이동

![img.png](img/readme_main/pull_request_image.png)

3. Pull request 생성, 주의 사항은 본인이 작성한 페이지로 이동해야 함.

![img_1.png](img/readme_main/pull_request.png)

4. request 날리면 카톡으로 *** PR 확인 요청 부탁 ***

## 2.4 폴더 관리 관련 규칙
폴더 구조는 초기 운영체제 스터디에서 정한 8개의 챕터로 분리를 함. 
각 주차 폴더 내에는 question 폴더와 내부에 image 폴더가 있음.

1. question.md에는 해당 챕터 담당자가 해당 챕터 관련 질문 내용 정리 후 업로드
2. 정리 내용 이미지는 question 폴더 내의 image 폴더에 본인 이름으로 폴더 생성 후 저장
