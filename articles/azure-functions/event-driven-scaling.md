---
title: Azure Functions에서 이벤트 기반 크기 조정
description: 소비 계획 및 프리미엄 계획 함수 앱의 크기 조정 동작에 대해 설명 합니다.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937749"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Azure Functions에서 이벤트 기반 크기 조정

소비 및 프리미엄 계획에서 함수 호스트의 추가 인스턴스를 추가 하 여 CPU 및 메모리 리소스를 확장 Azure Functions 합니다. 인스턴스 수는 함수를 트리거하는 이벤트의 수에 따라 결정 됩니다. 

소비 계획에 있는 함수 호스트의 각 인스턴스는 1.5 GB의 메모리와 하나의 CPU로 제한 됩니다.  호스트의 인스턴스는 전체 함수 앱입니다. 즉, 함수 앱 내의 모든 함수는 인스턴스 내에서 리소스를 공유 하 고 동시에 크기를 조정 합니다. 동일 소비 계획 크기를 독립적으로 공유 하는 함수 앱  프리미엄 계획에서 계획 크기는 해당 인스턴스에서 해당 계획의 모든 앱에 대해 사용 가능한 메모리와 CPU를 결정 합니다.  

함수 코드 파일은 함수의 주 저장소 계정에서 Azure Files 공유에 저장 됩니다. 함수 앱의 주 스토리지 계정을 삭제하면 함수 코드 파일이 삭제되고 복구할 수 없습니다.

## <a name="runtime-scaling"></a>런타임 크기 조정

Azure Functions는 *크기 조정 컨트롤러* 라는 구성 요소를 사용하여 이벤트의 비율을 모니터링하고 규모를 확장하거나 감축할 것인지 결정합니다. 크기 조정 컨트롤러는 각 트리거 유형에 대해 추론을 사용합니다. 예를 들어 Azure Queue Storage 트리거를 사용하는 경우 큐 길이 및 가장 오래된 큐 메시지의 기간에 따라 트리거가 조정됩니다.

Azure Functions 확장 단위는 함수 앱입니다. 함수 앱을 확장하면 Azure Functions 호스트의 여러 인스턴스를 실행하기 위해 추가 리소스가 할당됩니다. 반대로, 컴퓨팅 수요가 감소하면 크기 조정 컨트롤러에서 함수 호스트 인스턴스를 제거합니다. 함수 앱 내에서 실행 중인 함수가 없으면 인스턴스 수가 결국 0으로 "확장" 됩니다.

![이벤트를 모니터링하고 인스턴스를 만드는 크기 조정 컨트롤러](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>콜드 부팅

함수 앱이 시간 (분) 동안 유휴 상태가 된 후 플랫폼은 앱이 실행 되는 인스턴스 수를 0으로 확장할 수 있습니다. 다음 요청에는 0에서 1로 크기를 조정 하는 추가 대기 시간이 있습니다. 이 대기 시간을 _콜드 시작_ 이라고 합니다. 함수 앱에 필요한 종속성 수가 콜드 시작 시간에 영향을 줄 수 있습니다. 콜드 시작은 응답을 반환 해야 하는 HTTP 트리거와 같은 동기 작업에 대 한 문제입니다. 콜드 시작이 함수에 영향을 주는 경우 프리미엄 계획에서 실행 하거나 **Always on** 설정을 사용 하는 전용 계획에서 실행 하는 것이 좋습니다.   

## <a name="understanding-scaling-behaviors"></a>크기 조정 동작 이해

크기 조정은 다양한 요인에 따라 다르고, 선택한 트리거 및 언어에 따라 달라질 수 있습니다. 유의 해야 할 몇 가지 확장 동작은 다음과 같습니다.

* **최대 인스턴스:** 단일 함수 앱은 최대 200 개의 인스턴스로 확장할 수 있습니다. 단일 인스턴스는 동시에 둘 이상의 메시지 또는 요청을 처리할 수 있지만 동시 실행 수를 제한하지 않습니다.  필요에 따라 크기를 조정 하 [는 최대](#limit-scale-out) 크기를 지정할 수 있습니다.
* **새 인스턴스 율:** HTTP 트리거의 경우 새 인스턴스는 초당 한 번만 할당 됩니다. HTTP가 아닌 트리거의 경우 새 인스턴스는 최대 30 초 마다 한 번씩 할당 됩니다. 확장성은 [프리미엄 계획](functions-premium-plan.md)에서 실행 하는 경우 더 빠릅니다.
* **확장 효율성:** Service Bus 트리거의 경우 가장 효율적인 크기 조정을 위해 리소스에 대 한 _관리_ 권한을 사용 합니다. _수신_ 권한을 사용 하면 크기 조정 결정을 알리는 데 큐 길이를 사용할 수 없으므로 크기 조정이 정확 하지 않습니다. Service Bus 액세스 정책에서 권한을 설정 하는 방법에 대 한 자세한 내용은 [공유 액세스 권한 부여 정책](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)을 참조 하세요. 이벤트 허브 트리거의 경우 참조 문서에서 [크기 조정 지침](functions-bindings-event-hubs-trigger.md#scaling) 을 참조 하세요. 

## <a name="limit-scale-out"></a>규모 확장 제한

앱을 확장 하는 데 사용 되는 최대 인스턴스 수를 제한할 수 있습니다.  이는 데이터베이스와 같은 다운스트림 구성 요소의 처리량이 제한 되는 경우에 가장 일반적입니다.  기본적으로 소비 계획 함수는 최대 200 인스턴스로 확장 되 고 프리미엄 계획 함수는 최대 100 인스턴스로 확장 됩니다.  값을 수정 하 여 특정 앱에 대해 더 낮은 최대값을 지정할 수 있습니다 `functionAppScaleLimit` .  는 `functionAppScaleLimit` 로 설정 `0` 하거나 `null` 무제한으로 설정 하거나, `1` 와 앱의 최대 값을 사용할 수 있습니다.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>확장성 있는 앱의 모범 사례 및 패턴

호스트 구성, 런타임 공간 및 리소스 효율성을 비롯 하 여 크기 조정 방법에 영향을 주는 함수 앱의 많은 측면이 있습니다.  자세한 내용은 [성능 고려 사항 문서의 확장성 섹션](functions-best-practices.md#scalability-best-practices)을 참조하세요. 또한 함수 앱의 확장에 따라 연결이 어떻게 작동하는지도 알고 있어야 합니다. 자세한 내용은 [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)을 참조하세요.

Python 및 Node.js의 크기 조정에 대 한 자세한 내용은 [Azure Functions python 개발자 가이드-크기 조정 및 동시성](functions-reference-python.md#scaling-and-performance) 및 [Azure Functions Node.js 개발자 가이드-크기 조정 및 동시성](functions-reference-node.md#scaling-and-concurrency)을 참조 하십시오.

## <a name="billing-model"></a>청구 모델

여러 요금제에 대 한 요금 청구는 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)에 자세히 설명 되어 있습니다. 사용량은 함수 앱 수준에서 집계되며 함수 코드가 실행될 때만 계산됩니다. 다음은 요금 청구 단위입니다.

* **기가바이트-초 단위의 리소스 소비(GB-s)**. 함수 앱 내 모든 함수의 메모리 크기와 실행 시간 조합으로 계산됩니다. 
* **실행**. 이벤트 트리거에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

사용량 청구서를 이해 하는 방법에 대 한 유용한 쿼리와 정보 [는 청구 FAQ에서](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)찾을 수 있습니다.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>다음 단계

+ [Azure Functions 호스팅 옵션](functions-scale.md)

