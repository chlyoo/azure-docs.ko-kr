---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467790"
---
비행 데이터의 경우:

- 표준 TLS (전송 계층 보안) 1.2은 장치와 Azure 간에 이동 하는 데이터에 사용 됩니다. TLS 1.1 이전 버전에는 대체가 없습니다. TLS 1.2이 지원 되지 않는 경우 에이전트 통신이 차단 됩니다. TLS 1.2는 포털 및 SDK 관리에도 필요 합니다.
- 클라이언트가 브라우저의 로컬 웹 UI를 통해 장치에 액세스 하는 경우 표준 TLS 1.2가 기본 보안 프로토콜로 사용 됩니다.

  - 가장 좋은 방법은 TLS 1.2를 사용 하도록 브라우저를 구성 하는 것입니다.
  - 장치는 TLS 1.2만 지원 하 고 이전 버전의 TLS 1.1 또는 TLS 1.0을 지원 하지 않습니다.
- 데이터 서버에서 데이터를 복사할 때 데이터를 보호 하려면 암호화와 함께 SMB 3.0을 사용 하는 것이 좋습니다.
