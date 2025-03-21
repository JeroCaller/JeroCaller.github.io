---
title: "Github Issue & Project로 프로젝트 관리하기"
category: "Git & Github"
tag: ["Github", "Issue", "Project", "Milestone", "Kanban", "Project Management"]
---


Github에서는 단순히 코드 저장 및 버전 관리에 관한 기능들만 제공할 뿐만 아니라, 프로젝트 및 이슈 관리를 위한 툴들도 제공해준다. 이 글에서는 Github에 존재하는 이슈 및 프로젝트 관리 툴에 대해 간단히 살펴보고자 한다. 

# Issue Tracker

프로젝트에서 Issue란, 버그, 개선 사항 뿐만 아니라 새로 추가할 기능 모두를 아우르는 개념이며, 개인이 수행할 수 있는 업무의 최소 단위이다. 티켓(Ticket), 케이스(Case), 테스크(Task)라고도 부른다. 

한 편 이슈에는 각종 정보들이 담긴다. 

- 담당자: 해당 이슈 담당자
- 목표: 해당 이슈에서 해결, 달성하고자 하는 목표
- 우선순위: 모든 업무가 똑같은 우선순위를 가지진 않을 것이다. 그래서 각 이슈마다 우선순위를 부여하고, 담당자는 여러 이슈들 중 가장 우선순위가 높은 이슈를 먼저 해결한다.
- 마감일: 이슈를 해결해야하는 날짜
- 상태: 기본적으로는 열림 및 닫힘(Open, Close)으로 구분한다. 여기서 열림은 아직 해결되지 않은 이슈를 의미하고, 닫힌 이슈는 해결되었거나 어떠한 이유로 더 이상 해결할 이유가 없어진 이슈에 대해 부과하는 상태이다. 이 외에도 필요에 따라 “수정됨”, “수정 불가” 등의 여러 세부적인 상태들도 추가할 수 있다.

이 외에도 필요에 따라 추가적인 정보들을 이슈에 담을 수 있다고 한다. 

Issue Tracking System(ITS)은 프로젝트 내에서 발생하는 이러한 이슈들을 추적, 관리하기 위한 시스템을 말한다. 이러한 시스템이 존재해야 어떤 점이 아직 해결되지 않았는지 등 프로젝트 내 여러 상황들을 한 눈에 살펴볼 수 있다는 장점이 있다. 팀의 규모가 클수록 수많은 이슈들을 관리할 ITS를 사용함으로써 각 팀원 간의 역할 분담 및 중복 일 처리 방지 등의 효과를 얻을 수 있다. 

이러한 ITS의 작업 흐름(Workflow)은 대게 이렇다. 

- Opened: 이슈를 등록한다. 버그를 발견했거나 새로운 기능 추가 필요 등의 여러 이유로 이슈를 등록할 수 있다.
- 그 후, 이슈에 지정될 여러 정보들을 지정한다. 담당자, 목표, 우선순위 등이 그 예이다. 이렇게 하여 이슈를 분류한다. 그리고 필요하면 이슈에 대해 토의할 수 있다.
- Closed: 이슈가 해결되었거나 모종의 이유로 해결할 수 없음이 밝혀진 경우 이슈를 닫는다.
- Reopened: 만약 이미 닫힌 이슈를 어떠한 이유로 다시 열림 상태로 바꿔 작업을 진행해야 할 경우 선택적으로 닫힌 이슈를 다시 열 수 있다. 이전에는 기술상 해결할 수 없다고 판단되어 닫았다가 나중에 해결책을 발견한 경우, 또는 데드라인까지 얼마 안 남아 시간 내에 특정 기능을 구현할 수 없을 거라 생각했으나 갑자기 데드라인이 뒤로 미뤄졌거나 등등 여러 상황에서 사용할 수 있겠다.

