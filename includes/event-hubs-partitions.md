---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 780da47e6f071d854a16ca1d1c5cd02dbdd6bef0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955650"
---
이벤트 허브는 이벤트 시퀀스를 하나 이상의 파티션으로 구성합니다. 최신 이벤트가 도착하면 이 시퀀스의 끝에 추가됩니다. 파티션을 "커밋 로그"로 생각할 수 있습니다.

파티션은 이벤트의 본문을 포함하는 이벤트 데이터, 이벤트를 설명하는 사용자 정의 속성 모음, 파티션의 오프셋, 스트림 시퀀스의 번호, 서버 측에서 수락된 시간의 타임스탬프 같은 메타데이터를 보관합니다.

![이전 이벤트에서 최신 이벤트 순서로 이벤트 시퀀스를 표시하는 다이어그램입니다.](./media/event-hubs-partitions/partition.png)

Event Hubs는 대량의 이벤트를 처리하는 데 도움이 되도록 설계되었으며, 분할은 다음과 같은 두 가지 방면에서 유용합니다.

첫째, Event Hubs는 PaaS 서비스이지만 실제 현실을 기반으로 하며, 이벤트의 순서를 유지하는 로그를 유지 관리하려면 이러한 이벤트가 기본 스토리지 및 복제본에 함께 유지되어야 하므로 이러한 로그에 대한 처리량 상한이 발생합니다. 분할을 사용하면 동일한 이벤트 허브에 여러 병렬 로그를 사용할 수 있으므로 사용 가능한 원시 IO 처리량 용량이 크게 증가합니다.

둘째, 사용자의 애플리케이션은 이벤트 허브로 전송되는 이벤트의 볼륨 처리를 유지할 수 있어야 합니다. 이 작업이 복잡하여 꽤 많은 병렬 처리 용량을 스케일 아웃해야 할 수도 있습니다. 파티션의 이론적 원리는 위와 같습니다. 이벤트를 처리하는 단일 프로세스의 용량은 제한적이므로 여러 프로세스가 필요하며, 파티션은 솔루션이 이러한 프로세스를 제공하되 각 이벤트의 처리 소유자를 명확히 하는 방법입니다. 

Event Hubs는 모든 파티션에 적용되도록 구성된 보존 시간에 대한 이벤트를 유지합니다. 보존 기간에 도달하면 이벤트가 자동으로 제거됩니다. 보존 기간을 1일로 지정하면 해당 이벤트가 승인되고 정확히 24시간이 지나면 볼 수 없게 됩니다. 이벤트는 명시적으로 삭제할 수 없습니다. 

허용되는 보존 시간은 Event Hubs Standard의 경우 최대 7일이고, Event Hubs Dedicated의 경우 최대 90일입니다. 허용되는 보존 기간을 초과하여 이벤트를 보관해야 하는 경우 [Event Hubs 캡처 기능을 설정하여 Azure Storage 또는 Azure Data Lake에 자동으로 저장](../articles/event-hubs/event-hubs-capture-overview.md)되도록 할 수 있으며, 이러한 심층 보관 스토리지 계층을 검색하거나 분석해야 하는 경우 [Azure Synapse 또는 다른 유사한 저장소 및 분석 플랫폼으로 쉽게 가져올 수 있습니다](../articles/event-hubs/store-captured-data-data-warehouse.md). 

Event Hubs에서 데이터 보존 시간을 제한하는 이유는 타임스탬프로만 인덱싱되는 심층 저장소에 대량의 고객 데이터 기록이 모이지 않게 방지하고 순차적 액세스만 허용하도록 하기 위해서입니다. 여기에는 Event Hubs 또는 Kafka에서 제공하는 실시간 이벤트 인터페이스보다 풍부한 인덱싱 및 직접 액세스가 데이터 기록에 필요하다는 아키텍처 철학이 적용되었습니다. 이벤트 스트림 엔진은 이벤트 소싱을 위한 데이터 레이크 또는 장기 보관 스토리지 계층의 역할을 수행하는 데 적합하지 않습니다. 

