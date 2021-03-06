---
title: Azure Functions에 대 한 RabbitMQ 출력 바인딩
description: Azure Functions에서 RabbitMQ 메시지를 보내는 방법에 대해 알아봅니다.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1664656f82492e664b7574339893cd688f0a061d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097316"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Azure Functions 개요에 대 한 RabbitMQ 출력 바인딩

> [!NOTE]
> RabbitMQ 바인딩은 **프리미엄 및 전용** 계획 에서만 완전 하 게 지원 됩니다. 소비는 지원 되지 않습니다.

RabbitMQ 출력 바인딩을 사용 하 여 RabbitMQ 큐로 메시지를 보냅니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-rabbitmq-output.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 메서드 반환 값을 출력으로 사용 하 여 5 분 마다 RabbitMQ 메시지를 전송 하는 [c # 함수](functions-dotnet-class-library.md) 를 보여 줍니다.

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

다음 예에서는 IAsyncCollector 인터페이스를 사용 하 여 메시지를 보내는 방법을 보여 줍니다.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

다음 예제에서는 메시지를 POCOs로 보내는 방법을 보여 줍니다.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예제에서는 *function.js파일의* RabbitMQ 출력 바인딩과 바인딩을 사용 하는 [c # 스크립트 함수](functions-reference-csharp.md) 를 보여 줍니다. 함수는 HTTP 트리거에서 메시지를 읽고 RabbitMQ 큐에 출력 합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

C# 스크립트 코드는 다음과 같습니다.

```C#
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예제에서는 *function.js파일의* RabbitMQ 출력 바인딩과 바인딩을 사용 하는 [JavaScript 함수](functions-reference-node.md) 를 보여 줍니다. 함수는 HTTP 트리거에서 메시지를 읽고 RabbitMQ 큐에 출력 합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 *function.js파일의* RabbitMQ 출력 바인딩과 바인딩을 사용 하는 Python 함수를 보여 줍니다. 함수는 HTTP 트리거에서 메시지를 읽고 RabbitMQ 큐에 출력 합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

*_\__ \_ Py* 에서:

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

다음 Java 함수는 `@RabbitMQOutput` [java RabbitMQ 형식의](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) 주석을 사용 하 여 RabbitMQ queue 출력 바인딩에 대 한 구성을 설명 합니다. 함수는 5 분 마다 타이머 트리거를 통해 트리거되는 경우 RabbitMQ 큐에 메시지를 보냅니다.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C # 클래스 라이브러리](functions-dotnet-class-library.md)에서 [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs)를 사용 합니다.

`RabbitMQAttribute`메서드 시그니처의 특성은 다음과 같습니다.

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

전체 예제는 c # [예제](#example)를 참조 하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`RabbitMQOutput`주석을 사용 하면 RabbitMQ 메시지를 보낼 때 실행 되는 함수를 만들 수 있습니다. 사용 가능한 구성 옵션에는 큐 이름 및 연결 문자열 이름이 있습니다. 추가 매개 변수 세부 정보를 보려면 [RabbitMQOutput Java 주석을](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java)참조 하세요.

자세한 내용은 출력 바인딩 [예제](#example) 를 참조 하세요.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `RabbitMQ` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "RabbitMQ"로 설정 해야 합니다.|
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. |
|**name** | 해당 없음 | 함수 코드에서 큐를 나타내는 변수의 이름입니다. |
|**queueName**|**QueueName**| 메시지를 보낼 큐의 이름입니다. |
|**n**|**HostName**|(ConnectStringSetting을 사용 하는 경우 무시 됨) <br>큐의 호스트 이름 (예: 10.26.45.210)|
|**userName**|**UserName**|(ConnectionStringSetting를 사용 하는 경우 무시 됨) <br>큐에 액세스 하는 데 사용할 사용자 이름이 포함 된 앱 설정의 이름입니다. 예: UserNameSetting: "< UserNameFromSettings >"|
|**password**|**암호**|(ConnectionStringSetting를 사용 하는 경우 무시 됨) <br>큐에 액세스 하기 위한 암호를 포함 하는 앱 설정의 이름입니다. 예: UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|RabbitMQ message queue 연결 문자열이 포함 된 앱 설정의 이름입니다. local.settings.js에서 앱 설정을 통해서가 아니라 직접 연결 문자열을 지정 하는 경우에는 트리거가 작동 하지 않습니다. (예: *function.json*: connectionStringSetting: "rabbitMQConnection" <br> *local.settings.js*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**port**|**포트**|(ConnectionStringSetting를 사용 하는 경우 무시 됨) 사용 되는 포트를 가져오거나 설정 합니다. 기본값은 rabbitmq client의 기본 포트 설정인 5672을 가리키는 0입니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

출력 바인딩에 대해 다음 매개 변수 형식을 사용 합니다.

* `byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `POCO` -매개 변수 값이 c # 개체로 형식이 지정 되지 않은 경우 오류가 수신 됩니다. 전체 예제는 c # [예제](#example)를 참조 하세요.

C # 함수를 사용 하는 경우:

* 비동기 함수에는 매개 변수 대신 반환 값이 필요 `IAsyncCollector` `out` 합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

출력 바인딩에 대해 다음 매개 변수 형식을 사용 합니다.

* `byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `POCO` -매개 변수 값이 c # 개체로 형식이 지정 되지 않은 경우 오류가 수신 됩니다. 전체 예제는 c # 스크립트 [예제](#example)를 참조 하세요.

C # 스크립트 함수를 사용 하는 경우:

* 비동기 함수에는 매개 변수 대신 반환 값이 필요 `IAsyncCollector` `out` 합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

큐 메시지는 컨텍스트별를 통해 사용할 수 있습니다.<NAME> 여기서는 <NAME> function.js에 정의 된 이름과 일치 합니다. 페이로드가 JSON 인 경우 값은 개체로 deserialize 됩니다.

# <a name="python"></a>[Python](#tab/python)

Python [예](#example)를 참조 하세요.

# <a name="java"></a>[Java](#tab/java)

출력 바인딩에 대해 다음 매개 변수 형식을 사용 합니다.

* `byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `POJO` -매개 변수 값이 Java 개체로 형식이 지정 되지 않은 경우 오류가 수신 됩니다.

---

## <a name="next-steps"></a>다음 단계

- [RabbitMQ 메시지가 생성 될 때 함수 실행 (트리거)](./functions-bindings-rabbitmq-trigger.md)