이러한 이슈 추적, 관리를 도와주는 도구를 Issue Tracker라 한다. 여기에는 수많은 종류의 Issue Tracker가 있다고 한다. 이 글에서 다룰 Github Issues 뿐만 아니라 Jira, Asana 등등이 있다고 한다. 

# Github Issues

Github Issues는 각 리포지토리마다 사용할 수 있으며, 다음과 같이 따로 “Issues” 탭이 있어 여기서 이슈를 등록, 관리할 수 있다. 

![사진 1-1. Github Issues 탭](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/1.png)

사진 1-1. Github Issues 탭

## Issue Template 생성 및 사용법

한 편, 보통은 이슈를 작성하다보면 공통적인 양식이 있어 아예 템플릿으로 만들어 사용하면 더 편리할 것이다. Github Issues를 살펴보기 전에 Issue Template을 생성하는 방법에 대해 먼저 살펴보고 이를 Github Issues에서 사용하여 이슈를 등록해보도록 하겠다. 

1. 먼저, 특정 리포지토리에서 “Settings” 탭에 들어간 다음 아래로 스크롤을 내린다. 
    
    ![사진 2-1.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/2.png)
    
    사진 2-1.
    

1. 그 다음, 아래 사진처럼 “Features” 부분에서 “Issues” → “Set up templates”를 클릭한다. 
    
    ![사진 2-2. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/3.png)
    
    사진 2-2. 
    

1. 그러면 다음의 화면이 뜬다. 여기서는 원하는 형식으로 템플릿을 만들고자 한다. 그래서 “Custom template”를 클릭한다. 
    
    ![사진 2-3. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/4.png)
    
    사진 2-3. 
    

1. 그러면 다음과 같이 새 템플릿 항목이 생긴다. 해당 항목에서 “Preview and edit”을 클릭하여 템플릿을 만들어보자.
    
    ![사진 2-4.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/5.png)
    
    사진 2-4.
    

1. 다음과 같이 원하는대로 템플릿 이름, 해당 템플릿의 정보 및 템플릿 내용을 적는다. 필자는 투두를 작성할 목적의 템플릿을 다음과 같이 만들어보았다. 참고로 마크다운 문법을 지원한다. (여기서는 필자가 실수를 해서 제대로 마크다운 문법이 적용되지 않은 상태로 진행하고 말았다. 이를 감안하고 봐주시길 바란다)
    
    ![사진 2-5.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/6.png)
    
    사진 2-5.
    

1. 다 작성하였으면 “Close preview”를 누른다. 다시 살펴보면 다음과 같이 마크다운 문법이 적용된 상태의 템플릿 내용을 확인해볼 수 있다. 
    
    ![사진 2-6.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/7.png)
    
    사진 2-6.
    
2. 앞서 만든 템플릿을 저장하기 위해 Commit을 해야 한다. 이렇게 이슈 템플릿을 만들어 등록하는 과정도 Commit을 통해 진행된다. 우측 상단에 있는 초록색의 “Propose changes” 버튼 클릭 후 아래에 뜨는 입력란에 커밋 메시지를 기록한 후, 아래의 “Commit changes”를 클릭한다. 
    
    ![사진 2-7.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/8.png)
    
    사진 2-7.
    

1. 그러면 이슈 템플릿이 등록된다. 다음과 같이 `.github/ISSUE_TEMPLATE` 폴더가 저장소에 생성된다. 
    
    ![사진 2-8.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/9.png)
    
    사진 2-8.
    
    ![사진 2-9.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/10.png)
    
    사진 2-9.
    

여기까지 이슈 템플릿 생성 방법이었다. 이제 Github Issues에서 이 이슈 템플릿을 이용하여 이슈를 하나 작성해보자. 

1. “Issues” 탭을 누르면 다음의 화면이 뜬다. 해당 화면에는 여태까지 등록된 Open(또는 Closed)된 이슈들을 목록으로 살펴볼 수 있다. 지금은 처음 사용하므로 아무런 이슈가 없다. 새 이슈를 등록하기 위해 우측의 “New issue” 버튼을 클릭한다. 
    
    ![사진 2-10.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/11.png)
    
    사진 2-10.
    

