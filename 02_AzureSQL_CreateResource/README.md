## 01. Azure SQL Database 생성 (General purpose - 2vCore)
```powershell
# 파라미터 앞에 *이 붙은 항목은 필수 변경

$resourceGroup="rg-adstest"
$location="koreacentral"
$serverName="*myservername"
$dbName="*myDbname"
$userName="*myUsername"
$password="*myPassword"
$collation="Korean_Wansung_CI_AS"


az sql server create -l $location -g $resourceGroup -n $serverName -u $userName -p $password
az sql db create -g $resourceGroup -s $serverName -n $dbName --collation $collation --sample-name AdventureWorksLT -e GeneralPurpose -f Gen4 -c 2
```

![azsqlgp](https://docs.microsoft.com/ko-kr/azure/azure-sql/database/media/high-availability-sla/general-purpose-service-tier.png)


### 02. Azure SQL Database 생성 (Business critical - 2vCore)
```powershell
$location="koreacentral"
# 기존 General purpose와 다른 변수 입력
$bcServerName="*myservername"
$bcDbName="*mydbname"

az sql server create -l $location -g $resourceGroup -n $bcServerName -u $userName -p $password
az sql db create -g $resourceGroup -s $bcServerName -n $bcDbName --collation $collation --sample-name AdventureWorksLT -e BusinessCritical  -f Gen5 -c 2 
```

![azsqlbc](https://docs.microsoft.com/ko-kr/azure/azure-sql/database/media/high-availability-sla/business-critical-service-tier.png)

