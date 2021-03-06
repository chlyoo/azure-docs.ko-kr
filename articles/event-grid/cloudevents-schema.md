---
title: CloudEvents 스키마에서 이벤트에 Azure Event Grid 사용
description: Azure Event Grid에서 이벤트에 CloudEvents 스키마를 사용하는 방법을 설명합니다. 이 서비스는 CloudEvents의 JSON 구현에서 이벤트를 지원 합니다.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 93e514e0eac40cfaa51d410a446608deca3cbd6d
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901405"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Event Grid에서 CloudEvents v1.0 스키마 사용
[기본 이벤트 스키마](event-schema.md) 외에, Azure Event Grid는 기본적으로 [CloudEvents v1.0의 JSON 구현](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) 및 [HTTP 프로토콜 바인딩](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)의 이벤트를 지원합니다. [CloudEvents](https://cloudevents.io/)는 이벤트 데이터를 설명하는 [공개 사양](https://github.com/cloudevents/spec/blob/v1.0/spec.md)입니다.

CloudEvents는 클라우드 기반 이벤트를 게시 및 사용 하기 위한 공통 이벤트 스키마를 제공 하 여 상호 운용성을 간소화 합니다. 이 스키마는 일관 된 도구, 이벤트 라우팅 및 처리 표준 방법, 외부 이벤트 스키마를 deserialize 하는 일반적인 방법 등을 허용 합니다. 공통 스키마를 통해 여러 플랫폼에서 작업을 보다 쉽게 통합할 수 있습니다.

CloudEvents는 [Cloud Native Computing Foundation](https://www.cncf.io/)을 통해 Microsoft를 포함한 여러 [협력자](https://github.com/cloudevents/spec/blob/master/community/contributors.md)가 작성하고 있습니다. 현재 버전 1.0으로 제공됩니다.

이 문서에서는 Event Grid에서 CloudEvents 스키마를 사용하는 방법을 설명합니다.

## <a name="cloudevent-schema"></a>CloudEvent 스키마

CloudEvents 형식의 Azure Blob Storage 이벤트에 대 한 예는 다음과 같습니다.

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

사용 가능한 필드, 해당 형식 및 정의에 대 한 자세한 설명은 [CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)v1.0을 참조 하세요.

`content-type`을 제외하고 CloudEvents 스키마 및 Event Grid 스키마에 배달된 이벤트에 대한 헤더 값은 동일합니다. CloudEvents 스키마의 경우 해당 헤더 값은입니다 `"content-type":"application/cloudevents+json; charset=utf-8"` . Event Grid 스키마의 경우 해당 헤더 값은입니다 `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>CloudEvents에 대한 Event Grid 구성

CloudEvents 스키마에서 이벤트의 입력 및 출력 모두에 Event Grid를 사용할 수 있습니다. 다음 표에서는 가능한 변환에 대해 설명 합니다.

 Event Grid 리소스 | 입력 스키마       | 배달 스키마
|---------------------|-------------------|---------------------
| 시스템 항목       | Event Grid 스키마 | Event Grid 스키마 또는 CloudEvent 스키마
| 사용자 토픽/도메인 | Event Grid 스키마 | Event Grid 스키마
| 사용자 토픽/도메인 | CloudEvent 스키마 | CloudEvent 스키마
| 사용자 토픽/도메인 | 사용자 지정 스키마     | 사용자 지정 스키마, Event Grid 스키마 또는 CloudEvent 스키마
| 항목 항목       | CloudEvent 스키마 | CloudEvent 스키마

모든 이벤트 스키마의 경우 Event Grid 항목에 게시할 때 및 이벤트 구독을 만들 때 유효성 검사가 필요 Event Grid.

자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.

### <a name="input-schema"></a>입력 스키마

사용자 지정 토픽을 만들 때 해당 사용자 지정 토픽의 입력 스키마를 설정합니다.

Azure CLI의 경우 다음을 사용 합니다.

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>출력 스키마입니다.

이벤트 구독을 만들 때 출력 스키마를 설정합니다.

Azure CLI의 경우 다음을 사용 합니다.

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

PowerShell의 경우 다음을 사용합니다.
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 현재는, 이벤트가 CloudEvents 스키마에 전달되는 경우 Azure Functions 앱에 Event Grid 트리거를 사용할 수 없습니다. HTTP 트리거를 사용합니다. CloudEvents 스키마에서 이벤트를 수신하는 HTTP 트리거를 구현하는 예제는 [Azure Functions에서 CloudEvents 사용](#azure-functions)을 참조하세요.

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v1.0을 사용한 엔드포인트 유효성 검사

Event Grid에 대해 잘 알고 있는 경우 남용 방지를 위한 끝점 유효성 검사 핸드셰이크를 알고 있을 수 있습니다. CloudEvents v 1.0은 HTTP OPTIONS 메서드를 사용 하 여 자체 [남용 방지 기능](webhook-event-delivery.md) 을 구현 합니다. 자세한 내용은 [이벤트 배달을 위한 HTTP 1.1 웹 후크-버전 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)를 참조 하세요. 출력에 CloudEvents 스키마를 사용 하는 경우 Event Grid Event Grid 유효성 검사 이벤트 메커니즘 대신 CloudEvents v1.0 남용 방지 기능을 사용 합니다.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Azure Functions와 함께 사용

[Azure Functions Event Grid 바인딩은](../azure-functions/functions-bindings-event-grid.md) 기본적으로 CloudEvents을 지원 하지 않으므로 HTTP로 트리거되는 함수를 사용 하 여 CloudEvents 메시지를 읽습니다. HTTP 트리거를 사용 하 여 CloudEvents를 읽는 경우 Event Grid 트리거가 자동으로 수행 하는 작업에 대 한 코드를 작성 해야 합니다.

* [구독 유효성 검사 요청](../event-grid/webhook-event-delivery.md) 에 유효성 검사 응답을 보냅니다.
* 요청 본문에 포함 된 이벤트 배열의 요소당 한 번 함수를 호출 합니다.

함수를 로컬로 호출 하거나 Azure에서 실행할 때 사용할 URL에 대 한 자세한 내용은 [HTTP 트리거 바인딩 참조 설명서](../azure-functions/functions-bindings-http-webhook.md)를 참조 하세요.

HTTP 트리거에 대한 다음 샘플 C# 코드는 Event Grid 트리거 동작을 시뮬레이트합니다. CloudEvents 스키마에 전달된 이벤트의 경우, 이 예제를 사용합니다.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

HTTP 트리거에 대한 다음 샘플 JavaScript 코드는 Event Grid 트리거 동작을 시뮬레이트합니다. CloudEvents 스키마에 전달된 이벤트의 경우, 이 예제를 사용합니다.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* [CloudEvents에](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)대해 테스트 하 고, 주석을 하 고, 참여 하는 것이 좋습니다.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