1. 그러면 다음과 같이 모달창이 뜬다. 여기서 앞서 만든 이슈 템플릿을 클릭한다. 만약 아무런 템플릿 없이 그냥 이슈를 작성하고자 한다면 아래의 “Blank Issue”를 눌러 작성해도 된다. 
    
    ![사진 2-11.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/12.png)
    
    사진 2-11.
    

1. 그러면 다음의 창이 뜬다. 앞서 설정한 템플릿 내용이 그대로 있고, 이를 필요한 경우 조금씩 수정하여 작성하면 되겠다. 다 작성하였으면 우측 아래의 “Create” 초록색 버튼을 클릭한다. 
    
    ![사진 2-12.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/13.png)
    
    사진 2-12.
    

1. 그러면 다음과 같이 이슈가 등록된다. 
    
    ![사진 2-13.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/14.png)
    
    사진 2-13.
    
    ![사진 2-14. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/15.png)
    
    사진 2-14. 
    

이렇게 이슈를 등록하는 방법에 대해 살펴보았다. 해당 이슈를 닫고자 하는 경우, 목록에서 해당 이슈를 클릭하면 위 사진 2-13과 같은 화면이 보일 것인데, 아래의 “Close Issue”를 클릭하면 된다. 닫힌 이슈는 “Closed” 탭에 가서 다시 Reopen 할 수도 있다. 

한 편 사진 2-13의 우측을 보면 하나의 이슈에 여러 가지 정보들을 등록할 수 있음을 알 수 있다.

- Assignees : 해당 이슈 담당자. 자기 자신을 설정할 수도 있다.
- Labels : 해당 이슈에 붙일 라벨. 해당 이슈가 어떤 성격을 지니고 있는지 쉽게 식별하기 위해 사용된다. “bug”, “documentation” 등 여러 가지를 기본적으로 제공하고, 필요한 경우 직접 만들거나 기존의 것을 삭제하여 관리할 수도 있다.
    
    ![사진 2-15. 첫 이슈 화면에서 “Labels” 버튼 클릭 시 보이는 화면. 기본 제공하는 라벨들이 있으며, 필요하다면 새로운 라벨들을 추가하여 특정 이슈에 부여할 수 있다. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/16.png)
    
    사진 2-15. 첫 이슈 화면에서 “Labels” 버튼 클릭 시 보이는 화면. 기본 제공하는 라벨들이 있으며, 필요하다면 새로운 라벨들을 추가하여 특정 이슈에 부여할 수 있다. 
    
- Projects : 연관된 프로젝트를 연결시키고자 할 때 사용. 여기서의 프로젝트는 프로젝트 관리를 위해 현재 프로젝트 내 각 이슈 별 진행 사항을 살펴보기 위한 일종의 프로젝트 관리 툴인 Github Projects을 말한다. 이에 대해서는 아래에 후술할 Github Projects에 대해 설명할 때 같이 살펴볼 것이다.
- Milestone : Milestone은 여러 특정 이슈들을 하나로 묶은 일종의 그룹이며, 일종의 목표 지점을 설정하는 역할을 한다.

## Milestone으로 여러 이슈들을 묶어 관리하기

Milestone을 사용하면 특정 카테고리로 분류될 수 있는 여러 이슈들을 하나로 묶어 데드라인을 설정할 수 있고, 해당 Milestone 내 이슈들 중 몇 개가 닫혔는지에 따라 이슈 해결 상황을 한 눈에 파악할 수 있다. 

1. Issues 탭 첫 화면의 우측 상단에 있는 “Milestones”를 클릭한다. 
    
    ![사진 3-1.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/17.png)
    
    사진 3-1.
    

