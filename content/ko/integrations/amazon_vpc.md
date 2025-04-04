---
aliases: []
categories:
- aws
- cloud
- 로그 수집
- 네트워크
custom_kind: integration
dependencies: []
description: Amazon VPC 로그를 수집합니다.
doc_link: https://docs.datadoghq.com/integrations/amazon_vpc/
draft: false
further_reading:
- link: https://www.datadoghq.com/blog/vpc-security-flowlogs/
  tag: 블로그
  text: Datadog으로 플로우 로그를 모니터링하여 VPC 보안을 유지하세요.
- link: https://www.datadoghq.com/architecture/connect-to-Datadog-over-AWS-privatelink-using-AWS-vpc-peering/
  tag: 아키텍처 센터
  text: AWS VPC 피어링을 사용하여 AWS PrivateLink를 통해 Datadog 연결
git_integration_title: amazon_vpc
has_logo: false
integration_id: ''
integration_title: Amazon VPC
integration_version: ''
is_public: true
manifest_version: '1.0'
name: amazon_vpc
public_title: Datadog-Amazon VPC 통합
short_description: Amazon VPC 로그를 수집합니다.
version: '1.0'
---

<!--  SOURCED FROM https://github.com/DataDog/dogweb -->
## 개요

Amazon Virtual Private Cloud(Amazon VPC)로 가상 네트워크에서 AWS 리소스를 구동할 수 있습니다. VPC 플로우 로그는 VPC의 네트워크 인터페이스를 오가는 IP 트래픽에 대한 정보를 캡처할 수 있는 기능입니다.

## 설정

### 설치

아직 설정하지 않은 경우 먼저 [Amazon Web Services 통합][1]을 설정하세요.

### 메트릭 수집

