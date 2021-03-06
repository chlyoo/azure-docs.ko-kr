---
title: IP 방화벽 규칙
description: Azure SQL Database 또는 Azure Synapse Analytics 방화벽에서 데이터베이스에 대 한 서버 수준 IP 방화벽 규칙을 구성 합니다. SQL Database에 대 한 액세스를 관리 하 고 데이터베이스 수준 IP 방화벽 규칙을 구성 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: bbad7dcaa1d92df4969c88e4ba86a62987509e39
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632802"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database 및 Azure Synapse IP 방화벽 규칙
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

예를 들어 이름이 *mysqlserver* 인 Azure Synapse Analytics Azure SQL Database에서 새 서버를 만들 경우 서버 수준 방화벽은 서버에 대 한 공용 끝점 ( *mysqlserver.database.windows.net* 에서 액세스할 수 있음)에 대 한 모든 액세스를 차단 합니다. 간단히 하기 위해 *SQL Database* 를 사용 하 여 SQL Database 및 Azure Synapse Analytics를 모두 참조 합니다.

> [!IMPORTANT]
> 이 문서는 *Azure SQL Managed Instance* 에 적용되지 *않습니다*. 네트워크 구성에 대 한 자세한 내용은 [AZURE SQL Managed Instance에 응용 프로그램 연결](../managed-instance/connect-application-instance.md)을 참조 하세요.
>
> Azure Synapse는 서버 수준 IP 방화벽 규칙만 지원 합니다. 데이터베이스 수준 IP 방화벽 규칙은 지원 하지 않습니다.

## <a name="how-the-firewall-works"></a>방화벽 작동 방법

다음 다이어그램과 같이 인터넷 및 Azure의 연결 시도가 서버 또는 데이터베이스에 도달 하기 전에 방화벽을 통과 해야 합니다.

   ![방화벽 구성 다이어그램][1]

### <a name="server-level-ip-firewall-rules"></a>서버 수준 IP 방화벽 규칙

이러한 규칙을 통해 클라이언트는 전체 서버, 즉 서버에서 관리하는 모든 데이터베이스에 액세스할 수 있습니다. 규칙은 *master* 데이터베이스에 저장 됩니다. 서버에 대해 최대 128개의 서버 수준 IP 방화벽 규칙을 사용할 수 있습니다. **Azure 서비스 및 리소스에서이 서버에 액세스 하도록 허용** 설정을 사용 하도록 설정 하면 서버에 대 한 단일 방화벽 규칙으로 계산 됩니다.
  
Azure Portal, PowerShell 또는 Transact-sql 문을 사용 하 여 서버 수준 IP 방화벽 규칙을 구성할 수 있습니다.

- 포털 또는 PowerShell을 사용 하려면 구독 소유자 또는 구독 참가자 여야 합니다.
- Transact-sql을 사용 하려면 서버 수준 보안 주체 로그인 또는 Azure Active Directory 관리자로 *master* 데이터베이스에 연결 해야 합니다. 먼저 Azure 수준 사용 권한이 있는 사용자가 서버 수준 IP 방화벽 규칙을 만들어야 합니다.

> [!NOTE]
> 기본적으로 Azure Portal에서 새 논리 SQL server를 만드는 동안 **이 서버에 액세스할 수 있는 Azure 서비스 및 리소스 설정이** **아니요** 로 설정 됩니다.

### <a name="database-level-ip-firewall-rules"></a>데이터베이스 수준 IP 방화벽 규칙

데이터베이스 수준 IP 방화벽 규칙을 사용 하면 클라이언트가 특정 (보안) 데이터베이스에 액세스할 수 있습니다. 각 데이터베이스 ( *master* 데이터베이스 포함)에 대 한 규칙을 만들어 개별 데이터베이스에 저장 합니다.
  
- 첫 번째 서버 수준 방화벽을 구성한 후에만 Transact-sql 문을 사용 하 여 master 및 사용자 데이터베이스에 대 한 데이터베이스 수준 IP 방화벽 규칙을 만들고 관리할 수 있습니다.
- 서버 수준 IP 방화벽 규칙의 범위를 벗어나는 데이터베이스 수준 IP 방화벽 규칙의 IP 주소 범위를 지정 하면 데이터베이스 수준 범위에서 IP 주소가 있는 클라이언트만 데이터베이스에 액세스할 수 있습니다.
- 데이터베이스에 대해 최대 128개의 데이터베이스 수준 IP 방화벽 규칙을 가질 수 있습니다. 데이터베이스 수준 IP 방화벽 규칙을 구성 하는 방법에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 예제를 참조 하 고 [sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)를 참조 하세요.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>방화벽 규칙을 설정 하는 방법에 대 한 권장 사항