1. 그러면 다음과 같이 Milestones 목록이 뜬다. 지금은 하나도 생성하지 않았기에 아무것도 뜨지 않았다. 새 Milestone을 생성하기 위해 우측의 “New milestone”을 클릭한다. 
    
    ![사진 3-2. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/18.png)
    
    사진 3-2. 
    

1. 다음의 화면에서 Milestone의 제목, 데드라인, 설명을 작성한다. 다 작성하였으면 우측 하단의 “Create milestone”을 눌러 새 Milestone을 생성한다. 
    
    ![사진 3-3.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/19.png)
    
    사진 3-3.
    

1. 그러면 다음과 같이 방금 만든 Milestone이 목록에 등록된다. 이 화면에서 몇 개의 이슈들이 열리거나 닫힌 상태인지 파악할 수 있다. 전체 열린 이슈들 중 닫힌 이슈들의 비율을 계산해줌으로써 해당 Milestone에서 목표로 하고 있는 진행도를 파악할 수 있는 것이다. 
    
    ![사진 3-4.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/20.png)
    
    사진 3-4.
    
2. 이제 이 Milestone을 사용해보기 위해 이미 등록했던 Issue 몇 개를 이 Milestone에 포함시켜 보겠다. 특정 이슈를 클릭한 후 우측 화면의 “Milestone”에서 앞서 등록한 Milestone을 등록해준다. 그러면 해당 이슈는 이제 해당 Milestone에 포함되는 것이다. 
    
    ![사진 3-5.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/21.png)
    
    사진 3-5.
    

1. 필자는 다음과 같이 두 개의 이슈를 해당 Milestone에 등록한 뒤, 그 중 하나를 Closed 상태로 전환시켜보았다. 그러면 다음과 같이 50% 일이 진척되었다고 표시된다. 이를 이용하여 특정 이슈들을 하나로 묶어 관리하고, 데드라인까지 어느 정도의 진행이 되었는지 파악할 수 있는 것이다. 
    
    ![사진 3-6. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/22.png)
    
    사진 3-6. 
    

## 커밋 메시지를 이용하여 특정 이슈 자동으로 닫게 하기

한 편, 이렇게 Github Issues를 이용하면, 특정 이슈를 해결한 커밋의 메시지에 해당 이슈 ID를 지정한 뒤, PR(Pull Request) 및 main(master) 브랜치(정확히는 default branch로 지정된 브랜치)로 merge하면 해당 이슈가 자동으로 close 상태로 전환된다. 

1. 테스트를 위해 원격 저장소 내에 테스트용 파일을 생성한 뒤, 이를 “feature/B”라는 새로운 브랜치를 생성하여 여기에 커밋을 하고자 한다. 본래는 자신의 로컬 저장소에서 브랜치 분기 및 커밋 메시지를 작성한 뒤 원격 저장소에 push하는 과정을 거치겠지만 여기선 간단한 테스트를 위해 아래 사진처럼 Github 원격 저장소에서 바로 진행하였다. 
    
    ![사진 4-1. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/23.png)
    
    사진 4-1. 
    
    Github 공식 문서 [https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword) 에 따르면, 커밋 메시지의 footer에 “resolves”, “closes” 등의 키워드를 이용하면 이를 자동으로 인식하여 해당 이슈를 닫아준다고 한다. 위 사진에서는 `resolves: #4` 와 같이 작성하여 Github Issues에서 부과된 특정 이슈의 번호를 지정하여 해당 이슈가 해결되었음을 명시하고 있다. 커밋 메시지 작성 완료 후 “Propose changes”를 누른다. 
    

