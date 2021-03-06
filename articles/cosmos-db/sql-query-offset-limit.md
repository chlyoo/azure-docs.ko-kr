---
title: Azure Cosmos DB 오프셋 제한 절
description: OFFSET LIMIT 절을 사용 하 여에서 쿼리할 때 특정 값을 건너뛰거나 가져오는 방법에 대해 알아봅니다 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338300"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 오프셋 제한 절
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

OFFSET LIMIT 절은 건너뛸 선택적 절입니다 .이 절은 쿼리에서 특정 개수의 값을 사용 합니다. Offset LIMIT 절에는 오프셋 수와 제한 수가 필요 합니다.

OFFSET LIMIT를 ORDER BY 절과 함께 사용 하면 결과 집합은 skip을 수행 하 고 순서가 지정 된 값을 사용 하 여 생성 됩니다. ORDER BY 절을 사용 하지 않으면 값의 결정적 순서가 됩니다.

## <a name="syntax"></a>구문
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>인수

- `<offset_amount>`

   쿼리 결과에서 건너뛸 항목의 정수 수를 지정 합니다.

- `<limit_amount>`
  
   쿼리 결과에 포함 해야 하는 정수 항목 수를 지정 합니다.

## <a name="remarks"></a>설명
  
  `OFFSET`절에는 개수와 `LIMIT` 개수를 모두 입력 해야 `OFFSET LIMIT` 합니다. 선택적 절을 사용 하는 경우 `ORDER BY` 에는 정렬 된 값에 대해 skip을 수행 하 여 결과 집합을 생성 합니다. 그렇지 않은 경우 쿼리는 값의 고정 순서를 반환 합니다.

  로 쿼리를 사용 하는 경우에는 `OFFSET LIMIT` 오프셋 되는 용어 수가 증가 함에 따라 비용이 증가 합니다. [결과의 여러 페이지가](sql-query-pagination.md)있는 쿼리의 경우 일반적으로 [연속 토큰](sql-query-pagination.md#continuation-tokens)을 사용 하는 것이 좋습니다. 연속 토큰은 나중에 쿼리를 다시 시작할 수 있는 위치에 대 한 "책갈피"입니다. 를 사용 하 `OFFSET LIMIT` 는 경우 "책갈피"가 없습니다. 쿼리의 다음 페이지를 반환 하려는 경우 처음부터 시작 해야 합니다.
  
  `OFFSET LIMIT`항목 전체를 건너뛰고 클라이언트 리소스를 저장 하려는 경우에는를 사용 해야 합니다. 예를 들어, `OFFSET LIMIT` 1000th 쿼리 결과로 건너뛰려면 결과 1 ~ 999을 볼 필요가 없는 경우를 사용 해야 합니다. 백 엔드에서는 `OFFSET LIMIT` 건너뛴 항목을 포함 하 여 각 항목을 로드 합니다. 성능 이점은 필요 하지 않은 처리 항목을 방지 하 여 클라이언트 리소스를 절약할 수 있습니다.

## <a name="examples"></a>예

예를 들어 다음은 첫 번째 값을 건너뛰고 두 번째 값 (상주 도시 이름 순)을 반환 하는 쿼리입니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

다음은 첫 번째 값을 건너뛰고 순서를 지정 하지 않고 두 번째 값을 반환 하는 쿼리입니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [ORDER BY 절](sql-query-order-by.md)
