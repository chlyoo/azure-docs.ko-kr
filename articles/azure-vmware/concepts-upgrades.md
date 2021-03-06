---
title: 개념-사설 클라우드 업데이트 및 업그레이드
description: Azure VMware 솔루션의 주요 업그레이드 프로세스 및 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: d93453cbf6ad744844a04cd298cc18ad181cc0b0
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634995"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware 솔루션 사설 클라우드 업데이트 및 업그레이드

Azure VMware 솔루션 사설 클라우드의 장점 중 하나는 플랫폼이 유지 관리 된다는 것입니다. 유지 관리에는 최신 버전의 Azure VMware 솔루션 사설 클라우드 소프트웨어를 사용 하는 데 도움이 되도록 VMware 유효성이 검사 된 소프트웨어 번들에 대 한 자동화 된 업데이트가 포함 됩니다.

특히 Azure VMware 솔루션 사설 클라우드에는 다음이 포함 됩니다.

- VMware ESXi 하이퍼바이저로 프로 비전 된 전용 운영 체제 미 설치 서버 노드 
- ESXi 및 vSAN을 관리 하기 위한 vCenter 서버 
- VSphere 워크 로드 Vm을 위한 VMware NSX-T 소프트웨어 정의 네트워킹  
- Vsan 워크 로드 Vm에 대 한 VMware vSAN 데이터 저장소  
- 워크 로드 이동성을 위한 VMware HCX  

또한 Azure VMware 솔루션 사설 클라우드에는 연결 하 고 사설 클라우드를 작동 하는 데 필요한 Azure 언더레이의 리소스도 포함 됩니다. Azure VMware 솔루션은 언더레이 및 VMware 구성 요소의 상태를 지속적으로 모니터링 합니다. Azure VMware 솔루션이 실패를 감지 하면 실패 한 구성 요소를 복구 하는 작업을 수행 합니다. 

## <a name="what-components-get-updated"></a>업데이트 되는 구성 요소   

Azure VMware 솔루션은 다음 VMware 구성 요소를 업데이트 합니다. 

- 운영 체제 미 설치 서버 노드에서 실행 중인 vCenter Server 및 ESXi 
- vSAN 
- NSX-T 

또한 Azure VMware 솔루션은 드라이버, 네트워크 스위치의 소프트웨어 및 운영 체제 미 설치 노드의 펌웨어와 같은 언더레이의 소프트웨어를 업데이트 합니다. 

## <a name="types-of-updates"></a>업데이트 유형

Azure VMware 솔루션은 VMware 구성 요소에 다음과 같은 유형의 업데이트를 적용 합니다.

- 패치: VMware에서 릴리스된 보안 패치 및 버그 수정 
- 업데이트: 하나 이상의 VMware 구성 요소에 대 한 부 버전 업데이트입니다. 
- 업그레이드: 하나 이상의 VMware 구성 요소에 대 한 주 버전 업데이트입니다.

패치를 사설 클라우드에 적용 하기 전과 후에 알림이 표시 됩니다. 또한 사설 클라우드에 업데이트 또는 업그레이드를 적용 하기 전에 유지 관리 기간을 예약 하는 작업을 수행 합니다. 

## <a name="vmware-appliance-backup"></a>VMware 어플라이언스 백업 

Azure VMware 솔루션은 다음 VMware 구성 요소에 대 한 구성 백업도 수행 합니다.

- vCenter Server 
- NSX-T 관리자 

오류가 발생 한 시점에 Azure VMware 솔루션은 구성 백업에서 이러한 구성 요소를 복원할 수 있습니다. 

VMware 소프트웨어 버전에 대 한 자세한 내용은 [사설 클라우드 및 클러스터 개념 문서](concepts-private-clouds-clusters.md) 및 [FAQ](faq.yml)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware 솔루션의 주요 업그레이드 프로세스 및 기능을 설명 했으므로 다음에 대해 알아볼 수 있습니다.

- [사설 클라우드를 만드는 방법](tutorial-create-private-cloud.md)
- [Azure VMware 솔루션 리소스를 사용 하도록 설정 하는 방법](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
