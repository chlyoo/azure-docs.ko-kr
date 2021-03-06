---
title: 마이그레이션 개요-LUIS
description: 마이그레이션 경로에 대 한 내용 이해
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: d6ecacf9aa1a7e650de74a412ed4f161ed0e0790
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253805"
---
# <a name="migration-in-luis"></a>LUIS의 마이그레이션

마이그레이션 경로에 몇 가지 항목이 있습니다. 다음 표를 사용 하 여 영향을 받는 내용과 마이그레이션을 완료 해야 하는 경우를 이해 합니다.

|영역|설명|마이그레이션 완료 날짜|
|--|--|--|
|[예측 Api](luis-migration-api-v3.md)|V3 API로 마이그레이션합니다.|TBD|
|[API 작성](luis-migration-authoring-entities.md)|V3 API로 마이그레이션합니다.|TBD|
|[제작 리소스](luis-migration-authoring.md)|LUIS portal에서 LUIS authoring resource를 사용 하 여 제작 되지 않은 리소스에서로 마이그레이션|2020 년 11 월 2 일 |
|[필수 기능](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|이 변경 사항은 빌드 회의에서 2020 년 5 월에 적용 되었으며, 앱이 제한 된 기능을 사용 하는 경우에만 v3 authoring Api에 적용 됩니다.|2020 년 6 월 19 일|
|[복합 엔터티](migrate-from-composite-entity.md)|복합 엔터티를 기계 학습 엔터티로 업그레이드 하 여 엔터티를 디버깅 하는 데 더 나은 decomposability으로 더 완벽 한 예측을 받는 엔터티를 빌드합니다.|TBD|