1. 그 후 해당 커밋에 대해 PR을 한다. 그러면 다음과 같은 화면이 뜰 것이다. 원래라면 협업 시 아래 화면에서 코드 리뷰를 통해 피드백을 받거나 해당 기능을 main 브랜치에 병합시켜 추가할 지 여부 등을 토의할 것이다. 여기서는 이러한 과정을 모두 마쳐 병합하기로 했다고 가정한다. “Merge pull request”를 클릭하여 해당 브랜치를 main 브랜치에 병합시킨다. 
    
    ![사진 4-2.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/24.png)
    
    사진 4-2.
    
    ![사진 4-3.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/25.png)
    
    사진 4-3.
    
2. Issues 탭을 들어가 확인해보면 다음과 같이 앞선 커밋에서 참조했던 이슈인 “테스트] B 기능 개발하기”가 자동으로 Closed 상태로 변한 것을 볼 수 있다. 
    
    ![사진 4-4.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/26.png)
    
    사진 4-4.
    

주의할 점은, 위와 같이 커밋 메시지에서 특정 이슈 ID를 명시하여 자동으로 해당 이슈가 닫히게 하려면 먼저 main 브랜치가 아닌 다른 브랜치에서 커밋을 한 후, 이를 반드시 main 브랜치로 merge 해달라는 PR을 요청해야 한다. 만약 main 브랜치로 merge하지 않거나 또는 PR을 거치지 않고 바로 병합해버리면 특정 이슈가 자동으로 닫히는 기능을 사용할 수 없다고 한다. 

# Github Projects

한 편, Github에는 “Projects”라는 것도 제공하여 프로젝트 관리를 할 수 있다고 한다. Github Projects를 생성해보고, 이를 기존의 특정 리포지토리의 이슈와 연관지어 이슈 기반 프로젝트 관리를 해보자. 

먼저 프로젝트를 하나 만들어보자. 

1. “Projects” 탭을 클릭하면 다음과 같은 화면이 뜰 것이다. 프로젝트는 처음이므로 목록에 아무것도 뜨지 않는다. 우측의 초록색 “New project” 버튼을 누른다. 
    
    ![사진 5-1. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/27.png)
    
    사진 5-1. 
    

1. 그러면 새로운 창이 뜬다. 여기에는 각종 템플릿들이 기본으로 제공된다. 여기서는 “Board”를 클릭하여 보드 형식의 프로젝트를 만들어보겠다. 다음과 같이 원하는 이름으로 프로젝트 이름을 부여한 뒤, 우측 하단의 “Create project” 버튼을 클릭한다. 
    
    ![사진 5-2.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/28.png)
    
    사진 5-2.
    

1. 그러면 다음과 같은 보드 형태의 프로젝트가 생성된다. 
    
    ![사진 5-3.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/29.png)
    
    사진 5-3.
    

위 사진과 같은 형태를 “칸반 보드(Kanban Board)”라 하는데, 칸반 보드는 프로젝트 진행 단계를 위 사진에서처럼 여러 컬럼으로 나눈 후, 이슈를 나타내는 카드들을 현재 진행 단계에 따라 맨 왼쪽 컬럼부터 오른쪽으로 전진시키는 형식의 보드를 말한다. 위 사진에서는 왼쪽부터 차례로 “Todo”, “In Progress”, “Done” 컬럼이 기본 제공된다. “Todo”는 해야 할 일이지만 아직 시작하지 않은 일들을 모아둔 컬럼이고, “In Progress”는 현재 진행 중인 작업들을 모아두는 컬럼이다. “Done”은 작업이 완료된 것들을 모아두는 컬럼이다. 즉, 해야 할 일들을 Todo 컬럼에 설정한 뒤, 현재 시작, 진행하고자 하는 이슈는 Todo에서 In Progress로 옮긴다. 그리고 해당 작업이 모두 완료되면 Done 컬럼으로 이동시키는 방식이다. 이러한 방식으로 현재 프로젝트의 진행 상황을 한 눈에 파악하는 것이다. 

물론 원한다면 컬럼 이름을 변경하거나 원하는 컬럼을 추가해도 된다. 필자는 Project라는 기능을 빠르게 살펴보기 위함이기에 여기서는 그대로 나뒀다. 