가능 하면 항상 데이터베이스 수준 IP 방화벽 규칙을 사용 하는 것이 좋습니다. 이 방법을 사용하면 보안을 강화하고 데이터베이스의 이식성을 높일 수 있습니다. 관리자에 대해 서버 수준 IP 방화벽 규칙을 사용 합니다. 동일한 액세스 요구 사항을 가진 데이터베이스가 많고 각 데이터베이스를 개별적으로 구성 하지 않으려는 경우에도이를 사용 합니다.

> [!NOTE]
> 비즈니스 연속성의 맥락에서 휴대용 데이터베이스에 대한 자세한 내용은 [재해 복구를 위한 인증 요구 사항](active-geo-replication-security-configure.md)을 참조하세요.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>서버 수준 및 데이터베이스 수준 IP 방화벽 규칙 비교

*한 데이터베이스의 사용자가 다른 데이터베이스에서 완전히 분리되어야 하나요?*

그렇다면 *데이터베이스* 수준 IP 방화벽 규칙을 사용 하 여 액세스 권한을 부여 합니다. 이 방법은 모든 데이터베이스에 대 한 방화벽을 통한 액세스를 허용 하는 서버 수준 IP 방화벽 규칙을 사용 하지 않습니다. 그러면 방어 수준이 줄어듭니다.

*IP 주소에서 사용자가 모든 데이터베이스에 액세스 해야 하나요?*

그렇다면 *서버* 수준 ip 방화벽 규칙을 사용 하 여 ip 방화벽 규칙을 구성 해야 하는 횟수를 줄입니다.

*IP 방화벽 규칙을 구성 하는 개인 이나 팀은 Azure Portal, PowerShell 또는 REST API을 통해서만 액세스할 수 있나요?*

이 경우 서버 수준 IP 방화벽 규칙을 사용 해야 합니다. 데이터베이스 수준 IP 방화벽 규칙은 Transact-sql을 통해서만 구성할 수 있습니다.  

*IP 방화벽 규칙을 구성 하는 개인 이나 팀이 데이터베이스 수준에서 높은 수준의 사용 권한을 갖지 못하도록 허용 되나요?*

그렇다면 서버 수준 IP 방화벽 규칙을 사용 합니다. Transact-sql을 통해 데이터베이스 수준 IP 방화벽 규칙을 구성 하려면 데이터베이스 수준에서 최소 *제어 데이터베이스* 권한이 있어야 합니다.  

*IP 방화벽 규칙을 구성 하거나 감사 하는 개인 이나 팀이 수천 개의 데이터베이스에 대 한 IP 방화벽 규칙을 중앙에서 관리 하나요?*

이 시나리오에서 모범 사례는 요구 사항 및 환경에 따라 결정 됩니다. 서버 수준 IP 방화벽 규칙이 구성하기 더 쉬울 수 있지만 스크립팅은 데이터베이스 수준에서 규칙을 구성할 수 있습니다. 서버 수준 ip 방화벽 규칙을 사용 하는 경우에도 데이터베이스에 대 한 *CONTROL* 권한이 있는 사용자가 데이터베이스 수준 ip 방화벽 규칙을 만들 수 있는지 확인 하기 위해 데이터베이스 수준 ip 방화벽 규칙을 감사 해야 할 수 있습니다.

*서버 수준 및 데이터베이스 수준 IP 방화벽 규칙을 함께 사용할 수 있나요?*

예. 관리자와 같은 일부 사용자에 게는 서버 수준 IP 방화벽 규칙이 필요할 수 있습니다. 데이터베이스 애플리케이션 사용자와 같은 경우는 데이터베이스 수준 IP 방화벽 규칙이 필요할 수 있습니다.

### <a name="connections-from-the-internet"></a>인터넷에서 연결

컴퓨터에서 인터넷을 통해 서버에 연결 하려고 하면 먼저 방화벽은 연결에서 요청 하는 데이터베이스에 대 한 데이터베이스 수준 IP 방화벽 규칙에 대해 요청의 원래 IP 주소를 확인 합니다.

