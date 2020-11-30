### 01. 외부 접속 허용
만일 회사나 집 등 외부에서 접속하기 위해서는 public ip를 접속 가능하도록 변경 합니다  

Azure SQL Database는 외부 접속을 서버수준과 데이터베이스 수준에서 허용할 수 있습니다  

![azsqlfirewall](https://docs.microsoft.com/ko-kr/azure/azure-sql/database/media/firewall-configure/sqldb-firewall-1.png)

#### 서버 수준 IP 방화벽 규칙
서버에서 관리되는 모든 데이터베이스에 접근을 허용 합니다  
Azure Portal,Azure CLI,Azure Powershell 및 SSMS 등 데이터베이스에서 허용할 수 있습니다  

```powershell
# 아이피 수정 필요
$ruleName="allowmyip"
$ipAddress="*0.0.0.0"

az sql server firewall-rule create -g $resourceGroup -s $serverName -n $ruleName --start-ip-address $ipAddress --end-ip-address $ipAddress
```

SSMS에서 실행 (방화벽 추가)  
```sql
-- 아이피 수정 필요
EXECUTE sp_set_firewall_rule N'allowmyip', '*0.0.0.0', '*0.0.0.0';  
```

SSMS에서 방화벽 확인
```sql
select * from sys.firewall_rules;
```

SSMS에서 실행 (방화벽 제거)  
```sql
EXEC sp_delete_firewall_rule N'allowmyip'
```

#### 데이터베이스 수준 IP 방화벽 규칙
특정 데이터베이스에 접속을 허용 합니다
Azure SQL 에서 SSMS로 허용 합니다

SSMS에서 실행 (방화벽 추가)  
```sql
-- 아이피 수정 필요
EXECUTE sp_set_database_firewall_rule N'allowmyip', '*0.0.0.0', '*0.0.0.0';  
```

SSMS에서 확인
```sql
select * from sys.database_firewall_rules;
```

SSMS에서 실행 (방화벽 제거)  
```sql
EXEC sp_delete_database_firewall_rule N'allowmyip'
```

### 02. 방화벽 및 Service Endpoint 추가
기존 생성한 VM 에서 Azure SQL로 접속하기 위해서는 VM에 속한 VNet - Subnet에서 Service endpoint를 추가한 뒤 해당 서브넷을 허용 하여야 합니다  
Subnet에서 Service endpoint 추가 시 해당 VNet이 잠시동안 가동 중지될 수 있으니 주의하여야 합니다  
  
만일 당장 VNet이 잠시 중단 되는 것을 원하지 않는다면 IgnoreMissingVNetServiceEndpoint 플래그를 활성화하여 
Service endpoint를 무시하여 VNet이 가동 중지 되는 것을 방지한 후 가능한 시점에 Service Endpoint를 추가할 수 있습니다  

![azsqlserviceendpoint](https://docs.microsoft.com/ko-kr/azure/virtual-network/media/virtual-network-service-endpoints-overview/vnet_service_endpoints_overview.png)
```powershell
$vnetName="vnet-adstest"
$subnetName="subnet-adstest"
$ruleName="allow-adstest"

# 기존 서브넷에 Service endpoint를 추가 합니다
az network service-endpoint policy create --name "Microsoft.SQL" --resource-group $resourceGroup

# Azure SQL에 해당 서브넷을 허용 합니다
az sql server vnet-rule create --server $serverName --name $ruleName -g $resourceGroup --subnet $subnetName --vnet-name $vnetName --ignore-missing-endpoint true
```

### 03. Private Link for Azure SQL Database
서비스 엔드 포인트에는 몇 가지 제한이나 단점이 있습니다  
서비스 엔드 포인트에 대한 트래픽이 여전히 가상 네트워크 외부로 나가며 Azure PaaS 리소스가 여전히 해당 공용 주소에서 액세스되고 있습니다  
VPN 또는 Express Route와 같은 연결로 Azure Virtual Network를 통해 들어오는 트래픽에만 사용할 수 있습니다  
온 프레미스 리소스에 대한 액세스를 허용하려면 해당 퍼블릭 IP도 허용해야합니다  

![azsqlprivatelink](https://docs.microsoft.com/ko-kr/azure/azure-sql/database/media/quickstart-create-single-database/pe-connect-overview.png)


### 04. Service Endpoint vs Private Link

Service Endpoint
- VNET에 PaaS resource 접근에 대한 권한을 위임
- PIP


Private Link
- Service에 대한 트래픽이 항상 VNET내에 있음