이를 통해 알 수 있는 것은, Github에서 제공하는 저 “Project”라는 개념은 프로젝트 관리를 위한 일종의 툴을 의미하는 것이라는 사실이다. 

물론 위 상태에서 아래의 “Add Item” 버튼을 클릭하여 원하는 항목을 등록할 수 있다. 하지만 여기서는 이 프로젝트를 앞서 살펴본 “Github Issues”와 같이 연동하여 이슈들을 칸반 보드 형식의 프로젝트에서도 관리할 수 있도록 해볼 것이다. 

앞선 실습에서 진행한 리포지토리에서, 특정 이슈들이 새로 생성되어 Open되면 해당 이슈들이 자동으로 이 프로젝트에 추가되도록 설정해보겠다. 

> 프로젝트의 이슈 자동 연동과 관련하여, 필자가 조사해봤을 때 다른 블로거 분들이 소개한 방식과 현재 Github 내 화면이 완전히 달라졌다. 예를 들어 원래는 “Automated kanban”이라는 별도의 탭이 존재하여 이를 클릭하여 프로젝트를 생성하면 자동으로 이슈가 연동되는 방식이 있었다고 한다. 그런데 현재 Github 내에서 살펴보면 해당 기능이 존재하지 않는다. 아마도 그 사이에 Github 내에서 UI가 달라진 것으로 보인다.
> 

1. 앞서 생성한 프로젝트 화면에서 우측의 “…” 버튼 클릭 후 “Workflows”를 클릭한다. 
    
    ![사진 5-4.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/30.png)
    
    사진 5-4.
    
2. 그러면 아래와 같은 화면으로 바뀔 것이다. 왼쪽 사이드바에서 “Auto-add to project”를 클릭한다. 그러면 아래 사진처럼 보일 것이다. 아래 Filters에서 매핑하고자 하는 특정 리포지토리를 선택한다. 그리고 우측의 입력란에는 화면에서처럼 “is:issue,pr is:open label:todo”라 작성하였다. 이는 open된 상태이고, label이 “todo”인 이슈 또는 PR들을 자동으로 해당 프로젝트에 추가하겠다는 뜻이다(사실 이 쿼리가 기본으로 작성되어 있어서 필자는 레이블만 “todo”로 설정한 것이다). 다른 태그들도 자동으로 추가되길 원한다면 label 항목 뒤에 해당 태그명을 추가로 입력해주면 된다. 
    
    ![사진 5-5. ](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/31.png)
    
    사진 5-5. 
    

1. 그러면 해당 리포지토리의 “issues” 탭으로 이동하여 새로운 이슈를 생성해보자. 이 때 label을 todo로 설정한다. 새로 생성된 이슈가 다음과 같이 등록될 것이다. 잠시 기다리면 아래 화면의 우측에 “Projects” 영역에 앞서 생성한 Project가 자동으로 매핑될 것이다. 이는 앞서 프로젝트에서 설정한 “Open”된 “todo” 라벨을 가지는 “이슈”라는 조건에 모두 부합되기 때문에 자동으로 매핑된 것이다. 처음에는 이슈가 해당 프로젝트 내에서는 “No Status” 컬럼으로 분류될 것이다. 여기서 아래 사진처럼 “Todo”로 바꿔보자. 
    
    ![사진 5-6.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/32.png)
    
    사진 5-6.
    

1. 그러면 다음과 같이 해당 이슈가 자동으로 프로젝트에도 추가되는 것을 볼 수 있다. 
    
    ![사진 5-7.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/33.png)
    
    사진 5-7.
    

만약 특정 이슈가 시작 전 단계에서 진행 중 단계로 바뀌었다면 해당 이슈 화면의 Projects 칸에서 “In Progress”로 바꾸면 된다. 그러면 칸반 보드에서도 자동으로 해당 이슈가 “In Progress” 컬럼으로 이동된다. 해당 이슈를 해결하여 close하면 프로젝트에서도 자동으로 “Done” 컬럼으로 이동된다. 

