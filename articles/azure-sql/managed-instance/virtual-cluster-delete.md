---
title: SQL Managed Instance를 삭제 한 후 서브넷 삭제
description: Azure SQL Managed Instance를 삭제 한 후 Azure 가상 네트워크를 삭제 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093687"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance를 삭제 한 후 서브넷 삭제
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 마지막 Azure SQL Managed Instance를 삭제 한 후 서브넷을 수동으로 삭제 하는 방법에 대 한 지침을 제공 합니다.

SQL 관리 되는 인스턴스는 [가상 클러스터](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)에 배포 됩니다. 각 가상 클러스터는 서브넷과 연결 됩니다. 가상 클러스터는 마지막 인스턴스 삭제 후 12 시간 동안 설계 된 후에도 동일한 서브넷에서 SQL 관리 되는 인스턴스를 더 빠르게 만들 수 있습니다. 빈 가상 클러스터를 유지 하는 데에는 요금이 부과 되지 않습니다. 이 기간 동안에는 가상 클러스터와 연결된 서브넷을 삭제할 수 없습니다.

12 시간을 기다리지 않고 가상 클러스터 및 서브넷을 더 일찍 삭제 하려면 수동으로이 작업을 수행할 수 있습니다. Azure Portal 또는 가상 클러스터 API를 사용 하 여 가상 클러스터를 수동으로 삭제 합니다.

> [!IMPORTANT]
> - 삭제를 성공적으로 수행 하려면 가상 클러스터에 SQL 관리 되는 인스턴스를 포함 하지 않아야 합니다. 
> - 가상 클러스터를 삭제 하는 작업은 약 1.5 시간 동안 지속 되는 장기 실행 작업입니다 (최신 가상 클러스터 삭제 시간에 대 한 [SQL Managed Instance 관리 작업](./sql-managed-instance-paas-overview.md#management-operations) 참조). 이 프로세스가 완료 될 때까지 가상 클러스터는 포털에 계속 표시 됩니다.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Azure Portal에서 가상 클러스터를 삭제 합니다.

Azure Portal를 사용 하 여 가상 클러스터를 삭제 하려면 가상 클러스터 리소스를 검색 합니다.

![검색 상자가 강조 표시 된 Azure Portal의 스크린샷](./media/virtual-cluster-delete/virtual-clusters-search.png)

삭제 하려는 가상 클러스터를 찾은 후이 리소스를 선택 하 고 **삭제** 를 선택 합니다. 가상 클러스터 삭제를 확인 하는 메시지가 표시 됩니다.

![삭제 옵션이 강조 표시 된 Azure Portal 가상 클러스터 대시보드의 스크린샷](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure Portal 알림은 가상 클러스터 삭제 요청이 성공적으로 전송 되었음을 확인 하는 메시지를 표시 합니다. 삭제 작업 자체는 약 1.5 시간 동안 지속 되며,이 기간 동안에는 가상 클러스터가 포털에 계속 표시 됩니다. 프로세스가 완료 되 면 가상 클러스터가 더 이상 표시 되지 않고 연결 된 서브넷이 다시 사용할 수 있도록 해제 됩니다.

> [!TIP]
> 가상 클러스터에 표시 된 SQL 관리 되는 인스턴스가 없고 가상 클러스터를 삭제할 수 없는 경우 진행 중인 인스턴스 배포가 진행 되 고 있지 않은지 확인 하십시오. 아직 진행 중인 시작 및 취소 된 배포도 포함 됩니다. 이러한 작업은 가상 클러스터를 계속 사용 하 여 삭제 되지 않도록 하는 것 이기 때문입니다. 인스턴스가 배포 된 리소스 그룹의 **배포** 탭을 검토 하 여 진행 중인 모든 배포를 표시 합니다. 이 경우 배포가 완료 될 때까지 기다렸다가 SQL Managed Instance를 삭제 한 다음 가상 클러스터를 삭제 합니다.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>API를 사용 하 여 가상 클러스터 삭제

API를 통해 가상 클러스터를 삭제 하려면 [가상 클러스터 삭제 방법](/rest/api/sql/virtualclusters/delete)에 지정 된 URI 매개 변수를 사용 합니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [AZURE SQL Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- [SQL Managed Instance의 연결 아키텍처](connectivity-architecture-overview.md)에 대해 알아봅니다.
- [SQL Managed Instance에 대 한 기존 가상 네트워크를 수정](vnet-existing-add-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크를 만들고, Azure SQL Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원 하는 방법을 보여 주는 자습서는 [AZURE sql Managed Instance 만들기 (포털)](instance-create-quickstart.md)를 참조 하세요.
- DNS 문제는 [사용자 지정 Dns 구성](custom-dns-configure.md)을 참조 하세요.