---
title: 데이터 흐름 매핑의 창 변환
description: 데이터 흐름 창 변환 Azure Data Factory 매핑
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/16/2020
ms.openlocfilehash: 56024fd0aac2f9fbefb7fe919eef2481550e573f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367829"
---
# <a name="window-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 창 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

창 변환에서는 데이터 스트림에서 열의 창 기반 집계를 정의합니다. 식 작성기에서는 LEAD, LAG, NTILE, CUMEDIST, RANK 등과 같은 데이터 또는 기간(SQL OVER 절)을 기반으로 하는 다른 유형의 집계를 정의할 수 있습니다. 이러한 집계를 포함하는 출력에 새 필드가 생성됩니다. 또한 필드별 선택적 그룹화를 포함할 수 있습니다.

![메뉴에서 선택한 창 고를 보여 주는 스크린샷](media/data-flow/windows1.png "windows 1")

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>위
창 변환에 대한 열 데이터의 분할을 설정합니다. 해당하는 SQL은 SQL의 Over 절에 있는 ```Partition By```입니다. 분할에 사용할 식을 만들거나 계산을 만들려면 열 이름을 마우스로 가리키고 "계산 열"을 선택하여 수행할 수 있습니다.

![스크린샷 선택 된 위로 탭을 사용 하 여 창 고 설정을 보여 줍니다.](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>정렬
Over 절의 다른 부분은 ```Order By```를 설정하는 것입니다. 이렇게 하면 데이터 정렬 순서가 설정됩니다. 정렬을 위해 이 열 필드에서 계산 값에 대한 식을 만들 수도 있습니다.

![스크린샷은 정렬 탭이 선택 된 상태 창 설정을 보여 줍니다.](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>범위 기준
그런 다음, 창 프레임을 Unbounded 또는 Bounded로 설정합니다. 바인딩되지 않은 창 프레임을 설정하려면 슬라이더를 양쪽 끝에서 Unbounded로 설정합니다. Unbounded 및 Current Row 중에서 설정을 선택하는 경우 오프셋 시작 및 끝 값을 설정해야 합니다. 두 값은 모두 양의 정수입니다. 데이터의 상대 숫자 또는 값을 사용할 수 있습니다.

창 슬라이더에는 현재 행 이전 값과 현재 행 이후 값이라는 두 개의 값이 설정됩니다. 시작 및 끝 오프셋은 슬라이더에 있는 두 개의 선택기와 일치합니다.

![스크린 샷에서는 범위 탭을 선택 하 여 창 고 설정을 보여 줍니다.](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>창 열
마지막으로 식 작성기를 사용하여 RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG 등과 같은 데이터 창과 함께 사용하려는 집계를 정의합니다.

![스크린샷 작업 창 작업의 결과를 보여 줍니다.](media/data-flow/windows7.png "windows 7")

식 작성기를 통해 ADF Data Flow 식 언어에서 사용할 수 있는 집계 및 분석 함수의 전체 목록은 https://aka.ms/dataflowexpressions에 나열되어 있습니다.

## <a name="next-steps"></a>다음 단계

간단한 그룹별 집계를 찾고 있는 경우 [집계 변환을](data-flow-aggregate.md) 사용 합니다.
