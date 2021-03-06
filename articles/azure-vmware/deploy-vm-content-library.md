---
title: Azure VMware 솔루션에서 Vm을 배포 하는 콘텐츠 라이브러리 만들기
description: Azure VMware 솔루션 사설 클라우드에 VM을 배포 하는 콘텐츠 라이브러리를 만듭니다.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: a50b12ef8e139bf7de171398fd28f74fc3f310c9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382007"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Azure VMware 솔루션에서 Vm을 배포 하는 콘텐츠 라이브러리 만들기

콘텐츠 라이브러리는 라이브러리 항목 형식으로 콘텐츠를 저장하고 관리합니다. 단일 라이브러리 항목은 VM(가상 머신)을 배포하는 데 사용되는 하나 이상의 파일로 구성됩니다. 

이 문서에서는 콘텐츠 라이브러리를 만드는 절차를 안내 합니다.  그런 다음 콘텐츠 라이브러리의 ISO 이미지를 사용 하 여 VM을 배포 하는 과정을 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료 하려면 NSX-T 세그먼트 (논리 스위치) 및 관리 되는 DHCP 서비스가 필요 합니다.  자세한 내용은 [Azure VMware 솔루션에서 DHCP를 관리 하는 방법](manage-dhcp.md) 문서를 참조 하세요.

## <a name="create-a-content-library"></a>콘텐츠 라이브러리 만들기

1. 온-프레미스 vSphere 클라이언트에서 **메뉴 > 콘텐츠 라이브러리** 를 선택합니다.

   ![메뉴 -> 콘텐츠 라이브러리 선택](./media/content-library/vsphere-menu-content-libraries.png)

1. **추가** 단추를 선택 하 여 새 콘텐츠 라이브러리를 만듭니다.

   ![추가 단추를 선택 하 여 새 콘텐츠 라이브러리를 만듭니다.](./media/content-library/create-new-content-library.png)

1. 이름을 지정하고, vCenter 서버의 IP 주소를 확인하고, **다음** 을 선택합니다.

   ![원하는 이름과 메모를 입력하고 [다음]을 선택합니다.](./media/content-library/new-content-library-step1.png)

1. **로컬 콘텐츠 라이브러리** 를 선택하고 **다음** 을 선택합니다.

   ![이 예제에서는 로컬 콘텐츠 라이브러리를 만들고 [다음]을 선택합니다.](./media/content-library/new-content-library-step2.png)

1. 콘텐츠 라이브러리를 저장할 데이터 저장소를 선택하고 **다음** 를 선택합니다.

   ![콘텐츠 라이브러리를 호스트할 데이터 저장소를 선택하고 [다음]을 선택합니다.](./media/content-library/new-content-library-step3.png)

1. 콘텐츠 라이브러리 설정을 검토하고 **마침** 을 선택합니다.

   ![설정을 확인하고 [마침]을 선택합니다.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>콘텐츠 라이브러리에 ISO 이미지 업로드

콘텐츠 라이브러리가 생성되었으므로, 이제 ISO 이미지를 추가하여 프라이빗 클라우드 클러스터에 VM을 배포할 수 있습니다. 

1. vSphere 클라이언트에서 **메뉴 > 콘텐츠 라이브러리** 를 선택합니다.

1. 새 ISO에 사용할 콘텐츠 라이브러리를 마우스 오른쪽 단추로 클릭하고 **항목 가져오기** 를 선택합니다.

1. 다음 중 하나를 수행하여 원본에 대한 라이브러리 항목을 가져온 다음, **가져오기** 를 선택합니다.
   1. URL을 선택하고 ISO를 다운로드할 URL을 입력합니다.

   1. 로컬 시스템에서 업로드할 **로컬 파일** 을 선택합니다.

   > [!TIP]
   > 선택 사항으로, 대상의 사용자 지정 항목 이름과 메모를 입력할 수 있습니다.

1. 라이브러리를 열고 **기타 유형** 탭을 선택하여 ISO가 성공적으로 업로드되었는지 확인합니다.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>프라이빗 클라우드 클러스터에 VM 배포

1. vSphere 클라이언트에서 **메뉴 > 호스트 및 클러스터** 를 선택합니다.

1. 왼쪽 패널에서 트리를 확장하고 클러스터를 선택합니다.

1. **작업 > 새 가상 머신** 을 선택합니다.

1. 마법사를 진행하고 원하는 설정을 수정합니다.

1. **새 CD/DVD 드라이브 > 클라이언트 디바이스 > 콘텐츠 라이브러리 ISO 파일** 을 선택합니다.

1. 이전 섹션에서 업로드한 ISO를 선택하고 **확인** 을 선택합니다.

1. 전원을 켜면 ISO가 탑재되도록 **연결** 확인란을 선택합니다.

1. **새 네트워크 > 드롭다운 선택 > 찾아보기** 를 선택합니다.

1. **논리 스위치(세그먼트)** 를 선택하고 **확인** 을 선택합니다.

1. 다른 하드웨어 설정을 수정하고 **다음** 을 선택합니다.

1. 설정을 검토하고 **마침** 을 선택합니다.


## <a name="next-steps"></a>다음 단계

이제 Azure VMware 솔루션에서 Vm을 배포 하는 콘텐츠 라이브러리를 만들었습니다. 다음에 대해 알아 볼 수 있습니다.

- [VMware HCX를 배포 및 구성](tutorial-deploy-vmware-hcx.md) 하 여 VM 워크 로드를 사설 클라우드로 마이그레이션합니다.
- [Azure VMware 솔루션 vm의 수명 주기 관리](lifecycle-management-of-azure-vmware-solution-vms.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
