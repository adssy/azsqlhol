
# Azure Monitoring
Azure SQL을 모니터링 하는 여러 방법이 있겠지만 여기에서는 Azure Portal을 통해 리소스량을 모니터링 하는 방법과 Audit Log를 Log Analytics를 통해 보는 방법 이 두가지에 대하여 확인해 봅니다.

## Azure Portal

Azure Portal을 통한 모니터링은 기본적인 Resource 모니터링 용도로 활용됩니다.
(CPU Percentage, Disk Space, Data IO, Log IO...)
Azure Portal에서 확인할 수 있습니다.

<img src = "./images/sqlservermetric01.png" width="60%">
1번의 metric을 선택하거나 2번영역을 선택하면 기본적인 내용의 메트릭을 확인할 수 있습니다.

## SQL Audit Log with Log Analytics

### 01. Log Analytics Space 생성
<img src = "./images/loganalytics01.png" width="60%">

```
1. log analytics를 검색한 뒤
2. Add를 클릭
```

<img src = "./images/loganalytics02.png" width="60%">

```
# Resource 정보 기입
subscription : 현재 테스트 진행중인 알맞은 구독 선택
resource group : 테스트대로 진행하셨다면 rg-adstest 리소스 그룹 선택
name : la-adstest
region : Korea Central

# Review + Create 누른 뒤 리소스 생성버튼 클릭.
```
### 02. Azure SQL Server Audit & Database Audit 설정
<img src = "./images/sqlserver01.png" width="60%">

```
이전에 생성해둔 SQL Server(Database 아님)를 클릭한 뒤
1. Security - Auditing 클릭
2. Auditing Off -> On으로 변경
3. Log Analytics 선택한 뒤 앞서 생성한 Log Analytics 를 선택 후 저장.
```

<img src = "./images/sqlserver02.png" width="60%">

```
이번엔 SSQL Database에 대한 Audit 설정을 할 차례 방금전 SQL Server 하위의 Database 중 하나를 클릭
1. Security - Auditing 클릭
2. Auditing Off -> On으로 변경
3. Log Analytics 선택한 뒤 앞서 생성한 Log Analytics를 선택 후 저장.
```

### 03. Log Analytics에 적재된 SQL Audit Log 보기

<img src = "./images/sqlserver03.png" width="60%">

```
1. 방금설정한 sql database에서 View audit logs 클릭
2. Log Analytics 선택
```
<img src = "./images/sqlserver04.png" width="60%">

```
초기 수집할때까지 몇분정도 기다려보시면
수집된 로그들이 위 화면과 같이 보이게 됩니다.
Log Analytics에 적재된 로그들은 Kusto Query를 통해 조회할 수 있으며
사용방법은 하위 url을 참조바랍니다.

Kusto Query : https://docs.microsoft.com/ko-kr/azure/data-explorer/kusto/query/

```

