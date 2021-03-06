---
title: Azure Monitor 통합 문서 액세스 제어
description: 역할 기반 access control을 사용 하 여 미리 작성 되 고 사용자 지정 매개 변수가 있는 통합 문서로 복잡 한 보고 간소화
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: dd01b7e57441d4e5d763a07647ee27f1eef53ea9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613799"
---
# <a name="access-control"></a>Access Control

통합 문서에서 Access control은 다음 두 가지를 의미 합니다.

* 통합 문서의 데이터를 읽으려면 액세스 권한이 필요 합니다. 이 액세스는 통합 문서에 사용 되는 리소스에 대 한 표준 [Azure 역할](../../role-based-access-control/overview.md) 에 의해 제어 됩니다. 통합 문서는 해당 리소스에 대 한 액세스를 지정 하거나 구성 하지 않습니다. 사용자는 일반적으로 해당 리소스에 대 한 [모니터링 판독기](../../role-based-access-control/built-in-roles.md#monitoring-reader) 역할을 사용 하 여 이러한 리소스에 대 한 액세스 권한을 얻습니다.

* 통합 문서를 저장 하는 데 필요한 액세스

    - 비공개 `("My")` 통합 문서를 저장 하려면 추가 권한이 필요 하지 않습니다. 모든 사용자는 개인 통합 문서를 저장할 수 있으며 해당 통합 문서를 볼 수 있습니다.
    - 공유 통합 문서를 저장 하려면 통합 문서를 저장할 리소스 그룹에 대 한 쓰기 권한이 있어야 합니다. 이러한 권한은 일반적으로 [모니터링 참여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할에 의해 지정 되지만 *통합 문서 참가자* 역할을 통해 설정할 수도 있습니다.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>통합 문서 관련 권한이 있는 표준 역할

[모니터링 판독기](../../role-based-access-control/built-in-roles.md#monitoring-reader) 에는 리소스에서 데이터를 읽는 모니터링 도구 (통합 문서 포함)에서 사용할 수 있는 표준/읽기 권한이 포함 되어 있습니다.

[모니터링 참가자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 에 `/write` 는 항목 저장을 위한 다양 한 모니터링 도구에서 사용 하는 일반 권한 ( `workbooks/write` 공유 통합 문서 저장 권한 포함)이 포함 됩니다.
"통합 문서 참여자"는 공유 통합 문서를 저장 하기 위해 개체에 "통합 문서/쓰기" 권한을 추가 합니다.
사용자가 볼 수 있는 전용 통합 문서를 저장 하는 데에는 특별 한 권한이 필요 하지 않습니다.

사용자 지정 역할:

`microsoft.insights/workbooks/write`공유 통합 문서를 저장 하려면를 추가 합니다. 자세한 내용은 [통합 문서 참가자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 통합 문서에 대 한 자세한 내용은 다양 한 기능을 갖춘 시각화 옵션을 [시작](../platform/workbooks-overview.md#visualizations) 하세요.