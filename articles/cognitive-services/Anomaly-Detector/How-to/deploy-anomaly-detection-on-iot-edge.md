---
title: IoT Edge에서 변칙 탐지기 실행
titleSuffix: Azure Cognitive Services
description: IoT Edge에 변칙 탐지기 모듈을 배포 합니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936277"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>IoT Edge에 변칙 탐지기 모듈 배포

IoT Edge 장치에 Cognitive Services [변칙 탐지기](../anomaly-detector-container-howto.md) 모듈을 배포 하는 방법에 대해 알아봅니다. IoT Edge에 배포 되 고 나면 모듈은 다른 모듈과 함께 IoT Edge에서 컨테이너 인스턴스로 실행 됩니다. 표준 docker 컨테이너 환경에서 실행 되는 변칙 탐지기 컨테이너 인스턴스와 정확히 동일한 Api를 노출 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독을 사용합니다. Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.
* [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
* [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) 및 [IoT Edge](../../../iot-edge/quickstart-linux.md) 장치

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>이상에 변칙 검색 모듈 배포

1. Azure Portal에서 검색에 **IoT Edge 변칙 탐지기** 를 입력 하 고 Azure Marketplace 결과를 엽니다.
2. [IoT Edge 모듈의 Azure Portal 대상 장치 페이지로](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector)이동 합니다. 다음 필수 정보를 제공합니다.

    1. 구독을 선택합니다.

    1. IoT Hub를 선택 합니다.

    1. **장치 찾기** 를 선택 하 고 IoT Edge 장치를 찾습니다.

3. **만들기** 단추를 선택합니다.

4. **AnomalyDetectoronIoTEdge** 모듈을 선택 합니다.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="선택할 항목 임을 나타내는 AnomalyDetectoronIoTEdge 링크가 있는 IoT Edge 모듈 사용자 인터페이스의 이미지가 빨간색 상자로 강조 표시 됩니다.":::

5. **환경 변수** 를 탐색하고 다음 정보를 제공합니다.

    1.  Eula에 대해 **수락** 값을 유지합니다.

    1. Cognitive Services 엔드포인트로 **청구** 를 채웁니다.

    1. Cognitive Services API 키로 **ApiKey** 를 채웁니다.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="끝점 및 API 키에 대 한 값을 입력 해야 하는 영역 주위에 빨간색 상자가 있는 환경 변수":::

6. **업데이트** 를 선택합니다.

7. **다음:** 경로를 선택 하 여 경로를 정의 합니다. 모든 모듈의 모든 메시지를 Azure IoT Hub로 이동하도록 정의합니다.

8. **다음: 검토 + 만들기** 를 선택합니다. IoT Edge 디바이스에 배포되는 모든 모듈을 정의하는 JSON 파일을 미리 볼 수 있습니다.
    
9. **만들기** 를 선택하여 모듈 배포를 시작합니다.

10. 모듈 배포를 완료한 후에는 IoT 허브의 IoT Edge 페이지로 돌아갑니다. IoT Edge 디바이스 목록에서 디바이스를 선택하면 세부 정보를 볼 수 있습니다.

11. 아래로 스크롤하여 나열된 모듈을 확인합니다. 새 모듈에 대해 런타임 상태가 실행 중인지 확인 합니다. 

IoT Edge 장치의 런타임 상태 문제를 해결 하려면 [문제 해결 가이드](../../../iot-edge/troubleshoot.md) 를 참조 하세요.

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>IoT Edge 장치에서 변칙 탐지기 테스트

Azure Cognitive Services 컨테이너가 실행되고 있는 Azure IoT Edge 디바이스에 대한 HTTP 호출을 수행합니다. 컨테이너는 REST 기반 끝점 Api를 제공 합니다. `http://<your-edge-device-ipaddress>:5000`모듈 api에 대해 호스트를 사용 합니다.

Edge 장치가 포트 5000에서 인바운드 통신을 아직 허용 하지 않는 경우 새 **인바운드 포트 규칙** 을 만들어야 합니다. 

Azure VM의 경우 **가상 머신**  >  **설정**  >  **네트워킹**  >  **인바운드 포트 규칙**  >  **추가 인바운드 포트 규칙** 에서 설정할 수 있습니다.

모듈이 실행 중인지 확인 하는 방법에는 여러 가지가 있습니다. 해당 하는에 지 장치의 *외부 IP* 주소 및 노출 된 포트를 찾아 즐겨 찾는 웹 브라우저를 엽니다. 아래에서 다양 한 요청 Url을 사용 하 여 컨테이너가 실행 중인지 확인 합니다. 아래 나열 된 예제 요청 Url은 `http://<your-edge-device-ipaddress:5000` 이지만 특정 컨테이너는 다를 수 있습니다. Edge 장치의 *외부 IP* 주소를 사용 해야 한다는 점에 유의 하세요.

| 요청 URL | 용도 |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | 컨테이너는 홈페이지를 제공합니다. |
| `http://<your-edge-device-ipaddress>:5000/status` | 또한 GET을 사용 하 여 요청 하면 컨테이너를 시작 하는 데 사용 된 api 키가 끝점 쿼리를 발생 시 키 지 않고 유효한 지 여부를 확인 합니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | 컨테이너는 엔드포인트에 대한 전체 설명서 세트와 **사용해 보기** 기능을 제공합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |

![컨테이너의 홈페이지](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>다음 단계

* 컨테이너 이미지를 끌어오거나 컨테이너를 실행 하기 위한 컨테이너 [설치 및 실행](../anomaly-detector-container-configuration.md) 을 검토 합니다.
* [컨테이너 구성](../anomaly-detector-container-configuration.md)에서 구성 설정을 검토합니다.
* [변칙 탐지기 API 서비스에 대 한 자세한 정보](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
