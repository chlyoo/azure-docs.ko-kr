---
title: Digital Twins 관리
titleSuffix: Azure Digital Twins
description: 개별 쌍 및 관계를 검색, 업데이트 및 삭제 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: cbab73a2fb3aecaacdfc92950c0d0b86edf775af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653349"
---
# <a name="manage-digital-twins"></a>Digital Twins 관리

사용자 환경의 엔터티는 [디지털](concepts-twins-graph.md)쌍으로 표현 됩니다. 디지털 쌍을 관리 하려면 만들기, 수정 및 제거를 포함할 수 있습니다. 이러한 작업을 수행 하기 위해 [**DigitalTwins api**](/rest/api/digital-twins/dataplane/twins), [.net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)또는 [Azure Digital twins CLI](how-to-use-cli.md)를 사용할 수 있습니다.

이 문서에서는 디지털 쌍을 관리 하는 방법을 중점적으로 설명 합니다. 관계 및 쌍 [그래프](concepts-twins-graph.md) 전체를 사용 하려면 [*방법: 관계를 사용*](how-to-manage-graph.md)하 여 쌍 그래프 관리를 참조 하세요.

> [!TIP]
> 모든 SDK 함수는 동기 및 비동기 버전으로 제공 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>쌍를 관리 하는 방법

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>디지털 쌍 만들기

쌍을 만들려면 `CreateOrReplaceDigitalTwinAsync()` 서비스 클라이언트에서 다음과 같이 메서드를 사용 합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

디지털 쌍을 만들려면 다음을 제공 해야 합니다.
* 디지털 쌍의 원하는 ID입니다.
* 사용 하려는 [모델](concepts-models.md)

필요에 따라 디지털 쌍의 모든 속성에 대해 초기 값을 제공할 수 있습니다. 속성은 선택 사항으로 처리 되 고 나중에 설정할 수 있지만 **설정 될 때까지 쌍의 일부로 표시 되지 않습니다.**