파티션은 독립적이며 자체 데이터 시퀀스를 포함하기 때문에 종종 다른 속도로 증가합니다. Event Hubs에서는 Apache Kafka처럼 관리자 개입이 필요한 것은 아니지만, 불균등한 분포로 인해 다운스트림 이벤트 프로세서의 부하가 불균등해집니다.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

파티션 수는 만들 때 지정되며 Event Hubs Standard의 경우 1과 32 사이여야 합니다. Event Hubs Dedicated의 경우 용량 단위당 파티션 수는 최대 2,000개가 될 수 있습니다. 

특정 이벤트 허브에서 애플리케이션의 부하가 가장 높을 때는 지속적 TU([처리량 단위)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units)에 최소한 필요한 만큼의 파티션을 선택하는 것이 좋습니다. 단일 파티션의 처리량 용량을 1TU(1MByte 입력, 2MByte 출력)로 계산해야 합니다. 파티션 수에 관계없이 네임스페이스의 TU 또는 클러스터의 용량 단위를 조정할 수 있습니다. 32개의 파티션이 있는 이벤트 허브 또는 1개의 파티션이 있는 이벤트 허브는 네임스페이스가 1TU 용량으로 설정된 경우 정확히 동일한 비용을 발생시킵니다. 

애플리케이션은 다음 세 가지 방법 중 하나로 파티션에 대한 이벤트 매핑을 제어합니다.

- 파티션 키를 지정하여 사용 가능한 파티션 중 하나에 일관되게 매핑(해시 함수 사용) 
- 파티션 키를 지정하지 않고 broker가 특정 이벤트에 대한 파티션을 임의로 선택하도록 허용
- 특정 파티션에 명시적으로 이벤트 전송.

파티션 키를 지정하면 관련 이벤트를 동일한 파티션에 함께 유지하고 정확히 전송된 순서대로 유지할 수 있습니다. 파티션 키는 애플리케이션 컨텍스트에서 파생되고 이벤트의 상호 관계를 식별하는 문자열입니다.

파티션 키로 식별되는 이벤트 시퀀스는 *스트림* 입니다. 파티션은 이러한 여러 스트림에 대한 멀티플렉싱 로그 저장소입니다. 

이벤트 허브가 생성된 후 [전용 Event Hubs 클러스터](../articles/event-hubs/event-hubs-dedicated-overview.md)의 이벤트 허브 파티션 수를 [늘릴 수 있지만](../articles/event-hubs/dynamically-add-partitions.md), 파티션 수를 늘리면 파티션에 대한 파티션 키 매핑이 변경되어 파티션 전체의 스트림 분포가 변경되므로 애플리케이션에서 이벤트의 상대적인 순서가 중요한 경우에는 이러한 변경을 방지하기 위해 열심히 노력해야 합니다.

파티션 수를 최대 허용 값으로 설정하고 싶겠지만 여러 파티션을 활용할 수 있도록 이벤트 스트림을 구성해야 한다는 점을 항상 유의해야 합니다. 모든 이벤트 또는 소수의 하위 스트림에서 절대 순서를 유지해야 하는 경우 많은 파티션을 활용하지 못할 수 있습니다. 또한 파티션이 많으면 처리가 더 복잡해집니다. 

파티션을 직접 전송할 수는 있지만 권장하지 않습니다. 대신 [이벤트 게시자](../articles/event-hubs/event-hubs-features.md#event-publishers) 섹션에서 소개하는 더 높은 수준의 구문을 사용할 수 있습니다. 

파티션 및 가용성과 안정성 간의 균형에 대한 자세한 내용은 [Event Hubs 프로그래밍 가이드](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) 및 [Event Hubs의 가용성 및 일관성](../articles/event-hubs/event-hubs-availability-and-consistency.md) 문서를 참조하세요.
