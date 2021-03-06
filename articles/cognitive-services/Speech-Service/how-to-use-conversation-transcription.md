---
title: 실시간 대화 기록 빠른 시작-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK를 사용 하 여 실시간 대화 기록을 사용 하는 방법을 알아봅니다. 대화 기록을 통해 음성 서비스에 오디오를 스트리밍하 여 여러 참가자를 추가, 제거 및 식별할 수 있는 기능과 함께 모임 및 기타 대화를 높여줄 수 있습니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 48cd4c7996eabad7293aa2429c76b8943e0ab3da
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368475"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>실시간 대화 기록 시작

Speech SDK의 **ConversationTranscriber** API를 사용 하면 또는를 사용 하 여 음성 서비스에 오디오를 스트리밍하 여 여러 참가자를 추가, 제거 및 식별 하는 기능을 통해 모임 및 기타 대화를 높여줄 수 있습니다 `PullStream` `PushStream` . 먼저 REST API를 사용 하 여 각 참가자에 대 한 음성 서명을 만든 다음이 음성 서명을 SDK와 함께 사용 하 여 대화를 높여줄. 자세한 내용은 대화 내용 [개요](conversation-transcription.md) 를 참조 하세요.

## <a name="limitations"></a>제한 사항

* 다음 구독 지역 에서만 사용할 수 있습니다. `centralus` , `eastasia` , `eastus` , `westeurope`
* 7-mic 원형 다중 마이크 배열이 필요 합니다. 마이크 배열은 [사양을](./speech-devices-sdk-microphone.md)충족 해야 합니다.
* [음성 장치 SDK](speech-devices-sdk.md) 는 적절 한 장치 및 대화 기록을 보여 주는 샘플 앱을 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [비동기 대화](how-to-async-conversation-transcription.md) 
>  기록 [Roobo 장치 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  [Azure Kinect Dev Kit 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)