>[!NOTE]
>쌍 속성을 초기화할 필요가 없지만 쌍을 만들 때 쌍의 모든 [구성 요소](concepts-models.md#elements-of-a-model) 를 **설정 해야 합니다** . 비어 있는 개체 일 수 있지만 구성 요소 자체는 존재 해야 합니다.

모델과 모든 초기 속성 값은 `initData` 관련 데이터를 포함 하는 JSON 문자열인 매개 변수를 통해 제공 됩니다. 이 개체를 구조화 하는 방법에 대 한 자세한 내용은 다음 섹션을 계속 진행 하세요.

> [!TIP]
> 쌍을 만들거나 업데이트 한 후에는 변경 내용이 [쿼리에](how-to-query-graph.md)반영 될 때까지 최대 10 초의 대기 시간이 있을 수 있습니다. 이 `GetDigitalTwin` 문서의 뒷부분에서 설명 하는 api ( [이 문서의 뒷부분에서](#get-data-for-a-digital-twin)설명)는 이러한 지연 시간을 발생 하지 않으므로 새로 만든 twins를 확인 하려면 쿼리 대신 api 호출을 사용 합니다. 

### <a name="initialize-model-and-properties"></a>모델 및 속성 초기화

쌍이 생성 될 때 쌍의 속성을 초기화할 수 있습니다. 

쌍 생성 API는 쌍 속성의 유효한 JSON 설명으로 직렬화 된 개체를 허용 합니다. 쌍의 JSON 형식에 대 한 설명은 [*개념: Digital 쌍 및 쌍 그래프*](concepts-twins-graph.md) 를 참조 하세요. 

먼저 쌍 및 해당 속성 데이터를 나타내는 데이터 개체를 만들 수 있습니다. 매개 변수 개체는 수동으로 만들거나 제공 된 도우미 클래스를 사용 하 여 만들 수 있습니다. 다음은 각각의 예제입니다.

#### <a name="create-twins-using-manually-created-data"></a>수동으로 만든 데이터를 사용 하 여 쌍 만들기

사용자 지정 도우미 클래스를 사용 하지 않으면에서 쌍의 속성을 나타낼 수 있습니다 `Dictionary<string, object>` . 여기서은 `string` 속성의 이름이 고는 `object` 속성 및 해당 값을 나타내는 개체입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>도우미 클래스를 사용 하 여 쌍 만들기

의 도우미 클래스를 `BasicDigitalTwin` 사용 하 여 속성 필드를 "쌍" 개체에 직접 저장할 수 있습니다. 를 사용 하 여 속성 목록을 빌드할 수 있습니다 `Dictionary<string, object>` . 그러면이 개체를 직접 쌍으로 개체에 추가할 수 있습니다 `CustomProperties` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` 개체는 필드와 함께 제공 `Id` 됩니다. 이 필드는 비워 둘 수 있지만 ID 값을 추가 하는 경우 호출에 전달 된 ID 매개 변수와 일치 해야 `CreateOrReplaceDigitalTwinAsync()` 합니다. 다음은 그 예입니다. 
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>디지털 쌍에 대 한 데이터 가져오기

다음과 같이 메서드를 호출 하 여 디지털 쌍의 세부 정보에 액세스할 수 있습니다 `GetDigitalTwin()` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

이 호출은와 같은 강력한 형식의 개체 형식으로 쌍으로 된 데이터를 반환 `BasicDigitalTwin` 합니다. `BasicDigitalTwin` 는 SDK에 포함 된 serialization 도우미 클래스로,이 클래스는 미리 구문 분석 된 형식으로 핵심 쌍 메타 데이터 및 속성을 반환 합니다. 다음은이를 사용 하 여 쌍 세부 정보를 보는 방법에 대 한 예입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

메서드를 사용 하 여 쌍을 검색할 때 한 번 이상 설정 된 속성만 반환 됩니다 `GetDigitalTwin()` .

>[!TIP]
>쌍 `displayName` 에 대 한는 해당 모델 메타 데이터의 일부 이므로 쌍 인스턴스에 대 한 데이터를 가져올 때 표시 되지 않습니다. 이 값을 확인 하려면 [모델에서 검색할](how-to-manage-model.md#retrieve-models)수 있습니다.

단일 API 호출을 사용 하 여 여러 쌍을 검색 하려면 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)의 쿼리 API 예를 참조 하세요.

*초승달* 을 정의 하는 다음 모델 ( [Dtdl (디지털 Twins 정의 언어](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL))로 작성 됨)을 고려 합니다.

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

`object result = await client.GetDigitalTwinAsync("my-moon");` *초승달* 형식 쌍에 대 한 호출 결과는 다음과 같을 수 있습니다.

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

디지털 쌍의 정의 된 속성은 디지털 쌍의 최상위 속성으로 반환 됩니다. DTDL 정의의 일부가 아닌 메타 데이터 또는 시스템 정보는 접두사와 함께 반환 됩니다 `$` . 메타 데이터 속성은 다음과 같습니다.
* 이 Azure Digital Twins 인스턴스의 디지털 쌍 ID `$dtId` 입니다.
* `$etag`-웹 서버에서 할당 한 표준 HTTP 필드입니다.
* 섹션의 기타 속성 `$metadata` 여기에는 다음이 포함됩니다.
    - 디지털 쌍 모델의 DTMI입니다.
    - 쓰기 가능한 각 속성의 동기화 상태입니다. 장치에 가장 유용 합니다 .이는 서비스와 장치에 분기 된 상태가 있을 수 있습니다 (예: 장치가 오프 라인 상태인 경우). 현재이 속성은 IoT Hub에 연결 된 물리적 장치에만 적용 됩니다. 메타 데이터 섹션의 데이터를 사용 하 여 마지막 수정 타임 스탬프 뿐만 아니라 속성의 전체 상태를 이해할 수 있습니다. 동기화 상태에 대 한 자세한 내용은 장치 상태 동기화에 대 한 [이 IoT Hub 자습서](../iot-hub/tutorial-device-twins.md) 를 참조 하세요.
    - IoT Hub 또는 Azure Digital Twins와 같은 서비스별 메타 데이터. 

`BasicDigitalTwin` [*방법: Azure Digital Twins Api 및 sdk 사용*](how-to-use-apis-sdks.md)에서와 같은 serialization 도우미 클래스에 대해 자세히 알아볼 수 있습니다.

## <a name="view-all-digital-twins"></a>모든 디지털 쌍 보기

인스턴스의 모든 디지털 쌍을 보려면 [쿼리](how-to-query-graph.md)를 사용 합니다. 쿼리 [api](/rest/api/digital-twins/dataplane/query) 또는 [CLI 명령을](how-to-use-cli.md)사용 하 여 쿼리를 실행할 수 있습니다.

인스턴스의 모든 디지털 쌍 목록을 반환 하는 기본 쿼리의 본문은 다음과 같습니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>디지털 쌍 업데이트

디지털 쌍의 속성을 업데이트 하려면 [JSON 패치](http://jsonpatch.com/) 형식으로 바꿀 정보를 작성 합니다. 이러한 방식으로 여러 속성을 한 번에 바꿀 수 있습니다. 그런 다음 JSON 패치 문서를 메서드에 전달 합니다 `UpdateDigitalTwin()` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Patch 호출은 모든 속성을 원하는 대로 단일 쌍으로 업데이트할 수 있습니다. 여러 쌍에서 속성을 업데이트 해야 하는 경우에는 각 쌍에 대해 별도의 업데이트 호출이 필요 합니다.

> [!TIP]
> 쌍을 만들거나 업데이트 한 후에는 변경 내용이 [쿼리에](how-to-query-graph.md)반영 될 때까지 최대 10 초의 대기 시간이 있을 수 있습니다. 이 `GetDigitalTwin` [문서 앞부분에서](#get-data-for-a-digital-twin)설명 하는 api는 이러한 지연 시간을 발생 하지 않으므로 즉각적인 응답이 필요한 경우 쿼리 대신 api 호출을 사용 하 여 새로 업데이트 된 쌍를 확인 합니다. 

다음은 JSON 패치 코드의 예제입니다. 이 문서는 적용 되는 디지털 쌍의 *mass* 및 *radius* 속성 값을 대체 합니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Azure .NET SDK의 [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument?view=azure-dotnet&preserve-view=true)를 사용 하 여 패치를 만들 수 있습니다. 다음은 예제입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>디지털 쌍 구성 요소의 속성 업데이트

모델에 구성 요소가 포함 되어 있을 수 있으므로 다른 모델로 구성 될 수 있습니다. 

디지털 쌍의 구성 요소에서 속성을 패치 하려면 JSON 패치에서 경로 구문을 사용할 수 있습니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>디지털 쌍의 모델 업데이트

`UpdateDigitalTwin()`함수를 사용 하 여 디지털 쌍을 다른 모델로 마이그레이션할 수도 있습니다. 

예를 들어 디지털 쌍의 메타 데이터 필드를 대체 하는 다음과 같은 JSON 패치 문서를 살펴보세요 `$model` .

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

이 작업은 패치로 수정 되는 디지털 쌍이 새 모델을 준수 하는 경우에만 성공 합니다. 

다음 예제를 살펴보겠습니다.
1. *Foo_old* 모델을 사용 하는 디지털 쌍을 생각해 보세요. *foo_old* 은 필요한 속성 *질량* 을 정의 합니다.
2. 새 모델 *foo_new* 는 속성 질량을 정의 하 고 새 필수 속성 *온도* 를 추가 합니다.
3. 패치 후에 디지털 쌍에는 질량 속성과 온도 속성이 모두 있어야 합니다. 

이 상황에 대 한 패치는 모델과 쌍의 온도 속성을 모두 업데이트 해야 합니다. 예를 들면 다음과 같습니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>충돌 하는 업데이트 호출 처리

Azure Digital Twins는 들어오는 모든 요청이 차례로 처리 되도록 합니다. 즉, 여러 함수가 쌍의 동일한 속성을 동시에 업데이트 하려고 하는 경우에도 충돌을 처리 하기 위해 명시적인 잠금 코드를 작성할 **필요가 없습니다** .

이 동작은 쌍 단위로 발생 합니다. 

예를 들어 이러한 세 호출이 동시에 도착 하는 시나리오를 가정해 보겠습니다. 
*   *Twin1* 에 속성 A 쓰기
*   *Twin1* 에 속성 B 쓰기
*   *Twin2* 에 속성 A 쓰기

*Twin1* 를 수정 하는 두 호출은 한 번 실행 되며 변경 내용에 대 한 변경 메시지가 생성 됩니다. *Twin2* 를 수정 하는 호출은 충돌 없이 동시에 실행 될 수 있습니다 (도착 하는 즉시).

## <a name="delete-a-digital-twin"></a>디지털 쌍 삭제

메서드를 사용 하 여 쌍을 삭제할 수 있습니다 `DeleteDigitalTwin()` . 그러나 더 이상 관계가 없는 경우에만 쌍을 삭제할 수 있습니다. 따라서 쌍의 들어오고 나가는 관계를 먼저 삭제 합니다.

다음은 쌍 및 해당 관계를 삭제 하는 코드의 예입니다. `DeleteDigitalTwin`SDK 호출은 더 광범위 한 예제 컨텍스트에서 어디에 속하는지 명확 하 게 강조 표시 됩니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>모든 디지털 쌍 삭제

한 번에 모든 쌍를 삭제 하는 방법에 대 한 예제는 [*자습서: 샘플 클라이언트 앱을 사용 하 여 기본 사항 탐색*](tutorial-command-line-app.md)에서 사용한 샘플 앱을 다운로드 합니다. *CommandLoop.cs* 파일은 함수에서이를 수행 `CommandDeleteAllTwins()` 합니다.

## <a name="runnable-digital-twin-code-sample"></a>실행 가능한 디지털 쌍 코드 샘플

아래 실행 가능한 코드 샘플을 사용 하 여 쌍을 만들고, 해당 세부 정보를 업데이트 하 고, 쌍을 삭제할 수 있습니다. 

### <a name="set-up-the-runnable-sample"></a>실행 가능한 샘플 설정

이 코드 조각은 [*자습서: 샘플 클라이언트 앱을 사용 하 여 Azure Digital Twins 탐색*](tutorial-command-line-app.md)의 모델 정의 [에 대 한Room.js](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) 를 사용 합니다. 이 링크를 사용 하 여 파일로 직접 이동 하거나 [여기](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)에서 전체 종단 간 샘플 프로젝트의 일부로 다운로드할 수 있습니다.

샘플을 실행 하기 전에 다음을 수행 합니다.
1. 모델 파일을 다운로드 하 고 프로젝트에 배치한 다음 `<path-to>` 아래 코드의 자리 표시자를 바꿔서 프로그램에서 찾을 위치를 알려 줍니다.
2. 자리 표시자를 `<your-instance-hostname>` Azure 디지털 Twins 인스턴스의 호스트 이름으로 바꿉니다.
3. Azure Digital Twins를 사용 하는 데 필요한 두 개의 종속성을 프로젝트에 추가 합니다. 첫 번째는 [.NET용 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)의 패키지이고, 두 번째는 Azure에 대한 인증을 지원하는 도구를 제공합니다.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

또한 샘플을 직접 실행 하려면 로컬 자격 증명을 설정 해야 합니다. 다음 섹션에서는이 과정을 안내 합니다.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>샘플 실행

위의 단계를 완료 한 후에는 다음 샘플 코드를 직접 실행할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

위의 프로그램의 콘솔 출력은 다음과 같습니다. 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="쌍을 만들고, 업데이트 하 고, 삭제 하는 것을 보여 주는 콘솔 출력" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>다음 단계

디지털 쌍 간의 관계를 만들고 관리 하는 방법을 참조 하세요.
* [*방법: 관계로 쌍 그래프 관리*](how-to-manage-graph.md)