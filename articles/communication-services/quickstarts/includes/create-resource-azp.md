---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 051f903c0b203045886b80f72d868adc1fa85f73
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886101"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Azure Communication 리소스 만들기

Azure Communication Services 리소스를 만들려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다. 페이지의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Azure Portal에서 리소스 만들기를 강조 표시하는 스크린샷.":::

**Marketplace 검색** 입력 또는 포털 상단에 있는 검색 창에 **Communication** 을 입력합니다.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="검색 창에서 통신 서비스 검색을 보여주는 스크린샷.":::

결과에서 **Communication Services** 를 선택한 다음, **추가** 를 선택합니다.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="추가 단추가 강조 표시된 Azure 패널을 보여주는 스크린샷.":::

이제 Communication Services 리소스를 구성할 수 있습니다. 만들기 프로세스의 첫 페이지에서 다음을 지정하라는 메시지가 표시됩니다.

* 구독
* 리소스 그룹(새 그룹을 만들거나 기존 리소스 그룹을 선택할 수 있음)
* Communication Services 리소스의 이름
* 리소스가 연결되는 지리적 위치

다음 단계에서는 리소스에 태그를 할당할 수 있습니다. 태그를 사용하여 Azure 리소스를 구성할 수 있습니다. 태그에 대한 자세한 내용은 [리소스 태그 지정 설명서](../../../azure-resource-manager/management/tag-resources.md)를 참조하세요.

마지막으로 구성을 검토하고 **리소스** 를 만들 수 있습니다. 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="manage-your-communication-services-resource"></a>Communication Services 리소스 관리

Communication Services 리소스를 관리하려면 [Azure Portal](https://portal.azure.com)로 이동하여 **Azure Communication Services** 를 검색하여 선택합니다.

**Communication Services** 페이지에서 리소스의 이름을 선택합니다.

리소스의 **개요** 페이지에는 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 옵션이 포함되어 있습니다. 리소스 페이지의 왼쪽 메뉴에서 추가 구성 옵션을 찾을 수 있습니다.