이와 같이 Github Projects를 Github Issues와 연동하여 사용할 수 있다. 그러면 프로젝트를 진행하면서 이슈를 기반으로 프로젝트를 관리할 수 있을 뿐만 아니라 Github Projects를 통해 특정 이슈들의 진행 상황을 한 눈에 살펴볼 수 있는 것이다. 

앞서 살펴본 모든 기능들을 종합하면 이슈에서는 milestone을 이용하여 특정 이슈들의 진행도를 파악할 수 있고, 커밋 메시지에 특정 이슈를 언급하여 PR 및 default branch에 merge함으로써 자동으로 해당 이슈를 close 할 수 있다. 그리고 이는 Github Projects에도 자동 반영시킬 수 있다. 

한 편, Github Projects는 원래 리포지토리와 무관하게 생성된다. 이러한 이유로, 생성한 프로젝트는 앞서 살펴본 과정을 통해 이슈를 자동 추가할 수는 있지만, 그럼에도 해당 프로젝트가 특정 리포지토리와 연결되어 있다는 느낌을 받기는 힘들 것이다. 이를 명시적으로 매핑하는 방법도 있다. 

1. 특정 리포지토리로 이동한다. 해당 리포지토리 화면에서도 “Projects” 탭을 볼 수 있다. 해당 탭을 클릭하면 다음의 화면을 볼 수 있다. 아래의 “Link a project”를 클릭한다. 
    
    ![사진 5-8.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/34.png)
    
    사진 5-8.
    
2. 그러면 다음과 같은 작은 창이 뜬다. 여기서 연결하고자 하는 프로젝트를 선택하여 체크 표시한 후 “Submit” 버튼을 클릭한다. 
    
    ![사진 5-9.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/35.png)
    
    사진 5-9.
    

1. 그러면 다음과 같이 해당 프로젝트가 특정 리포지토리와 링크된 것을 볼 수 있다. 
    
    ![사진 5-10.](/images/2025-03-21/Github%20Issue%20%26%20Project%EB%A1%9C%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0/36.png)
    
    사진 5-10.
    

# 글을 마치며…

사실 이전부터 Github Issues 및 Projects의 존재는 알았지만 이들이 각각 무얼 하는 건지는 몰랐었다. Issue의 경우 필자는 Github Pages를 이용하여 기술 블로그를 운영하고 있고, 여기에 각 포스트마다 댓글 기능을 연동하도록 구성하면서 접했던 적이 있었다. 이 때 이 댓글이 Github Issues와 연동되는 방식이었기 때문이다. 또한 보통 코딩 관련 책들에서는 학습을 위해 저자가 자신의 깃허브 리포지토리 주소를 남기곤 한다. 그리고 해당 리포지토리로 가보면 항상 “issue” 탭에 QnA가 마련되어 있었다. 그래서 필자는 이 이슈 탭이 단순히 댓글 또는 질문 및 토의를 거치기 위한 곳인 줄 알았다. 그런데 사실 기본적으로는 프로젝트 진행 시 이슈를 관리하는 곳임을 이번 기회에 알게 되었다. 물론, 상황에 따라 댓글, 토의를 위한 곳으로 써도 될 것이다. 

그런데 Github Projects는 뭐하는 곳인지 전혀 알지 못했었다. 처음에는 단순히 “내가 경험했던 프로젝트들을 올려 마치 프로필처럼 쓰는 곳인가?” 란 생각을 했었다. 그런데 이번 기회에 알고 보니 프로젝트 관리를 위한 곳임을 알게 되었다. 

