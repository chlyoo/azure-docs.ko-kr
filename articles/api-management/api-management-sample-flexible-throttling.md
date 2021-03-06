---
title: Azure API Management로 고급 요청 제한
description: Azure API Management로 유연한 할당량 및 속도 제한 정책을 만들고 적용하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 36b21196207f65975dae950f43ec0c7094991dad
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362032"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Azure API Management로 고급 요청 제한
들어오는 요청을 제한하는 것은 Azure API Management의 중요한 역할입니다. Azure API Management는 요청 속도 또는 전송된 총 요청/데이터를 제어하여 API 공급자가 자신의 API가 남용되지 않도록 보호하고 다양한 API 제품 계층에 대해 가치를 창출할 수 있도록 합니다.

## <a name="rate-limits-and-quotas"></a>요율 한도 및 할당량
요금 제한과 할당량은 다양 한 용도로 사용 됩니다.

### <a name="rate-limits"></a>속도 제한
속도 제한은 일반적으로 짧고 강한 볼륨 버스트를 방지 하는 데 사용 됩니다. 예를 들어 백 엔드 서비스의 데이터베이스에서 호출 볼륨이 많은 병목 상태가 발생 하는 경우 `rate-limit-by-key` 이 설정을 사용 하 여 높은 통화 볼륨을 허용 하지 않도록 정책을 설정할 수 있습니다.

### <a name="quotas"></a>할당량
할당량은 일반적으로 더 긴 시간 동안 호출 속도를 제어 하는 데 사용 됩니다. 예를 들어 특정 월에 특정 구독자가 수행할 수 있는 총 호출 수를 설정할 수 있습니다. API를 수익 화 계층 기반 구독에 대해 할당량을 다르게 설정할 수도 있습니다. 예를 들어 기본 계층 구독은 한 달에 1만 개 미만의 호출을 할 수 있지만 프리미엄 계층은 매월 1억 호출까지 이동할 수 있습니다.

Azure API Management 내에서 속도 제한은 일반적으로 노드에 걸쳐 더 빠르게 전파 되어 급증을 방지 합니다. 반면에 사용 할당량 정보는 더 긴 기간 동안 사용 되므로 구현은 다릅니다.

> [!CAUTION]
> 조정 아키텍처의 분산 특성으로 인해 요율 제한은 완전히 정확 하지 않습니다. 구성 된 요청 수와 허용 되는 실제 요청 수의 차이는 요청 볼륨 및 비율, 백 엔드 대기 시간 및 기타 요인에 따라 달라 집니다.

## <a name="product-based-throttling"></a>제품 기반 제한
특정 구독으로 범위가 지정 된 속도 제한 기능은 api 공급자가 해당 API를 사용 하기 위해 등록 한 개발자에 게 제한을 적용 하는 데 유용 합니다. 그러나 예를 들어 API의 개별 최종 사용자를 제한 하는 것은 지원 되지 않습니다. 개발자 응용 프로그램의 단일 사용자가 전체 할당량을 사용 하 여 개발자의 다른 고객이 응용 프로그램을 사용할 수 없도록 할 수 있습니다. 또한, 대용량의 요청을 생성할 수 있는 여러 고객이 간헐적 사용자에 대한 액세스를 제한할 수 있습니다.

## <a name="custom-key-based-throttling"></a>사용자 지정 키 기반 조정

> [!NOTE]
> `rate-limit-by-key` `quota-by-key` Azure API Management의 소비 계층에서는 및 정책을 사용할 수 없습니다. 