- 주소가 데이터베이스 수준 IP 방화벽 규칙에 지정 된 범위 내에 있는 경우 해당 규칙을 포함 하는 데이터베이스에 대 한 연결이 부여 됩니다.
- 주소가 데이터베이스 수준 IP 방화벽 규칙의 범위 내에 없는 경우 방화벽은 서버 수준 IP 방화벽 규칙을 확인 합니다. 주소가 서버 수준 IP 방화벽 규칙의 범위 내에 있는 경우 연결이 허용 됩니다. 서버 수준 IP 방화벽 규칙은 서버에서 관리 하는 모든 데이터베이스에 적용 됩니다.  
- 주소가 데이터베이스 수준 또는 서버 수준 IP 방화벽 규칙의 범위에 포함 되지 않은 경우 연결 요청이 실패 합니다.

> [!NOTE]
> 로컬 컴퓨터에서 Azure SQL Database에 액세스 하려면 네트워크와 로컬 컴퓨터의 방화벽이 TCP 포트 1433에서 나가는 통신을 허용 하는지 확인 합니다.

### <a name="connections-from-inside-azure"></a>Azure 내부에서 연결

Azure 내에서 호스트 되는 응용 프로그램이 SQL server에 연결할 수 있도록 하려면 Azure 연결을 사용 하도록 설정 해야 합니다. Azure 연결을 사용 하도록 설정 하려면 시작 및 끝 IP 주소가 0.0.0.0으로 설정 된 방화벽 규칙이 있어야 합니다.

Azure의 응용 프로그램이 서버에 연결 하려고 하면 방화벽은이 방화벽 규칙이 있는지 확인 하 여 Azure 연결이 허용 되는지 확인 합니다. **방화벽 및 가상 네트워크** **설정에서이** 서버에 액세스할 수 있는 **Azure 서비스 및 리소스 허용** 을 전환 하 여 Azure Portal 블레이드에서 직접 설정할 수 있습니다. ON으로 설정 하면 이름이 **AllowAllWindowsIP** 인 IP 0.0.0.0-0.0.0.0에 대 한 인바운드 방화벽 규칙이 생성 됩니다. 포털을 사용 하지 않는 경우 PowerShell 또는 Azure CLI를 사용 하 여 시작 및 끝 IP 주소가 0.0.0.0으로 설정 된 방화벽 규칙을 만듭니다. 

> [!IMPORTANT]
> 이 옵션은 다른 고객의 구독에서 연결을 포함 하 여 Azure의 모든 연결을 허용 하도록 방화벽을 구성 합니다. 이 옵션을 선택 하는 경우 로그인 및 사용자 권한이 권한 있는 사용자만 액세스할 수 있도록 제한 해야 합니다.

## <a name="permissions"></a>사용 권한

Azure SQL Server에 대한 IP 방화벽 규칙을 만들고 관리하려면 다음 중 하나에서 수행할 수 있습니다.

