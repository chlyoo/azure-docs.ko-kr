---
title: Azure Data Lake Analytics 개요
description: Data Lake Analytics를 통해 모든 규모의 클라우드 데이터에서 얻는 정보를 사용하여 비즈니스를 이끌 수 있습니다.
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: 4a6ef2821080982e2b34108703bd80574443244a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445532"
---
# <a name="what-is-azure-data-lake-analytics"></a>Azure Data Lake Analytics이란?

Azure Data Lake Analytics는 빅 데이터를 간소화하는 주문형 분석 작업 서비스입니다. 하드웨어를 배포, 구성 및 조정하는 대신, 데이터를 변형하고 귀중한 통찰력을 얻기 위한 쿼리를 작성합니다. 이 분석 서비스는 필요한 전력 크기만큼 다이얼을 설정하여 어떤 크기의 작업도 즉시 처리할 수 있습니다. 실행할 때 작업 기준으로 비용이 부과되므로 비용 효과적일 수 있습니다. 

## <a name="azure-data-lake-analytics-recent-update-information"></a>Azure Data Lake Analytics 최신 업데이트 정보

Azure Data Lake Analytics 서비스는 특정 목적을 위해 불규칙적으로 업데이트됩니다. 구성 요소 업데이트, 구성 요소 베타 미리 보기 등을 통해 이 서비스에 대한 지원이 지속적으로 제공됩니다. 

- 최신 업데이트 일반 정보는 [Data Lake Analytics의 새로운 기능](data-lake-analytics-whats-new.md)을 참조하세요.
- 각 업데이트에 대한 자세한 내용은 [Azure Data Lake Analytics 릴리스 정보](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes)를 참조하세요.

## <a name="dynamic-scaling"></a>동적 크기 조정
  
Data Lake Analytics를 통해 리소스를 동적으로 프로비전하고 테라바이트에서 엑사바이트 단위의 데이터도 분석할 수 있습니다. 사용하는 처리량에 따라서만 비용을 지불합니다. 저장된 데이터 크기 또는 사용된 컴퓨팅 리소스의 양을 늘리거나 줄일 때 코드를 다시 작성할 필요가 없습니다. 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>친숙한 도구를 사용하여 더 빠르게 개발하고, 디버그하고, 더 스마트하게 최적화
  
Data Lake Analytics는 Visual Studio와 통합됩니다. 친숙한 도구를 사용하여 코드를 실행, 디버깅 및 조정할 수 있습니다. U-SQL 작업의 시각화를 통해 코드가 실행되는 방식을 전체적으로 확인할 수 있으므로 성능 병목 현상을 쉽게 식별하고 비용을 최적화할 수 있습니다.

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: 간편하고, 친숙하고, 강력한 확장성
  
Data Lake Analytics은 SQL의 친숙하고 간단한 선언적 특성을 C#의 표현 능력으로 확장한 쿼리 언어인 U-SQL을 포함합니다. U-SQL 언어는 Microsoft의 내부 엑사바이트 규모의 데이터 레이크를 구동하는 동일한 분산된 런타임을 사용합니다. 이제 SQL 및 .NET 개발자가 이미 보유하고 있는 기술을 사용하여 데이터를 처리하고 분석할 수 있습니다.

## <a name="integrates-seamlessly-with-your-it-investments"></a>IT 투자와 완벽하게 통합
  
Data Lake Analytics은 ID, 관리 및 보안에 대한 기존 IT 투자를 사용합니다. 이러한 방법으로 데이터 거버넌스가 간소화되며 현재 데이터 애플리케이션을 쉽게 확장할 수 있습니다. Data Lake Analytics은 사용자 관리 및 권한에 대해 Active Directory와 통합되고 기본 제공 모니터링 및 감사와 함께 제공됩니다.

## <a name="affordable-and-cost-effective"></a>저렴하고 비용 효율적

Data Lake Analytics은 빅 데이터 작업을 실행하기 위한 경제적인 솔루션입니다. 데이터가 처리될 때 작업 단위로 비용을 지불합니다. 하드웨어, 라이선스 또는 서비스별 지원 계약이 필요하지 않습니다. 시스템은 작업이 시작되고 완료됨에 따라 자동으로 확장되고 축소되므로 필요 이상으로 비용을 지불하지 않아도 됩니다. [비용 제어 및 절약에 대해 자세히 알아봅니다](https://aka.ms/adlasavemoney).

## <a name="works-with-all-your-azure-data"></a>모든 Azure 데이터를 사용하여 작업
  
Data Lake Analytics는 최고의 성능, 처리량 및 병렬 처리를 위해 Azure Data Lake Storage와 함께 작동하며 Azure Storage Blob, Azure SQL Database, Azure Synapse Analytics에서 작동합니다.


## <a name="next-steps"></a>다음 단계

* See the Azure Data Lake Analytics recent update using [Azure Data Lake Analytics의 새로운 기능](data-lake-analytics-whats-new.md)을 사용하는 Azure Data Lake Analytics 최신 업데이트를 참조하세요.
* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md)를 사용하여 Data Lake Analytics 시작
* [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)를 사용하여 Azure Data Lake Analytics 관리
* [Data Lake Analytics로 비용을 제어하고 절약하는 방법](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
