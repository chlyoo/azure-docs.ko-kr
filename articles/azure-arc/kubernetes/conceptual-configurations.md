---
title: 구성 및 GitOps-Azure Arc 사용 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes의 GitOps 및 구성 기능에 대 한 개념적 개요를 제공 합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너, 구성, GitOps
ms.openlocfilehash: f8fe1522eee4cc855ae1f396d9c98323114a25ce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652550"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Azure Arc를 사용 하는 구성 및 GitOps Kubernetes

Kubernetes와 관련해 서 GitOps는 Git 리포지토리에서 Kubernetes 클러스터 구성 (배포, 네임 스페이스 등)의 desired 상태를 선언 하는 방법입니다. 이 선언은 다음에 연산자를 사용 하 여 이러한 클러스터 구성의 폴링 및 끌어오기 기반 배포를 수행 합니다. Git 리포지토리에는 다음이 포함 될 수 있습니다.
* 네임 스페이스, ConfigMaps, 배포, DaemonSets 등을 비롯 한 모든 유효한 Kubernetes 리소스를 설명 하는 YAML 형식 매니페스트
* 응용 프로그램 배포를 위한 투구 차트

GitOps 공간에서 널리 사용 되는 오픈 소스 도구인 [Flux](https://docs.fluxcd.io/)는 Git 리포지토리에서 구성의 흐름을 Kubernetes 클러스터로 쉽게 Kubernetes 클러스터에 배포할 수 있습니다. Flux는 클러스터와 네임 스페이스 범위 모두에서 해당 연산자의 배포를 지원 합니다. 네임 스페이스 범위를 사용 하 여 배포 된 flux 연산자는 해당 특정 네임 스페이스 내에 Kubernetes 개체만 배포할 수 있습니다. 클러스터 또는 네임 스페이스 범위를 선택 하는 기능은 동일한 Kubernetes 클러스터에서 다중 테 넌 트 배포 패턴을 구현 하는 데 도움이 됩니다.

## <a name="configurations"></a>구성

[![구성 아키텍처 ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

클러스터와 Git 리포지토리 간의 연결은 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure Resource Manager에서로 표시 되는 Azure Arc Enabled Kubernetes 리소스의 맨 위에 확장 리소스로 생성 됩니다 `Microsoft.Kubernetes/connectedClusters` . 

`sourceControlConfiguration`리소스 속성은 매니페스트를 끌어올 Git 리포지토리와 해당 매개 변수를 풀 하는 폴링 간격 등 적절 한 매개 변수를 사용 하 여 클러스터에 Flux 연산자를 배포 하는 데 사용 됩니다. 데이터는 `sourceControlConfiguration` 암호화 된 상태로 저장 되며, 데이터 기밀성을 위해 Azure Cosmos DB 데이터베이스에 저장 됩니다.

`config-agent`클러스터에서 실행 되는는 다음을 담당 합니다.
* `sourceControlConfiguration`Azure Arc Enabled Kubernetes 리소스에서 새로운 또는 업데이트 된 확장 리소스를 추적 합니다.
* Flux 운영자를 배포 하 여 각에 대 한 Git 리포지토리를 시청 `sourceControlConfiguration` 하세요. '
* 모든 업데이트 적용 `sourceControlConfiguration` . 

`sourceControlConfiguration`다중 테 넌 트를 얻기 위해 동일한 Azure Arc 사용 Kubernetes 클러스터에서 네임 스페이스 범위 리소스를 여러 개 만들 수 있습니다.

> [!NOTE]
> * `config-agent``sourceControlConfiguration`Azure Arc Enabled Kubernetes 리소스에서 사용할 수 있는 새로운 또는 업데이트 된 확장 리소스를 지속적으로 모니터링 합니다. 따라서 에이전트는 필요한 상태 속성을 클러스터로 가져오기 위해 일관 된 연결을 필요로 합니다. 에이전트가 Azure에 연결할 수 없는 경우 클러스터에 필요한 상태가 적용 되지 않습니다.
> * 개인 키, 알려진 호스트 콘텐츠, HTTPS 사용자 이름, 토큰 또는 암호와 같은 중요 한 고객 입력은 Azure Arc 사용 Kubernetes 서비스에서 최대 48 시간 동안 저장 됩니다. 구성에 대 한 중요 한 입력을 사용 하는 경우 최대한 정기적으로 클러스터를 온라인으로 전환 합니다.

## <a name="apply-configurations-at-scale"></a>규모에 맞게 구성 적용

Azure Resource Manager는 구성을 관리 하므로 구독 또는 리소스 그룹의 범위 내에서 Azure Policy를 사용 하 여 모든 Azure Arc 사용 Kubernetes 리소스에 대해 동일한 구성 만들기를 자동화할 수 있습니다. 

이 크기 조정 적용은 공통 기준 구성 (ClusterRoleBindings, RoleBindings 및 NetworkPolicy와 같은 구성 포함)을 전체 또는 Azure Arc 사용 Kubernetes 클러스터의 인벤토리에 적용할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Arc에 클러스터 연결](./connect-cluster.md)
* [Arc 사용 Kubernetes 클러스터에 대 한 구성 만들기](./use-gitops-connected-cluster.md)
* [Azure Policy를 사용 하 여 대규모로 구성 적용](./use-azure-policy.md)