- [SQL Server 참가자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할의
- [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할에서
- Azure SQL Server을 포함 하는 리소스의 소유자입니다.

## <a name="create-and-manage-ip-firewall-rules"></a>IP 방화벽 규칙 만들기 및 관리

[Azure Portal](https://portal.azure.com/) 를 사용 하거나 [Azure PowerShell](/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule)또는 Azure [REST API](/rest/api/sql/firewallrules/createorupdate)를 사용 하 여 프로그래밍 방식으로 첫 번째 서버 수준 방화벽 설정을 만듭니다. 이러한 메서드 또는 Transact-sql을 사용 하 여 추가 서버 수준 IP 방화벽 규칙을 만들고 관리 합니다.

> [!IMPORTANT]
> Transact-sql을 사용 하 여 데이터베이스 수준 IP 방화벽 규칙을 만들고 관리할 수 있습니다.

성능 향상을 위해 서버 수준 IP 방화벽 규칙이 데이터베이스 수준에서 일시적으로 캐시됩니다. 캐시를 새로 고치려면 [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)를 참조하세요.

> [!TIP]
> [데이터베이스 감사](../../azure-sql/database/auditing-overview.md) 를 사용 하 여 서버 수준 및 데이터베이스 수준 방화벽 변경을 감사할 수 있습니다.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Azure Portal를 사용 하 여 서버 수준 IP 방화벽 규칙 관리

Azure Portal에서 서버 수준 IP 방화벽 규칙을 설정 하려면 데이터베이스 또는 서버에 대 한 개요 페이지로 이동 합니다.

> [!TIP]
> 자습서는 [Azure Portal 사용 하 여 데이터베이스 만들기](single-database-create-quickstart.md)를 참조 하세요.

#### <a name="from-the-database-overview-page"></a>데이터베이스 개요 페이지에서

1. 데이터베이스 개요 페이지에서 서버 수준 IP 방화벽 규칙을 설정 하려면 다음 그림과 같이 도구 모음에서 **서버 방화벽 설정** 을 선택 합니다.

    ![서버 IP 방화벽 규칙](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    서버에 대한 **방화벽 설정** 페이지가 열립니다.

2. 도구 모음에서 **클라이언트 Ip 추가** 를 선택 하 여 사용 중인 컴퓨터의 IP 주소를 추가 하 고 **저장** 을 선택 합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

    ![서버 수준 IP 방화벽 규칙 설정](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>서버 개요 페이지에서

서버에 대 한 개요 페이지가 열립니다. 정규화 된 서버 이름 (예: *mynewserver20170403.database.windows.net*)을 표시 하 고 추가 구성을 위한 옵션을 제공 합니다.

1. 이 페이지에서 서버 수준 규칙을 설정 하려면 왼쪽의 **설정** 메뉴에서 **방화벽** 을 선택 합니다.

2. 도구 모음에서 **클라이언트 Ip 추가** 를 선택 하 여 사용 중인 컴퓨터의 IP 주소를 추가 하 고 **저장** 을 선택 합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-sql을 사용 하 여 IP 방화벽 규칙 관리

| 카탈로그 뷰 또는 저장 프로시저 | Level | 설명 |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |데이터베이스 |현재 데이터베이스 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 제거합니다. |

다음 예에서는 기존 규칙을 검토 하 고, *Contoso* 서버에서 ip 주소 범위를 사용 하도록 설정 하 고, ip 방화벽 규칙을 삭제 합니다.

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

다음으로 서버 수준 IP 방화벽 규칙을 추가합니다.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

서버 수준 IP 방화벽 규칙을 삭제 하려면 *sp_delete_firewall_rule* 저장 프로시저를 실행 합니다. 다음 예에서는 *에서는 contosofirewallrule* 규칙을 삭제 합니다.

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>PowerShell을 사용 하 여 서버 수준 IP 방화벽 규칙 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL Database에서 계속 지원 되지만 이제 Az. Sql 모듈에 대 한 모든 개발이 지원 됩니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 및 AzureRm 모듈의 명령에 대 한 인수는 실질적으로 동일 합니다.

| Cmdlet | Level | 설명 |
| --- | --- | --- |
| [AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |서버 |현재 서버 수준 방화벽 규칙 반환 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |서버 |새 서버 수준 방화벽 규칙 만들기 |
| [AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |서버 |기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |서버 |서버 수준 방화벽 규칙 제거 |

다음 예제에서는 PowerShell을 사용 하 여 서버 수준 IP 방화벽 규칙을 설정 합니다.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> $Servername에 대해 정규화 된 DNS 이름이 아닌 서버 이름을 지정 합니다. 예를 들어 **mysqldbserver.database.windows.net** 대신 **mysqldbserver** 를 지정 합니다.
>
> 빠른 시작의 컨텍스트에서 PowerShell 예제를 보려면 PowerShell을 사용 하 여 [DB 만들기-powershell](powershell-script-content-guide.md) 및 [단일 데이터베이스 만들기 및 서버 수준 IP 방화벽 규칙 구성](scripts/create-and-configure-database-powershell.md)을 참조 하세요.

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI를 사용 하 여 서버 수준 IP 방화벽 규칙 관리

| Cmdlet | Level | 설명 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|서버|서버 IP 방화벽 규칙을 만듭니다.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|서버|서버의 IP 방화벽 규칙을 나열합니다.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|서버|IP 방화벽 규칙의 세부 정보를 표시 합니다.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|서버|IP 방화벽 규칙을 업데이트 합니다.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|서버|IP 방화벽 규칙을 삭제 합니다.|

다음 예제에서는 CLI를 사용 하 여 서버 수준 IP 방화벽 규칙을 설정 합니다.

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> $Servername에 대해 정규화 된 DNS 이름이 아닌 서버 이름을 지정 합니다. 예를 들어 **mysqldbserver.database.windows.net** 대신 **mysqldbserver** 를 지정 합니다.
>
> 빠른 시작의 컨텍스트에서 CLI 예제는 Azure CLI를 사용 하 여 [DB Azure CLI 만들기](az-cli-script-samples-content-guide.md) 및 [단일 데이터베이스 만들기 및 서버 수준 IP 방화벽 규칙 구성](scripts/create-and-configure-database-cli.md)을 참조 하세요.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>REST API를 사용 하 여 서버 수준 IP 방화벽 규칙 관리

| API | Level | 설명 |
| --- | --- | --- |
| [방화벽 규칙 나열](/rest/api/sql/firewallrules/listbyserver) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [방화벽 규칙 만들기 또는 업데이트](/rest/api/sql/firewallrules/createorupdate) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [방화벽 규칙 삭제](/rest/api/sql/firewallrules/delete) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [방화벽 규칙 가져오기](/rest/api/sql/firewallrules/get) | 서버 | 서버 수준 IP 방화벽 규칙을 가져옵니다. |

## <a name="troubleshoot-the-database-firewall"></a>데이터베이스 방화벽 문제 해결

Azure SQL Database에 대 한 액세스가 예상과 다르게 작동 하는 경우 다음 사항을 고려 하세요.

- **로컬 방화벽 구성:**

  컴퓨터가 Azure SQL Database에 액세스하려면 먼저 컴퓨터에서 TCP 포트 1433에 대한 방화벽 예외를 만들어야 하는 경우가 있습니다. Azure 클라우드 경계 내에서 연결을 만들려면 추가 포트를 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 Azure SQL Database에 대해 1433를 초과](adonet-v12-develop-direct-route-ports.md)하는 포트의 "SQL Database: 외부 vs 내부" 섹션을 참조 하세요.

- **네트워크 주소 변환:**

  NAT (network address translation)로 인해 컴퓨터에서 Azure SQL Database에 연결 하는 데 사용 하는 IP 주소가 컴퓨터의 IP 구성 설정에 있는 IP 주소와 다를 수 있습니다. 컴퓨터에서 Azure에 연결 하는 데 사용 하는 IP 주소를 보려면 다음을 수행 합니다.
    1. 포털에 로그인합니다.
    1. 데이터베이스를 호스트 하는 서버에서 **구성** 탭으로 이동 합니다.
    1. **현재 클라이언트 Ip 주소** 는 **허용 된 ip 주소** 섹션에 표시 됩니다. **허용 된 IP 주소** 에 대해 **추가** 를 선택 하 여이 컴퓨터에서 서버에 액세스할 수 있도록 합니다.

- **허용 목록에 대 한 변경 내용이 아직 적용 되지 않았습니다.**

  Azure SQL Database 방화벽 구성에 대 한 변경 내용이 적용 되려면 최대 5 분 정도 걸릴 수 있습니다.

- **로그인에 권한이 없거나 잘못 된 암호가 사용 되었습니다.**

  로그인에 서버에 대 한 사용 권한이 없거나 암호가 잘못 된 경우 서버에 대 한 연결이 거부 됩니다. 방화벽 설정을 만들면 클라이언트에서 서버에 연결할 수 있는 *기회가* 제공 됩니다. 클라이언트는 여전히 필요한 보안 자격 증명을 제공 해야 합니다. 로그인 준비에 대 한 자세한 내용은 [데이터베이스 액세스 제어 및 권한 부여](logins-create-manage.md)를 참조 하세요.

- **동적 IP 주소:**

  동적 IP 주소 지정을 사용 하는 인터넷 연결이 있고 방화벽을 통과 하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도해 보세요.
  
  - 인터넷 서비스 공급자에 게 서버에 액세스 하는 클라이언트 컴퓨터에 할당 된 IP 주소 범위를 요청 합니다. Ip 주소 범위를 IP 방화벽 규칙으로 추가 합니다.
  - 클라이언트 컴퓨터에 대 한 고정 IP 주소 지정을 대신 가져옵니다. Ip 주소를 IP 방화벽 규칙으로 추가 합니다.

## <a name="next-steps"></a>다음 단계

- 회사 네트워크 환경에서 Azure 데이터 센터에서 사용 하는 계산 IP 주소 범위 (SQL 범위 포함)에서 인바운드 통신을 허용 하는지 확인 합니다. 이러한 IP 주소를 허용 목록에 추가 해야 할 수도 있습니다. [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요.  
- [Azure SQL Database에서 단일 데이터베이스를 만드는](single-database-create-quickstart.md)방법에 대 한 빠른 시작을 참조 하세요.
- 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL Database의 데이터베이스에 연결 하는 방법에 대 한 도움말은 [클라이언트 빠른 시작 코드 샘플](connect-query-content-reference-guide.md#libraries)을 사용 하 여 Azure SQL Database를 참조 하세요.
- 열어야 할 수 있는 추가 포트에 대 한 자세한 내용은 [ADO.NET 4.5 및 SQL Database에 대해 1433를 초과](adonet-v12-develop-direct-route-ports.md) 하는 포트의 "SQL Database: 외부 vs 내부" 섹션을 참조 하세요.
- Azure SQL Database 보안에 대 한 개요는 [데이터베이스 보안](security-overview.md)설정을 참조 하세요.

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
