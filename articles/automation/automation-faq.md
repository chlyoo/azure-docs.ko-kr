---
title: Azure Automation FAQ | Microsoft Docs
description: 이 문서에서는 Azure Automation에 대한 질문과 대답을 제공합니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724275"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation 질문과 대답

이 Microsoft FAQ는 Azure Automation에 대한 일반적인 질문과 대답 목록입니다. 해당 기능에 대 한 다른 질문이 있는 경우 토론 포럼으로 이동 하 여 질문을 게시 합니다. 질문을 자주 묻는 메시지를이 문서에 추가 하 여 빠르고 쉽게 찾을 수 있습니다.

## <a name="update-management"></a>업데이트 관리

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>예기치 않은 OS 수준 업그레이드를 방지할 수 있나요?

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서는 OS 수준 업그레이드가 패키지를 통해 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. 업데이트 관리는 관리자가 Linux 머신에서 로컬로 사용하는 것과 동일한 방법으로 패키지를 업데이트하므로 이 동작은 의도적인 것입니다.

업데이트 관리 배포를 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 `redhat-release-server.x86_64`입니다.

### <a name="why-arent-criticalsecurity-updates-applied"></a>중요/보안 업데이트가 적용되지 않는 이유는 무엇인가요?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정된 조건에 맞는 업데이트를 필터링합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 적용을 수행하므로, 로컬 머신에 해당 정보가 없더라도 업데이트 관리의 일부 업데이트에 보안에 영향을 주는 것으로 플래그를 지정할 수 있습니다. Linux 시스템에 중요 업데이트를 적용하면 해당 머신의 보안에 영향을 주는 것으로 표시되지 않은 일부 업데이트가 있으므로 업데이트가 적용되지 않습니다. 그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있으므로 해당 머신을 호환되지 않는 것으로 보고할 수 있습니다.

업데이트 분류에 따라 업데이트를 배포하는 것은 RTM 버전의 CentOS에서 작동하지 않습니다. CentOS에 대한 업데이트를 제대로 배포하려면 업데이트를 적용할 수 있도록 모든 분류를 선택합니다. SUSE의 경우 분류로 **다른 업데이트** 를 선택 하면 zypper (패키지 관리자)와 관련 된 다른 보안 업데이트를 설치 하거나 해당 종속성이 먼저 필요할 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 경우에 따라 업데이트 배포를 다시 실행한 후 업데이트 로그를 통해 배포를 확인해야 할 수도 있습니다.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure 테넌트에 업데이트를 배포할 수 있나요?

업데이트 관리에 보고하는 다른 Azure 테넌트에서 패치가 필요한 머신이 있는 경우 다음 해결 방법을 사용하여 예약해야 합니다. `ForUpdateConfiguration` 매개 변수가 지정된 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet을 사용하여 일정을 만들 수 있습니다. [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet을 사용하여 다른 테넌트의 머신을 `NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>프로세스 자동화-Python runbook

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure Automation에서 지원 되는 Python 3 버전은 무엇 인가요?

클라우드 작업의 경우 Python 3.8이 지원 됩니다. 코드가 서로 다른 버전에서 호환 되는 경우에는 모든 2.x 버전의 스크립트 및 패키지가 작동할 수 있습니다.

Windows Hybrid Runbook Worker에 대 한 하이브리드 작업의 경우 사용 하려는 모든 2.x 버전을 설치 하도록 선택할 수 있습니다. Linux Hybrid Runbook Worker에 대 한 하이브리드 작업의 경우 컴퓨터에 설치 된 Python 3 버전에 따라 DSC OMSConfig 및 Linux Hybrid Worker를 실행 합니다. 버전 3.6을 설치 하는 것이 좋습니다. 그러나 Python 3 버전 사이에 메서드 서명 또는 계약의 주요 변경 내용이 없는 경우에도 서로 다른 버전을 사용 해야 합니다.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 및 Python 3 runbook을 동일한 automation 계정에서 실행할 수 있나요?

예, 동일한 automation 계정에서 Python 2 및 Python 3 runbook을 사용 하는 데에는 제한이 없습니다.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>기존 Python 2 runbook 및 패키지를 Python 3으로 마이그레이션하기 위한 계획은 무엇 인가요?

Azure Automation Python 2 runbook과 패키지를 Python 3으로 마이그레이션할 계획이 없습니다. 이 마이그레이션을 직접 수행 해야 합니다. 기존 및 새 Python 2 runbook과 패키지는 계속 작동 합니다.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Python 3 환경에서 기본적으로 지원 되는 패키지는 무엇 인가요?

Azure package 4.0.0는 기본적으로 Python 3 Automation 환경에 설치 됩니다. 더 높은 버전의 Azure 패키지를 수동으로 가져와서 기본 버전을 재정의할 수 있습니다.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Python 2 패키지를 참조 하는 Python 3 runbook을 실행 하거나 그 반대로 실행 하는 경우 어떻게 되나요?

Python 2와 Python 3에는 서로 다른 실행 환경이 있습니다. Python 2 runbook을 실행 하는 동안 python 3 패키지를 가져오고 Python 3의 경우에도 유사 하 게 가져올 수 있습니다.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Python 2와 Python 3 runbook과 패키지를 구분할 어떻게 할까요? 있나요?

Python 3은 Python 2와 Python 3 runbook을 구분 하는 새로운 runbook 정의입니다. 마찬가지로 Python 3 패키지에 대해서도 다른 패키지 종류가 도입 되었습니다.

## <a name="next-steps"></a>다음 단계

질문에 대 한 답변이 없는 경우 다음 소스를 참조 하 여 더 많은 질문과 대답을 확인할 수 있습니다.

- [Azure Automation](/answers/topics/azure-automation.html)
- [피드백 포럼](https://feedback.azure.com/forums/905242-update-management)