그래서 사실 이 글은 프로젝트 관리를 위한 Github 활용 방법을 다루는 글이었다. 생각 외로 사용법이 복잡하지 않으면서도 친숙한 Github를 이용하여 프로젝트 관리를 할 수 있다니 흥미로웠다. 물론 이 글을 작성한 시점에서는 본격적으로 실제 프로젝트를 하면서 이 기능들을 사용해본 건 아니라서 Github에서 제공하는 이 툴들의 진정한 장단점을 체험해보진 않았지만 지금으로서는 꽤 유용해 보인다. 최근 스프링 부트 기반 간단한 라이브러리를 제작하고자 하는데, 이 프로젝트의 관리를 이걸로 한 번 해봐야겠다. 이를 통해 Github에서 제공하는 프로젝트 관리 툴을 직접 체험해봐야겠다. 

---

References

[1] [[GitHub] Issue, Project 활용](https://velog.io/@dohaeng0/GitHub-Project-Issue-%ED%99%9C%EC%9A%A9)

[2] [GitHub로 프로젝트 관리하기 Part1 - 이슈 발급 부터 코드리뷰까지 \| Popit](https://www.popit.kr/github%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0-part1-%EC%9D%B4%EC%8A%88-%EB%B0%9C%EA%B8%89-%EB%B6%80%ED%84%B0-%EC%BD%94%EB%93%9C%EB%A6%AC%EB%B7%B0%EA%B9%8C/)

[3] [[git, github] git issue 생성 및 작성 방법 (1)](https://hyeonic.tistory.com/181)

[4] [[Github] 협업시 프로젝트(Projects)와 이슈(Issue) 사용하기](https://devlog-wjdrbs96.tistory.com/227)

[5] 참고 자료) Github를 활용하여 프로젝트를 관리하는 방법

[GitHub - cheese10yun/github-project-management: :octocat:  Github로 프로젝트 관리하기](https://github.com/cheese10yun/github-project-management?tab=readme-ov-file)

[6] 이슈 트래킹 개념

[이슈 트래킹 시스템 탈탈 털기 — ITS 정의부터 Jira vs Asana vs Linear 비교까지](https://medium.com/saas-design-archive/%EC%9D%B4%EC%8A%88-%ED%8A%B8%EB%9E%98%ED%82%B9-%EC%8B%9C%EC%8A%A4%ED%85%9C-%ED%83%88%ED%83%88-%ED%84%B8%EA%B8%B0-its-%EC%A0%95%EC%9D%98%EB%B6%80%ED%84%B0-jira-vs-asana-vs-linear-%EB%B9%84%EA%B5%90%EA%B9%8C%EC%A7%80-4407eaee9199)

[7] 이슈 트래커 개념

[](https://namu.wiki/w/%EC%9D%B4%EC%8A%88%20%ED%8A%B8%EB%9E%98%EC%BB%A4)

[8] [Github / Issue Tracker와 Project Board](https://velog.io/@code-bebop/Github-Issue-Tracker%EC%99%80-Project-Board)

[9] 깃허브 공식 문서) 커밋 메시지에서의 이슈 트래커 작성으로 Github Issue 내 연관된 issue를 자동으로 닫게 하는 방법 및 관련 키워드

[Linking a pull request to an issue - GitHub Docs](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue)

[10] 참고) 깃허브 기반 1인 워크플로우 사례

[[GitHub]  깃허브로 1인 워크플로우 만들기](https://dev-dmsgk.tistory.com/16)

[11] 깃허브 공식 문서) markdown 문법들

[기본 쓰기 및 서식 지정 구문 - GitHub Docs](https://docs.github.com/ko/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#task-lists)

[12] 최근 형태의 github project에서 특정 이슈 생성 시 자동으로 project에도 추가해주도록 설정하는 방법

[GitHub Project Management - Create GitHub Project Board & Automations 2024](https://www.youtube.com/watch?v=oPQgFxHcjAw)

[13] 칸반보드

[간반 보드](https://ko.wikipedia.org/wiki/%EA%B0%84%EB%B0%98_%EB%B3%B4%EB%93%9C)