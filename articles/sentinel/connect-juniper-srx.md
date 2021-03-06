---
title: Azure 센티널에 향나무 네트워크 SRX 데이터 연결 | Microsoft Docs
description: 곱 향나무 SRX 데이터 커넥터를 사용 하 여 곱 향나무 SRX 로그를 Azure 센티널로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 곱 향나무 SRX 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530640"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Azure 센티널에 곱 향나무 SRX 방화벽 연결

> [!IMPORTANT]
> 곱 향나무 SRX 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

이 문서에서는 Azure 센티널에 곱 향나무 SRX 방화벽 어플라이언스를 연결 하는 방법을 설명 합니다. 곱 향나무 SRX 데이터 커넥터를 사용 하면 통합 문서에서 데이터를 확인 하 고이를 사용 하 여 사용자 지정 경고를 만든 다음이를 통합 하 여 조사를 향상 시킬 수 있도록 Azure 센티널과 SRX 로그를 쉽게 연결할 수 있습니다. 향나무 SRX와 Azure 센티널 간의 통합은 Syslog를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 에 대 한 곱 향나무 SRX 솔루션은 Syslog를 통해 로그를 내보내도록 구성 되어야 합니다.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Syslog 에이전트에 대 한 전달 향나무 SRX 로그  

Syslog 에이전트를 통해 Azure 센티널 작업 영역에 Syslog 메시지를 전달 하도록 곱 향나무 SRX를 구성 합니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **곱 향나무 Srx (미리 보기)** 커넥터를 선택한 다음 **커넥터 페이지를 엽니다**.

1. **곱 향나무 SRX** 커넥터 페이지의 지침을 따릅니다.

    1. Linux 용 에이전트 설치 및 등록

        - Azure Linux VM 또는 비 Azure Linux 컴퓨터 (실제 또는 가상)를 선택 합니다.

    1. 수집할 로그 구성

        - 작업 영역 에이전트 구성에서 시설 및 심각도를 선택 합니다.

    1. 곱 향나무 SRX 구성 및 연결

        - 다음 지침에 따라 이전 syslog를 전달 하도록 곱 향나무 SRX를 구성 합니다.
            - [트래픽 로그 (보안 정책 로그)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [시스템 로그](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - 원격 서버에 대해 Linux 에이전트를 설치한 Linux 컴퓨터의 IP 주소를 사용 합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 된 후에는 해당 데이터가 Syslog 아래 Log Analytics에 표시 됩니다.

몇 가지 유용한 샘플 쿼리는 커넥터 페이지의 **다음 단계** 탭을 참조 하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 곱 SRX를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