[키](./api-management-access-restriction-policies.md#LimitCallRateByKey) 별 및 [할당량](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) 별 정책은 트래픽 제어에 보다 유연한 솔루션을 제공 합니다. 이러한 정책을 통해 트래픽 사용량을 추적 하는 데 사용 되는 키를 식별 하는 식을 정의할 수 있습니다. 이러한 작동 방식은 예제를 통해 가장 쉽게 이해할 수 있습니다. 

## <a name="ip-address-throttling"></a>IP 주소 제한
다음 정책은 단일 클라이언트 IP 주소를 1분에 10개 호출만으로 제한하고 1개월에 총 1,000,000개 호출과 10,000킬로바이트의 대역폭으로 제한합니다. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

인터넷의 모든 클라이언트가 고유 IP 주소를 사용한 경우 사용자별로 사용량을 제한하는 효과적인 방법일 수 있습니다. 그러나 사용자는 NAT 디바이스를 통해 인터넷에 액세스하므로 여러 사용자가 하나의 공용 IP 주소를 공유할 가능성이 높습니다. 그럼에도 불구하고 `IpAddress` 에 대한 무단 액세스를 허용하는 API에 대해서는 최상의 옵션일 수 있습니다.

## <a name="user-identity-throttling"></a>사용자 ID 제한
최종 사용자가 인증된 후 해당 사용자를 고유하게 식별하는 정보를 기반으로 제한 키를 생성할 수 있습니다.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

이 예제에서는 인증 헤더를 추출하여 `JWT` 개체로 변환하고 토큰의 주체를 사용하여 사용자를 식별하고 속도 제한 키로 사용하는 방법을 보여 줍니다. 사용자 ID가 `JWT`에 다른 클레임 중 하나로 저장된 경우 해당 값을 대신 사용할 수 있습니다.

## <a name="combined-policies"></a>결합된 정책
사용자 기반 조정 정책은 구독 기반 조정 정책 보다 더 많은 제어를 제공 하지만 두 기능을 결합 하는 값도 있습니다. 제품 구독 키로 제한([구독으로 호출 속도 제한](./api-management-access-restriction-policies.md#LimitCallRate) 및 [구독으로 사용 할당량 설정](./api-management-access-restriction-policies.md#SetUsageQuota))은 사용 수준에 따라 요금을 청구하여 API 수익화를 실현하는 뛰어난 방법입니다. 사용자별로 제한할 수 있도록 세분화된 제어로 보완되며 한 사용자의 동작이 다른 사용자의 환경에 부정적인 영향을 주지 않도록 합니다. 

## <a name="client-driven-throttling"></a>클라이언트 기반 제한
제한 키가 [정책 식](./api-management-policy-expressions.md)을 사용하여 정의된 경우 제한 범위를 정하는 방식을 선택하는 것은 API 공급자입니다. 그러나 개발자가 자신의 고객을 속도 제한하는 방식을 제어하려 할 수 있습니다. API 공급자가 개발자의 클라이언트 애플리케이션이 키를 API에 전달하도록 허용하는 사용자 지정 헤더를 도입하여 이를 수행할 수 있습니다.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

따라서 개발자의 클라이언트 애플리케이션이 속도 제한 키를 만드는 방법을 선택할 수 있습니다. 클라이언트 개발자는 키 집합을 사용자에 할당하고 키 사용을 회전하여 자신의 고유한 속도 계층을 만들 수 있습니다.

## <a name="summary"></a>요약
Azure API Management에서는 속도 및 할당량 제한을 제공하여 API 서비스를 보호하고 가치를 더합니다. 사용자 지정 범위 규칙이 포함된 새로운 제한 정책을 통해 정책을 세밀하게 제어할 수 있으므로 고객이 훨씬 향상된 애플리케이션을 구축할 수 있도록 합니다. 이 문서의 예제에서는 클라이언트 IP 주소, 사용자 ID 및 클라이언트에서 생성한 값으로 속도 제한 키를 제조하여 이러한 새 정책을 사용하는 방법을 보여줍니다. 그러나 사용자 에이전트, URL 경로 조각, 메시지 크기와 같이 사용할 수 있는 메시지의 많은 다른 부분이 있습니다.

## <a name="next-steps"></a>다음 단계
이 항목에 대 한 GitHub 문제로 사용자 의견을 제공 해 주세요. 귀하의 시나리오에서 논리적으로 선택된 잠재적인 다른 키 값에 대한 의견을 환영합니다.
