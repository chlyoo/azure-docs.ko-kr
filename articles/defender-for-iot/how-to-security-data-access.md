---
title: 액세스 보안 & 권장 사항 데이터
description: IoT 용 Defender를 사용 하는 경우 보안 경고 및 권장 사항 데이터에 액세스 하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: d054b005b6b11e718662619b5525de3fc1ae5f4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592644"
---
# <a name="access-your-security-data"></a>보안 데이터 액세스

IoT 용 Defender는 보안 경고, 권장 사항 및 원시 보안 데이터 (저장 하기로 선택한 경우)를 Log Analytics 작업 영역에 저장 합니다.

## <a name="log-analytics"></a>Log Analytics

사용 되는 Log Analytics 작업 영역을 구성 하려면:

1. IoT Hub를 엽니다.
1. **보안** 섹션에서 **설정** 블레이드를 클릭 합니다.
1. **데이터 컬렉션** 을 클릭 하 고 Log Analytics 작업 영역 구성을 변경 합니다.

구성 후 Log Analytics 작업 영역에서 경고 및 권장 사항에 액세스 하려면 다음을 수행 합니다.

1. IoT 용 Defender에서 경고 또는 권장 사항을 선택 합니다.
1. **추가 조사** 를 클릭 한 다음 **이 경고가 있는 장치를 보려면 여기를 클릭 하 여 DeviceId 열을 확인** 합니다.

Log Analytics에서 데이터를 쿼리 하는 방법에 대 한 자세한 내용은 [Log Analytics에서 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조 하세요.

## <a name="security-alerts"></a>보안 경고

보안 경고는 IoT 용 Defender 솔루션에 대해 구성 된 Log Analytics 작업 영역의 _AzureSecurityOfThings 경고_ 테이블에 저장 됩니다.

보안 경고 탐색을 시작 하는 데 도움이 되는 다양 한 유용한 쿼리를 제공 하 고 있습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드를 선택 합니다.

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 설명                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격 성공           | 장치에서 무차별 암호 대입 공격 성공        |    {"전체 원본 주소": "[ \" 10.165.12.18: \" ]", "사용자 이름": "[ \" \" ]", "DeviceId": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 성공      | 장치에 대 한 로컬 로그인에 성공 했습니다.     | {"원격 주소": "?", "원격 포트": "", "로컬 포트": "", "로그인 셸": "/bin/su", "로그인 프로세스 Id": "28207", "사용자 이름": "공격자", "DeviceId": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 시도 실패  | 장치에 대 한 로컬 로그인 시도가 실패 했습니다. |    {"원격 주소": "?", "원격 포트": "", "로컬 포트": "", "로그인 셸": "/bin/su", "로그인 프로세스 Id": "22644", "사용자 이름": "공격자", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>장치 요약

지난 주에 검색 된 개별 보안 경고의 수를 IoT Hub, 장치, 경고 심각도, 경고 유형별로 그룹화 하 여 가져옵니다.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격 성공           | 9   |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 장치에서 로컬 로그인 시도 실패  | 242 |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 성공      | 31  |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 암호화 동전 마이너                     | 4   |

### <a name="iot-hub-summary"></a>IoT hub 요약

지난 주에 경고가 발생 한 여러 고유 장치를 선택 합니다. IoT Hub, 경고 심각도, 경고 유형

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 무차별 암호 대입 공격 성공           | 1          |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 장치에서 로컬 로그인 시도 실패  | 1          |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 장치에서 로컬 로그인 성공      | 1          |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 암호화 동전 마이너                     | 1          |

## <a name="security-recommendations"></a>보안 권장 사항

보안 권장 사항은 IoT 용 Defender 솔루션에 대해 구성 된 Log Analytics 작업 영역의 _AzureSecurityOfThings 권장 사항_ 테이블에 저장 됩니다.

보안 권장 사항 탐색을 시작 하는 데 도움이 되는 다양 한 유용한 쿼리를 제공 하 고 있습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드를 선택 합니다.

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 설명 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | 활성 | 입력 체인에서 허용 되는 방화벽 규칙을 찾았습니다. | 광범위 한 IP 주소 또는 포트에 대 한 허용 패턴이 포함 된 방화벽의 규칙이 있습니다. | {"Rules": "[{ \" SourceAddress \" : \" \" , \" sourceport \" : \" \" , \" destinationaddress \" : \" \" , \" destinationaddress \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | 활성 | 입력 체인에서 허용 되는 방화벽 규칙을 찾았습니다. | 광범위 한 IP 주소 또는 포트에 대 한 허용 패턴이 포함 된 방화벽의 규칙이 있습니다. | {"Rules": "[{ \" SourceAddress \" : \" \" , \" sourceport \" : \" \" , \" destinationaddress \" : \" \" , \" destinationaddress \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>장치 요약

IoT Hub, 장치, 권장 사항 심각도 및 유형별로 그룹화 된 고유한 활성 보안 권장 사항의 수를 가져옵니다.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 2   |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 1 |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 1  |
| /subscriptions/<subscription_id>/Sourceggg/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 4   |

## <a name="next-steps"></a>다음 단계

- IoT 용 Defender [개요](overview.md) 읽기
- IoT [아키텍처용](architecture.md) Defender에 대 한 자세한 정보
- [IoT 경고에 대 한 Defender](concept-security-alerts.md) 이해 및 탐색
- [IoT에 대 한 Defender 권장 사항](concept-recommendations.md) 이해 및 탐색