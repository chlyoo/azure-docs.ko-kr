---
title: 텍스트 분석 API의 새로운 기능
titleSuffix: Text Analytics - Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Text Analytics의 새로운 릴리스 및 기능에 대 한 정보를 제공 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 3205e96bca6ce13afdfe06fede1112e6ddb1ab39
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653920"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Text Analytics API의 새로운 기능

텍스트 분석 API는 지속적으로 업데이트 됩니다. 최신 개발을 최신 상태로 유지 하기 위해이 문서에서는 새로운 릴리스 및 기능에 대 한 정보를 제공 합니다.

## <a name="february-2021"></a>2021년 2월

* S0 ~ S4 가격 책정 계층은 2021 년 3 월 8 일에 사용 중지 됩니다. S0 ~ S4 가격 책정 계층을 사용 하는 기존 Text Analytics 리소스가 있는 경우 표준 [가격 책정 계층](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)을 사용 하도록 업데이트 해야 합니다.

## <a name="january-2021"></a>2021년 1월

* 에서 `2021-01-15` 제공 하는 [명명 된 엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md) v3. x에 대 한 모델 버전 
  * [몇 가지 일반 엔터티 범주](named-entity-types.md)에 대 한 확장 된 언어 지원 
  * 지원 되는 모든 v3 언어에 대 한 일반 엔터티 범주의 향상 된 AI 품질 

* `2021-01-05` [언어 검색](how-tos/text-analytics-how-to-language-detection.md)에 대 한 모델 버전으로, 추가 [언어 지원을](language-support.md?tabs=language-detection)제공 합니다.

이러한 모델 버전은 현재 미국 동부 지역에서 사용할 수 없습니다. 