논-`aws.vpc.flowlogs.*` Amazon VPC을 수집하는데 추가 단계가 필요하지 않습니다. `aws.vpc.flowlogs.*` 접두사가 붙은 메트릭은  [Datadog VPC 플로우 로그][2] 통합으로 생성됩니다. 플로우 로그 메트릭을 수집하려면 하단의 [로그 수집](#log-collection) 섹션을 확인하세요. 

`aws.vpc.subnet.*` 메트릭의 경우:
   1. [Amazon EC2][3] 통합이 설치되어 있는지 다시 확인하고 [AWS 통합 페이지][4]에서 **Metric Collection** 탭 아래에 EC2 메트릭 수집이 활성화되어 있는지 확인하세요.
   2. [Datadog 고객지원팀][5]에 문의해 내 계정에서 수집을 활성화합니다.

### 로그 수집


#### AWS에서 VPC 플로우 로그용 대상 리소스를 탐색 또는 생성하세요.

Datadog에 전송하기 전 먼저 VPC 흐름 로그를 중간 대상에 전송해야 합니다. Kinesis Data Firehose로 직접 전송하거나 S3 버킷 또는 클라우드와치(CloudWatch) 로그 그룹에 저장할 수 있습니다.

Amazon Data Firehose는 운영 오버헤드가 적고 비용 효율적이므로 VPC 흐름 로그를 Datadog으로 보내는 데 권장되는 옵션입니다. 자세한 내용을 확인하려면 [Kinesis Data Firehose에 Amazon VPC 흐름 로그 적용하기][6]를 참조하세요.

1. 새 항목을 만들거나 기존 항목을 선택합니다.
   - Amazon Data Firehose(권장). Datadog으로 로그를 전송하기 위한 기존 Amazon Data Firehose 전송 스트림이 아직 없는 경우 [Datadog Amazon Firehose 대상으로 AWS 서비스 로그 보내기][7] 가이드의 지침에 따라 스트림을 생성하세요. **참고:** 중앙 집중식 로그 수집 및 전송을 위해 필요한 경우 VPC와 별도의 다른 AWS 계정에서 전송 스트림을 선택할 수 있습니다.
   - S3 버킷 또는 폴더 경로.
   - 클라우드와치(CloudWatch) 로그 그룹.

**참고**: S3 경로나 클라우드와치(CloudWatch) 로그 그룹 이름의 접두사로 `vpc`를 지정하면 Lambda가 로그의 `vpc` 소스를 자동 태깅합니다.


#### VPC 흐름 로그 로깅 활성화

1. AWS 콘솔에서 모니터링하려는 VPC로 이동합니다.
2. **Flow logs** 탭으로 이동합니다. 
3. **Create flow log**를 클릭합니다.
4. `All` 필터를 선택하면 승인된 연결과 거부된 연결을 모두 확인할 수 있습니다.
5. 로그에 대해 원하는 대상 유형(Amazon Data Firehose, S3 버킷 또는 CloudWatch 로그 그룹)을 선택합니다.
6. 대상 리소스 세부 정보를 입력합니다.
7. **Create flow log**를 클릭합니다.

#### Datadog로 로그 전송

Amazon Data Firehose를 대상으로 선택했다면 모든 작업이 완료된 것입니다.

S3 버킷 또는 클라우드와치(CloudWatch) 로그 그룹을 대상으로 선택한 경우:

1. 이미 하지 않은 경우 AWS 계정에서 [Datadog 포워더(Forwarder) 람다 함수][8]를 설정하세요.
2. 설정한 후에는 Datadog Forwarder Lambda 함수로 이동하세요. Function Overview 섹션에서 **Add Trigger**를 클릭합니다.
3. **S3** 또는 **CloudWatch Logs** 트리거를 선택하여 트리거를 설정합니다.
4. VPC 로그가 포함된 S3 버킷 또는 클라우드와치(CloudWatch) 로그 그룹을 선택합니다.
5. S3의 경우 이벤트 유형은 `All object create events`로 둡니다. 
6. **Add**를 클릭해 Lambda에 트리거를 추가합니다.

 [Log Explorer][9]로 이동해 로그를 탐색합니다.

AWS 서비스 로그 수집에 대한 자세한 정보는 [Datadog Lambda 함수를 사용해 AWS 서비스 로그 전송][10]을 참고하세요.

## 수집한 데이터

### 메트릭
{{< get-metrics-from-git "amazon_vpc" >}}


AWS에서 검색된 각 메트릭에는 AWS 콘솔에 나타나는 것과 동일한 태그가 할당됩니다, 호스트 이름, 보안 그룹 등을 포함하되 이에 국한되지 않습니다.

### 이벤트

Amazon VPC 통합은 이벤트를 포함하지 않습니다.

### 서비스 점검

Amazon VPC 통합은 서비스 점검을 포함하지 않습니다.

## 트러블슈팅

도움이 필요하신가요? [Datadog 지원팀][5]에 문의하세요.

## 참고 자료

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://docs.datadoghq.com/ko/integrations/amazon_web_services/
[2]: https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:464622532012:applications~Datadog-VPC-Flow-Logs
[3]: https://docs.datadoghq.com/ko/integrations/amazon_ec2/
[4]: https://app.datadoghq.com/integrations/amazon-web-services
[5]: https://docs.datadoghq.com/ko/help/
[6]: https://aws.amazon.com/blogs/networking-and-content-delivery/introducing-amazon-vpc-flow-logs-kinesis-data-firehose/
[7]: https://docs.datadoghq.com/ko/logs/guide/send-aws-services-logs-with-the-datadog-kinesis-firehose-destination/
[8]: https://docs.datadoghq.com/ko/logs/guide/forwarder/
[9]: https://docs.datadoghq.com/ko/logs/explorer/
[10]: https://docs.datadoghq.com/ko/logs/guide/send-aws-services-logs-with-the-datadog-lambda-function/
[11]: https://github.com/DataDog/dogweb/blob/prod/integration/amazon_vpc/amazon_vpc_metadata.csv