---
further_reading:
- link: service_management/case_management/settings
  tag: 설명서
  text: 케이스 설정
title: 사례 보기 및 관리
---

## 개요

{{< img src="/service_management/case_management/view_and_manage/view_and_manage_overview_cropped.png" alt="모든 사례 보기, 상태 선택 옵션 및 할당 번호 보기를 표시하는 사례 관리 폐이지" style="width:100%;" >}}

[사례 관리 페이지][1]에서 사례를 생성 날짜, 상태 또는 우선순위별로 정렬할 수 있습니다. 기본값으로 사례는 생성 날짜별로 정렬됩니다. 

프로젝트 내에서 사례를 일괄 편집하려면 체크박스를 사용하여 사례를 하나 이상 선택합니다. 그런 다음 드롭다운 메뉴를 사용하여 상태 관리, 할당 및 아카이빙 등의 작업을 일괄 수행합니다. 사례를 다른 프로젝트로 이동시키면 해당 사례에 신규 사례 ID가 할당됩니다. 기존 사례 URL을 사용하여도 신규 사례로 리디렉션되지 않습니다.

## 키보드 단축키
아래의 키보드 단축키를 사용하여 빠르게 탐색하세요.

| 작업              | 단축키       |
| ------------------  | ----------     |
| 위로             | `↑` 또는 `K`     |
| 아래로           | `↓` 또는 `J`     |
| 사례 선택         | `X`            |
| 선택한 사례 보기  | `Enter` 또는 `O` |
| 사례 생성       | `C`            |
| 상태 설정          | `S`            |
| 사용자에게 할당      | `A`            |
| 우선순위 설정        | `P`            |
| 프로젝트로 이동     | `V`            |
| 아카이브 / 아카이브 해제 | `E`            |

## 사례 검색

프로젝트 내에서 다음으로 사례를 검색할 수 있습니다.
- **속성 키-값 쌍**: 예를 들어, 이벤트 상관 관계 패턴으로 생성한 모든 사례를 찾으려면 `creation_source:Event Management`를 검색합니다. 개별 이벤트에서 생성된 사례의 경우  `creation_source:Event`를 검색합니다.
- **제목**: 검색 용어를 큰따옴표로 묶습니다. 예를 들어, 제목에 "쿠버네티스(Kubernetes) 포드"라는 용어가 포함된 모든 사례를 찾으려면 `"kubernetes pods."`를 검색합니다.

보다 복잡한 쿼리를 작성하려면 `AND`, `OR`, 그리고 `-` (exclusion). For example, `priority:(P2 OR P3)` returns cases of either priority `P2` or `P3`과 같은 대소문자를 구분하는 불리언 연산자를 사용할 수 있습니다.

아울러, 좌상단의 검색 바에서 모든 프로젝트의 사례를 검색할 수 있습니다.

## 보기 생성하기

**보기**는 저장된 쿼리 필터로, 본 기능을 사용하여 사례 목록에서 사용자와 가장 관련성이 높은 사례만 선별할 수 있습니다. 프로젝트에는 시작, 진행 중, 종료됨, 아카이브됨과 같이 각 상태에 대한 기본값 보기가 표시됩니다. 아울러, 할당되거나 생성한 사례에에 대한 기본값 보기도 있습니다. 

커스텀 보기를 만들려면:
1. 프로젝트 내에서 **보기 추가**를 선택합니다.
1. 보기 이름을 지정합니다.
1. 검색 상자에 쿼리를 입력합니다. 미리보기가 새로고침되어 현재 검색 쿼리와 매칭되는 사례가 표시됩니다.
1. (선택 사항) Slack, Microsoft Teams, PagerDuty 또는 Webhook와 같은 타사 도구를 사용하여 알림을 전송합니다. **+수신자 유형 추가**를 클릭하고 미리 구성된 채널 또는 수신자 중에서 선택합니다. 사용 가능한 도구 및 옵션에 대해 자세히 알아보려면 [알림 및 티켓 생성][2]을 참조하세요.
1. **보기 저장**을 클릭합니다.

## 사례 세부 정보

