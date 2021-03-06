---
title: Azure Cognitive Search의 새로운 기능
description: 서비스 이름을 Azure Search에서 Azure Cognitive Search로 변경하는 것을 포함하여 새로운 기능과 향상된 기능에 대한 공지 사항입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 02/09/2021
ms.custom: references_regions
ms.openlocfilehash: 13cb22c178be29af71b57d0f50fdbd0e95718069
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104606"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 서비스를 최신 상태로 유지하려면 이 페이지에 대한 책갈피를 지정하세요. [미리 보기 기능 목록](search-api-preview.md)을 확인하여 공개 미리 보기의 기능을 봅니다.

## <a name="february-2021"></a>2021년 2월

|기능&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | 가용성  |
|------------------------------|---------------|---------------|
| [문서 다시 설정(미리 보기)](search-howto-run-reset-indexers.md) |  인덱서 워크로드에서 개별적으로 선택한 검색 문서를 다시 처리합니다. | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [가용성 영역](search-performance-optimization.md#availability-zones)| 둘 이상의 복제본이 [이 문서](search-performance-optimization.md#availability-zones)에 나열된 특정 지역에 있는 검색 서비스는 복제본을 둘 이상의 고유한 물리적 위치에 저장하여 복원력을 얻습니다.  | 검색 서비스를 만든 지역 및 날짜는 가용성을 결정합니다. 자세한 내용은 성능 튜닝 문서를 참조하세요. |

## <a name="january-2021"></a>2021년 1월

|기능&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | 가용성  |
|------------------------------|-------------|---------------|
| [Azure Cognitive Search 및 QnA Maker용 솔루션 가속기](https://github.com/Azure-Samples/search-qna-maker-accelerator) | 문서에서 질문과 답변을 끌어오고 관련성이 가장 높은 답변을 제안합니다. 라이브 데모 앱은 [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo)에서 찾을 수 있습니다.  | 오픈 소스 프로젝트(SLA 없음) |

## <a name="2020-archive"></a>2020 보관

| 월 | 기능 | 설명 |
|-------|---------|-------------|
| 11월 | [고객 관리형 키 암호화(확장)](search-security-manage-encryption-keys.md) | 검색 서비스에서 만들고 관리하는 자산의 전체 범위에 대해 고객 관리형 암호화를 확장합니다. 일반 공급.|
| 9월 | [Visual Studio Code용 Azure Cognitive Search 확장](search-get-started-vs-code.md) | 인덱스, 인덱서, 데이터 원본 및 기술 세트를 만들기 위한 작업 영역, 탐색, 인텔리전스 및 템플릿을 추가합니다. | 퍼블릭 미리 보기 |
| 9월 | [관리 서비스 ID(인덱서)](search-howto-managed-identities-data-sources.md) | 일반 공급.  |
| 9월 | [프라이빗 링크를 사용하는 아웃바운드 요청](search-indexer-howto-access-private.md) | 일반 공급.  |
| 9월 | [관리 REST API(2020-08-01)](/rest/api/searchmanagement/management-api-versions) | 일반 공급. |
| 9월 | [관리 REST API(2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Azure Functions 및 Azure SQL for MySQL Databases용 공유 프라이빗 링크 리소스를 추가합니다. |
| 9월 | [관리 .NET SDK 4.0](/dotnet/api/overview/azure/search/management) |  관리 SDK용 Azure SDK 업데이트, 대상 REST API 버전 2020-08-01. 일반 공급.|
| 8월 | [이중 암호화](search-security-overview.md#encryption) | 다음 지역에서 2020년 8월 1일 이후에 만든 모든 검색 서비스에서 일반 공급됩니다. 미국 서부 2, 미국 동부, 미국 중부, US Gov 버지니아, US Gov 애리조나 지역에서 만든 모든 검색 서비스에 일반 공급됩니다. |
| 7월 | [Azure.Search.Documents 클라이언트 라이브러리](/dotnet/api/overview/azure/search.documents-readme) | .NET용 Azure SDK, 일반 공급 |
| 7월 | [azure.search.documents 클라이언트 라이브러리](/python/api/overview/azure/search-documents-readme)  | Python용 Azure SDK, 일반 공급 |
| 7월 | [@azure/search-documents 클라이언트 라이브러리](/javascript/api/overview/azure/search-documents-readme)  | JavaScript용 Azure SDK, 일반 공급 |
| 6월 | [지식 저장소](knowledge-store-concept-intro.md) | 일반 공급. |
| 6월 | [Search REST API 2020-06-30](/rest/api/searchservice/) | 일반 공급. |
| 6월 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) | 기술을 선택적으로 다시 처리하고 증분 방식으로 보강하는 기술 세트 다시 설정을 추가합니다. |
| 6월 | [Okapi BM25 관련성 알고리즘](index-ranking-similarity.md) | 일반 공급. |
| 6월 |  **executionEnvironment**(Azure Private Link를 사용하여 검색 서비스에 적용) | 일반 공급. |
| 6월 | [AML 기술(미리 보기)](cognitive-search-aml-skill.md) | 사용자 지정 AML(Azure Machine Learning) 모델을 사용하여 AI 보강을 확장하는 인식 기술입니다. |
| 5월 | [디버그 세션(미리 보기)](cognitive-search-debug-session.md) | 포털의 기술 세트 디버거입니다.  |
| 5월 | [인바운드 방화벽 지원에 대한 IP 규칙](service-configure-firewall.md) | 일반 공급.  |
| 5월 | [프라이빗 검색 엔드포인에 대한 Azure Private Link](service-create-private-endpoint.md) | 일반 공급.  |
| 5월 | [관리 서비스 ID(인덱서) - (미리 보기)](search-howto-managed-identities-data-sources.md) | 관리 ID를 사용하여 Azure 데이터 원본에 연결합니다.  |
| 5월 | [sessionId 쿼리 매개 변수](index-similarity-and-scoring.md), [scoringStatistics=global 매개 변수](index-similarity-and-scoring.md#scoring-statistics)  | [검색 관련성에 대한 기계 학습(LearnToRank) 모델](https://github.com/Azure-Samples/search-ranking-tutorial)에 유용한 전역 검색 통계입니다.  |
| 5월 | [featuresMode 관련성 점수 확장(미리 보기)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|3월  | [네이티브 Blob 일시 삭제(미리 보기)](search-howto-index-changed-deleted-blobs.md) | 원본 Blob이 Blob 스토리지에서 일시 삭제되면 검색 문서를 삭제합니다. |
|3월  | [관리 REST API(2020-03-13)](/rest/api/searchmanagement/management-api-versions) | 일반 공급. |
|February | [PII 검색 기술(미리 보기)](cognitive-search-skill-pii-detection.md)  | 개인 정보를 추출하고 마스크하는 인식 기술입니다. |
|February | [사용자 지정 엔터티 조회 기술(미리 보기)](cognitive-search-skill-custom-entity-lookup.md) | 목록에서 단어와 구를 찾고, 일치하는 엔터티가 있는 모든 문서에 레이블을 지정하는 인식 기술입니다.  |
|January | [고객 관리형 키 암호화](search-security-manage-encryption-keys.md) | 일반 공급  |
|January | [인바운드 방화벽 지원에 대한 IP 규칙(미리 보기)](service-configure-firewall.md) | [CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service)의 새 **IpRule** 및 **NetworkRuleSet** 속성입니다.  |
|January | [프라이빗 엔드포인트 만들기(미리 보기)](service-create-private-endpoint.md) | 검색 서비스에 대한 보안 연결을 위한 Private Link를 설정합니다. 이 미리 보기 기능에는 솔루션의 일부로 [Azure Private Link](../private-link/private-link-overview.md) 및 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) 종속성이 있습니다. |

## <a name="2019-archive"></a>2019 보관

| 월 | 기능 | 설명 |
|-------|---------|-------------|
|12월 | [데모 앱 만들기(미리 보기)](search-create-app-portal.md) | 인덱스에 대한 쿼리(읽기 전용) 액세스 권한이 있는 다운로드 가능한 HTML 파일을 생성하는 마법사이며, 전체 클라이언트 앱에 대한 바로 가기가 아니라 유효성 검사 및 테스트 도구로 사용됩니다.|
|11월 | [증분 보강(미리 보기)](cognitive-search-incremental-indexing-conceptual.md) | 나중에 다시 사용할 수 있도록 기술 세트 처리를 캐시합니다.  |
|11월 | [문서 추출 기술(미리 보기)](cognitive-search-skill-document-extraction.md) | 기술 세트 내에서 파일의 콘텐츠를 추출하는 인식 기술입니다.|
|11월 | [텍스트 번역 기술](cognitive-search-skill-text-translation.md) | 인덱싱 중에 사용되어 텍스트를 평가하고 번역하는 인식 기술입니다. 일반 공급.|
|11월 | [Power BI 템플릿](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | 지식 저장소에서 콘텐츠를 시각화하기 위한 템플릿입니다. |
|11월 | [Azure Data Lake Storage Gen2(미리 보기)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API(미리 보기)](search-howto-index-cosmosdb.md) 및 [Cosmos DB Cassandra API(미리 보기)](search-howto-index-cosmosdb.md) | 공개 미리 보기의 새 인덱서 데이터 원본입니다. |
|7월 | [Azure Government 클라우드 지원](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search) | 일반 공급.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>새 서비스 이름

Azure Search는 핵심 작업에서 인식 기술 및 AI 처리의 확장된 사용(아직 선택 사항임)을 반영하기 위해 2019년 10월에 **Azure Cognitive Search** 로 이름이 변경되었습니다. API 버전, NuGet 패키지, 네임스페이스 및 엔드포인트는 변경되지 않았습니다. 새 검색 솔루션과 기존 검색 솔루션은 서비스 이름 변경의 영향을 받지 않습니다.

## <a name="service-updates"></a>서비스 업데이트

Azure Cognitive Search에 대한 [서비스 업데이트 공지](https://azure.microsoft.com/updates/?product=search&status=all)는 Azure 웹 사이트에서 확인할 수 있습니다.