> [!div class="nextstepaction"]
> [새 NER 모델에 대 한 자세한 정보](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>2020년 12월

* 텍스트 분석 API에 대 한 [업데이트 된 가격](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) 정보

## <a name="november-2020"></a>2020년 11월

* NER, PII 및 키 구 추출 작업을 위한 일괄 처리를 지 원하는 새로운 비동기 [분석 API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)에 대 한 텍스트 분석 API v 3.1-preview. 3의 [새 끝점](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) 입니다.
* 일괄 처리를 지 원하는 상태 호스팅 API [에 대 한](how-tos/text-analytics-for-health.md) 새로운 비동기 Text Analytics에 대 한 텍스트 분석 API v 3.1-preview. 3의 [새 끝점](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) 입니다.
* 위에 나열 된 새 기능은 `West US 2` ,, `East US 2` `Central US` `North Europe` 및 `West Europe` 지역 에서만 사용할 수 있습니다.
* 포르투갈어 (브라질) `pt-BR` 는 이제 모델 버전부터 실행 되는 [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x에서 지원 됩니다 `2020-04-01` . 이는 `pt-PT` 포르투갈어에 대 한 기존 지원을 추가 합니다.
* 비동기 분석 및 상태 작업 Text Analytics를 포함 하는 업데이트 된 클라이언트 라이브러리입니다. GitHub에 대 한 예제를 찾을 수 있습니다.

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)


> [!div class="nextstepaction"]
> [텍스트 분석 API v 3.1-미리 보기에 대해 자세히 알아보세요. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>2020년 10월

* 모델 버전부터 시작 하 여 감정 분석 v3. x에 대 한 힌디어 지원 `2020-04-01` . 
* 향상 된 `2020-09-01` 언어 검색 및 정확도 기능이 추가 된 v3/언어 끝점에 대 한 모델 버전입니다.
* 인도 중부 및 아랍에미리트 북부의 v3 가용성.

## <a name="september-2020"></a>2020년 9월

### <a name="general-api-updates"></a>일반 API 업데이트

* 다음 명명 된 엔터티 인식 v3 끝점에 대 한 업데이트를 지원 하기 위해 Text Analytics v 3.1 공개 미리 보기에 대 한 새 URL을 해제 합니다. 
    * `/pii` 이제 끝점 `redactedText` 은 입력 텍스트에서 검색 된 PII 엔터티가 `*` 해당 엔터티의 각 문자에 대해로 대체 되는 응답 JSON에 새로운 속성을 포함 합니다.
    * `/linking` 이제 끝점은 `bingID` 연결 된 엔터티에 대 한 응답 JSON에 속성을 포함 합니다.
* 다음 Text Analytics preview API 끝점은 2020 년 9 월 4 일에 사용이 중지 되었습니다.
    * v 2.1-미리 보기
    * v3.0-미리 보기
    * v. 3.0-preview. 1
    
> [!div class="nextstepaction"]
> [텍스트 분석 API v 3.1-미리 보기에 대해 자세히 알아보세요. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>상태 컨테이너 업데이트에 대 한 Text Analytics

다음 업데이트는 상태 컨테이너에 대 한 Text Analytics 9 월 릴리스에만 적용 됩니다.
* 새 모델 버전의 태그가 있는 새 컨테이너 이미지가 `1.1.013530001-amd64-preview` `2020-09-03` 컨테이너 미리 보기 리포지토리에 릴리스 되었습니다. 
* 이 모델 버전은 엔터티 인식, 약어 검색 및 대기 시간 향상의 향상 된 기능을 제공 합니다.

> [!div class="nextstepaction"]
> [상태 Text Analytics에 대 한 자세한 정보](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>2020년 8월

### <a name="general-api-updates"></a>일반 API 업데이트

* `2020-07-01`V3 및 끝점에 대 한 모델 버전은 `/keyphrases` `/pii` 다음을 추가 합니다 `/languages` .
    * 명명 된 엔터티 인식을 위한 추가 정부 및 국가 별 [엔터티 범주](named-entity-types.md?tabs=personal) 입니다.
    * 감정 분석 v3의 노르웨이어 및 터키어 지원.
* 이제 게시 된 [데이터 제한을](concepts/data-limits.md)초과 하는 v3 API 요청에 대해 HTTP 400 오류가 반환 됩니다. 
* 이제 오프셋을 반환 하는 끝점은 선택적 `stringIndexType` 매개 변수를 지원 하며,이 매개 변수는 반환 된 `offset` 및 `length` 값을 지원 되는 [문자열 인덱스 체계](concepts/text-offsets.md)와 일치 하도록 조정 합니다.

### <a name="text-analytics-for-health-container-updates"></a>상태 컨테이너 업데이트에 대 한 Text Analytics

다음 업데이트는 상태 컨테이너에 대해서만 Text Analytics의 8 월 릴리스와 관련 됩니다.

* 새 모델-상태 Text Analytics의 버전: `2020-07-24`
* 상태 요청에 대 한 Text Analytics를 보내기 위한 새 URL: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (이 새 컨테이너 이미지에 포함 된 데모 웹 앱을 사용 하기 위해 브라우저 캐시를 지우도록 요구 됩니다.)

JSON 응답의 다음 속성이 변경 되었습니다.

* `type`는 `category`로 이름이 변경되었습니다. 
* `score`는 `confidenceScore`로 이름이 변경되었습니다.
* `category`JSON 출력의 필드에 있는 엔터티는 이제 파스칼식 대/소문자로 되어 있습니다. 다음 엔터티의 이름이 변경 되었습니다.
    * `EXAMINATION_RELATION` 이름이 `RelationalOperator`으로 바뀌었습니다.
    * `EXAMINATION_UNIT` 이름이 `MeasurementUnit`으로 바뀌었습니다.
    * `EXAMINATION_VALUE` 이름이 `MeasurementValue`으로 바뀌었습니다.
    * `ROUTE_OR_MODE` 의 이름이 변경 되었습니다 `MedicationRoute` .
    * 관계형 엔터티의 `ROUTE_OR_MODE_OF_MEDICATION` 이름이로 바뀌었습니다 `RouteOfMedication` .

추가 된 엔터티는 다음과 같습니다.

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* 관계 추출
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [상태 컨테이너 Text Analytics에 대 한 자세한 정보](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>2020년 7월 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>상태 컨테이너에 대 한 Text Analytics-공용 제어 된 미리 보기

상태 컨테이너에 대 한 Text Analytics는 이제 환자 흡입구 양식, 의사 (정보), 연구 용지 및 방전 요약 등의 임상 문서에서 구조화 되지 않은 영어 텍스트의 정보를 추출할 수 있는 공용 제어 미리 보기입니다. 현재 상태 컨테이너 사용에 대 한 Text Analytics에 대해서는 요금이 청구 되지 않습니다.

컨테이너는 다음과 같은 기능을 제공 합니다.

* 명명된 엔터티 인식
* 관계 추출
* 엔터티 연결
* 부정

## <a name="may-2020"></a>2020년 5월

### <a name="text-analytics-api-v3-general-availability"></a>텍스트 분석 API v3 일반 공급

이제 텍스트 분석 API v3은 다음과 같은 업데이트를 사용 하 여 일반 공급 됩니다.

* 모델 버전 `2020-04-01`
* 각 기능에 대 한 새로운 [데이터 제한](concepts/data-limits.md)
* [SA (감정 분석) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) 의 업데이트 된 [언어 지원](language-support.md)
* 엔터티 링크를 위한 별도의 끝점 
* [NER (명명 된 엔터티 인식) v3](how-tos/text-analytics-how-to-entity-linking.md)의 새 "Address" 엔터티 범주입니다.
* NER v3의 새 하위 범주:
   * 위치-지리적 위치
   * 위치-구조적
   * 조직-주식 교환
   * 조직-의료
   * 조직-스포츠
   * 이벤트-문화권
   * 이벤트-자연
   * 이벤트-스포츠

JSON 응답의 다음 속성이 추가 되었습니다.
   * `SentenceText` 감정 분석에서
   * `Warnings` 각 문서에 대해 

JSON 응답에서 다음 속성의 이름이 변경 되었습니다 (해당 하는 경우).

* `score`는 `confidenceScore`로 이름이 변경되었습니다.
    * `confidenceScore` 에는 두 개의 소수점 자릿수가 있습니다. 
* `type`는 `category`로 이름이 변경되었습니다.
* `subtype`는 `subcategory`로 이름이 변경되었습니다.

> [!div class="nextstepaction"]
> [텍스트 분석 API v 3에 대 한 자세한 정보](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>텍스트 분석 API v 3.1 공개 미리 보기
   * 새 감정 분석 기능- [의견 마이닝](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * `PII`보호 된 상태 정보 ()에 대 한 새 개인 () 도메인 필터 `PHI` 입니다.

> [!div class="nextstepaction"]
> [텍스트 분석 API v 3.1 Preview에 대 한 자세한 정보](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020년 2월

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>텍스트 분석 API v3 공개 미리 보기에 대 한 SDK 지원

[통합 된 AZURE sdk 릴리스의](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)일부로 텍스트 분석 API v3 SDK는 이제 다음과 같은 프로그래밍 언어에 대 한 공개 미리 보기로 제공 됩니다.
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript(Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [텍스트 분석 API v3 SDK에 대해 자세히 알아보기](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>명명 된 엔터티 인식 v3 공개 미리 보기

이제 추가 엔터티 형식은 텍스트에 있는 일반 및 개인 정보 엔터티 검색을 확장할 때 NER (명명 된 엔터티 인식) v3 공개 미리 보기 서비스에서 사용할 수 있습니다. 이 업데이트 [](concepts/model-versioning.md) `2020-02-01` 에서는 다음을 포함 하는 모델 버전을 소개 합니다.

* 다음과 같은 일반 엔터티 형식 인식 (영어만 해당):
    * PersonType
    * 제품
    * 이벤트
    * 지정 학적 엔터티 (GPE)를 위치 아래의 하위 형식으로
    * 기술

* 다음 개인 정보 엔터티 형식 인식 (영어만 해당):
    * Person
    * 조직
    * 수량 아래의 하위 형식으로 사용 기간
    * DateTime 아래의 하위 형식으로 날짜
    * Email 
    * 전화 번호 (미국에만 해당)
    * URL
    * IP 주소

### <a name="october-2019"></a>2019년 10월

#### <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

* 개인 정보 엔터티 형식을 인식 하기 위한 [새 끝점](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) (영어만 해당)

* [엔터티 인식](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) 및 [엔터티 링크](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)를 위한 별도의 끝점.

* [](concepts/model-versioning.md) `2019-10-01` 다음을 포함 하는 모델 버전:
    * 텍스트에 있는 엔터티의 확장 된 검색 및 분류 
    * 다음과 같은 새로운 엔터티 유형을 인식 합니다.
        * 전화 번호
        * IP 주소

엔터티 연결은 영어와 스페인어를 지원 합니다. NER 언어 지원은 엔터티 형식에 따라 달라 집니다.

#### <a name="sentiment-analysis-v3-public-preview"></a>감정 분석 v3 공개 미리 보기

* 감정 분석을 위한 [새 끝점](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) 입니다.
* [](concepts/model-versioning.md) `2019-10-01` 다음을 포함 하는 모델 버전:

    * API의 텍스트 분류 및 점수 매기기의 정확성과 세부 정보에 상당한 기능이 향상 되었습니다.
    * 텍스트의 다른 정서에 대 한 자동 레이블 지정
    * 문서 및 문장 수준에서 분석 및 출력을 감정 합니다. 

영어 ( `en` ), 일본어 ( `ja` ), 중국어 간체 ( `zh-Hans` ), 중국어 번체 ( `zh-Hant` ), 프랑스어 (), 이탈리아어 (), 스페인어 (), 네덜란드어 (), 포르투갈어 () 및 독일어 ()를 지원 하며,,,,,,,,,, `fr` `it` `es` `nl` `pt` `de` `Australia East` `Central Canada` `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` 및 `West US 2` 지역에서 사용할 수 있습니다. 

> [!div class="nextstepaction"]
> [감정 분석 v 3에 대 한 자세한 정보](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>다음 단계

* [텍스트 분석 API 이란?](overview.md)  
* [사용자 시나리오 예](text-analytics-user-scenarios.md)
* [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [언어 감지](how-tos/text-analytics-how-to-language-detection.md)
* [엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md)
* [핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md)