{{< img src="/service_management/case_management/view_and_manage/case_details_overview.png" alt="에스컬레이션된 예시 사례의 세부 정보 보기" style="width:100%;" >}}

사례 세부 정보 페이지는 조사가 어떻게 진행되고 있는지를 알려주는 단일 정보 소스 역할을 합니다. 각 사례는 다음과 같은 속성을 갖습니다.

상태
: 모든 사례는 생성 시 기본적으로 오픈 상태입니다. 사례를 진행하면서 상태를 진행 중 및 종료로 변경할 수 있습니다. `S`를 입력하여 사례의 상태를 변경합니다.

우선 순위
: 우선 순위는 기본값으로 정의되어 있지 않습니다. 사례의 우선순위를 P1 - 중요, P2 - 높음, P3 - 중간, P4 - 낮음, P5 - 정보로 설정할 수 있습니다. `P`를 입력하여 사례의 우선 순위를 설정합니다. 

피할당자
: 기본으로 할당되지 않습니다. 사용자에게 할당하려면 `A` 을 입력합니다. 자신에게 할당하려면 `I`을 입력합니다. 

속성
: 속성을 추가하면 구조화 및 필터링이 가능합니다. 모든 사례는 기본값으로 팀, 데이터 센터, 서비스, 환경, 버전 등의 속성을 갖추고 있습니다.

아카이빙
: 사례를 아카이빙하면 검색에서 삭제됩니다. `E`를 입력하여 사례를 아카이빙합니다.

활동 타임라인
: 각 사례는 자동으로 활동 타임라인을 생성하여 상태, 피할당자, 우선 순위, 신호 및 추가된 코멘트에 대한 실시간 업데이트를 캡처합니다. 코멘트에 태그가 지정되면 이메일을 받습니다. 코멘트를 추가하려면 `M`, 보내려면 `Cmd + Enter`을 입력하세요.

## 조치 취하기

사례 관리 기능으로 정보, 컨텍스트 및 리소스를 수집하여 취해야 할 적절한 조치를 결정하세요. 해당 조치에는 추가 조사, 인시던트로 에스컬레이션 또는 사례 종결이 포함됩니다.

개별 사례:
- [조사 노트북 생성][3]: 조사 정보를 수집하고 팀 구성원과 협력하세요.
- [인시던트 선언][4]을 클릭합니다: 케이스를 인시던트로 에스컬레이션하고 인시던트 대응 프로세스를 시작하세요.
- 수동으로 Jira 이슈를 만듭니다: `Shift + J`를 사용하여 Jira 이슈를 생성합니다. 자동 Jira 이슈 생성 및 양방향 동기화를 설정하는 방법에 대한 자세한 내용은 [설정][5] 설명서를 참조하세요. 
- 수동으로 ServiceNow 인시던트 생성: `Shift + N`을 사용하여 ServiceNow 인시던트를 생성합니다.
- [CoScreen 회의][6]: 협력 디버깅을 위한 화면 공유
- 사례 종료: 더 이상의 조치가 필요하지 않음을 팀에 알립니다. 사례 상태를 종결로 업데이트합니다.

## 사례 분석

{{< img src="/service_management/case_management/view_and_manage/view_and_manage_case_analytics.png" alt="데이터 소스로 선택한 사례 옵션을 표시하는 그래프 편집기" style="width:100%;" >}}

Case Analytics는 집계된 케이스 통계에 대한 쿼리 가능한 데이터 소스입니다. [대시보드][7] 및 [노트북][3]의 다양한 그래프 위젯에서 이러한 분석을 통해 팀 생산성을 분석하고 이슈의 패턴을 파악할 수 있습니다. 

다음 위젯은 시계열, 상위 목록, 쿼리 값, 테이블, 트리 맵, 파이 차트, 변경 및 목록과 같은 사례 분석을 지원합니다. 

## 참고 자료

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://app.datadoghq.com/cases
[2]: /ko/service_management/case_management/create_notifications_and_third_party_tickets
[3]: /ko/notebooks/
[4]: /ko/service_management/incident_management/#describing-the-incident
[5]: /ko/service_management/case_management/settings/#jira
[6]: /ko/coscreen/
[7]: https://docs.datadoghq.com/ko/dashboards/