---
title: AKS(Azure Kubernetes Service)에서 클러스터 자동 크기 조정기 사용
description: 클러스터 자동 크기 조정기를 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기를 조정하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: c0564dc3b394b4a65e70a487b6f6989cb306bdda
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373252"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기 조정

AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞추려면 워크로드를 실행하는 노드 수를 조정해야 할 수 있습니다. 클러스터 자동 크기 조정기 구성 요소는 리소스 제약 조건으로 인해 예약할 수 없는 클러스터의 Pod를 확인할 수 있습니다. 문제가 발견되면 애플리케이션 수요에 맞게 노드 풀의 노드 수가 증가합니다. 또한 실행 중인 Pod가 부족한지 노드를 주기적으로 확인하고 필요에 따라 노드 수가 감소합니다. AKS 클러스터에서 노드 수를 자동으로 강화하거나 규모 축소하는 이 기능을 통해 효율적이고 비용 효과적인 클러스터를 실행할 수 있습니다.

이 문서에서는 AKS 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정하고 관리하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.76 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="about-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 정보

업무 시간에서 야간 사이에 또는 주말에 변화하는 애플리케이션 수요에 맞게 조정하기 위해 일반적으로 클러스터에는 자동으로 크기를 조정하는 방법이 필요합니다. AKS 클러스터는 다음 두 가지 방법 중 하나로 크기를 조정할 수 있습니다.

* **클러스터 자동 크기 조정기** 는 리소스 제약 조건으로 인해 노드에서 예약할 수 없는 Pod를 확인합니다. 이에 따라 클러스터가 자동으로 노드 수를 늘립니다.
* **Horizontal Pod Autoscaler** 는 Kubernetes 클러스터에서 메트릭 서버를 사용하여 Pod의 리소스 수요를 모니터링합니다. 애플리케이션에 더 많은 리소스가 필요한 경우 수요에 맞게 자동으로 Pod 수가 증가합니다.

![종종 클러스터 자동 크기 조정기 및 Horizontal Pod Autoscaler가 연동되어 필요한 애플리케이션 수요를 지원함](media/autoscaler/cluster-autoscaler.png)

Horizontal Pod Autoscaler 및 클러스터 자동 크기 조정기가 둘 다 필요에 따라 Pod 및 노드 수를 줄일 수도 있습니다. 클러스터 자동 크기 조정기는 일정 기간 사용되지 않은 용량이 있는 경우 노드 수를 줄입니다. 클러스터 자동 크기 조정기가 제거할 노드의 Pod는 클러스터의 다른 위치에서 안전하게 예약됩니다. 다음 상황에서처럼 Pod를 이동할 수 없는 경우 클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있습니다.

* Pod가 직접 생성되었고 컨트롤러 개체(예: 배포 또는 복제본 세트)에서 지원되지 않습니다.
* PDB(Pod Disruption Budget)가 너무 제한적이고 이를 사용하면 Pod 수가 특정 임계값보다 낮아질 수 없습니다.
* Pod는 다른 노드에서 예약된 경우 적용할 수 없는 노드 선택기 또는 선호도 방지를 사용합니다.

클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있는 경우에 대한 자세한 내용은 [클러스터 자동 크기 조정기가 노드를 제거할 수 없도록 하는 Pod 유형은 무엇인가요?][autoscaler-scaledown]를 참조하세요.

클러스터 자동 크기 조정기는 크기 조정 이벤트와 리소스 임계값 사이의 시간 간격 같은 항목에 시작 매개 변수를 사용합니다. 클러스터 autoscaler 사용 하는 매개 변수에 대 한 자세한 내용은 [autoscaler 프로필 사용](#using-the-autoscaler-profile)을 참조 하세요.

Horizontal Pod Autoscaler 및 클러스터 자동 크기 조정기가 연동되고 종종 둘 다 하나의 클러스터에 배포됩니다. 결합된 경우 Horizontal Pod Autoscaler는 애플리케이션 수요를 충족하는 데 필요한 개수의 Pod를 실행하는 데 중점을 둡니다. 클러스터 자동 크기 조정기는 예약된 Pod를 지원하는 데 필요한 개수의 노드를 실행하는 데 중점을 둡니다.

> [!NOTE]
> 클러스터 자동 크기 조정기를 사용하면 수동 크기 조정이 사용하지 않도록 설정됩니다. 클러스터 자동 크기 조정기가 필요한 노드 수를 결정하도록 해보겠습니다. 클러스터 크기를 수동으로 조정하려면 [클러스터 자동 크기 조정기를 사용하지 않도록 설정](#disable-the-cluster-autoscaler)합니다.

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS 클러스터를 만들고 클러스터 자동 크기 조정기를 사용하도록 설정

AKS 클러스터를 만들어야 하는 경우 [az aks create][az-aks-create] 명령을 사용합니다. 클러스터에 대 한 노드 풀에서 클러스터 autoscaler를 사용 하도록 설정 하 고 구성 하려면 매개 변수를 사용 하 고 `--enable-cluster-autoscaler` 및 노드를 지정 합니다 `--min-count` `--max-count` .

> [!IMPORTANT]
> 클러스터 자동 크기 조정기는 Kubernetes 구성 요소입니다. AKS 클러스터는 가상 머신 확장 집합을 노드에 사용하지만, Azure Portal에서 또는 Azure CLI를 사용하여 확장 집합 자동 크기 조정에 대한 설정을 직접 설정하거나 편집하지 마세요. Kubernetes 클러스터 자동 크기 조정기가 필수 크기 조정 설정을 자동으로 관리하게 두세요. 자세한 내용은 [노드 리소스 그룹의 AKS 리소스를 수정할 수 있나요?][aks-faq-node-resource-group]를 참조하세요.

다음 예에서는 가상 머신 확장 집합에 지원되는 단일 노드 풀로 AKS 클러스터를 만듭니다. 또한 클러스터의 노드 풀에서 클러스터 자동 크기 조정기를 사용하도록 설정하고 최소 *1* 개 및 최대 *3* 개 노드를 설정합니다.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

클러스터를 만들고 클러스터 자동 크기 조정기 설정을 구성하는 데 몇 분 정도 걸립니다.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>클러스터 autoscaler을 사용 하도록 기존 AKS 클러스터를 업데이트 합니다.

[Az aks update][az-aks-update] 명령을 사용 하 여 기존 클러스터에 대 한 노드 풀에서 cluster autoscaler를 사용 하도록 설정 하 고 구성할 수 있습니다. `--enable-cluster-autoscaler`매개 변수를 사용 하 고 및 노드를 지정 `--min-count` `--max-count` 합니다.

> [!IMPORTANT]
> 클러스터 자동 크기 조정기는 Kubernetes 구성 요소입니다. AKS 클러스터는 가상 머신 확장 집합을 노드에 사용하지만, Azure Portal에서 또는 Azure CLI를 사용하여 확장 집합 자동 크기 조정에 대한 설정을 직접 설정하거나 편집하지 마세요. Kubernetes 클러스터 자동 크기 조정기가 필수 크기 조정 설정을 자동으로 관리하게 두세요. 자세한 내용은 [노드 리소스 그룹의 AKS 리소스를 수정할 수 있나요?][aks-faq-node-resource-group]를 참조하세요.

다음 예에서는 기존 AKS 클러스터를 업데이트 하 여 클러스터에 대 한 노드 풀에서 cluster autoscaler를 사용 하도록 설정 하 고 최소 *1 개* 에서 최대 *3* 개의 노드를 설정 합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

클러스터를 업데이트 하 고 클러스터 autoscaler 설정을 구성 하는 데 몇 분 정도 걸립니다.

## <a name="change-the-cluster-autoscaler-settings"></a>클러스터 자동 크기 조정기 설정 변경

> [!IMPORTANT]
> AKS 클러스터에 여러 노드 풀이 있는 경우 [여러 에이전트 풀로 자동 크기 조정](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) 섹션으로 건너뜁니다. 여러 에이전트 풀을 사용하는 클러스터는 `az aks nodepool` 명령 세트를 사용하여 `az aks` 대신 노드 풀 관련 속성을 변경해야 합니다.

AKS 클러스터를 만들거나 기존 노드 풀을 업데이트하는 이전 단계에서 클러스터 자동 크기 조정기의 최소 노드 수는 *1* 로 설정되고 최대 노드 수는 *3* 으로 설정되었습니다. 애플리케이션 수요가 변경되면 클러스터 자동 크기 조정기의 노드 수를 조정해야 할 수 있습니다.

노드 수를 변경하려면 [az aks update][az-aks-update] 명령을 사용합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

위의 예에서는 *myAKSCluster* 의 단일 노드 풀에 있는 클러스터 자동 크기 조정기를 최소 *1* 개 및 최대 *5* 개 노드로 업데이트합니다.

> [!NOTE]
> 클러스터 autoscaler는 각 노드 풀에 설정 된 최소 및 최대 개수에 따라 크기 조정을 결정 하지만 최소 또는 최대 개수를 업데이트 한 후에는이를 적용 하지 않습니다. 예를 들어 현재 노드 수가 3 인 경우 최소 개수를 5로 설정 하면 풀이 최대 5 개까지 즉시 확장 되지 않습니다. 노드 풀의 최소 개수에 현재 노드 수보다 큰 값이 있는 경우 새 추가 노드가 2 개 필요 하 고 autoscaler 이벤트를 트리거하는 예약 되지 않은 pod이 충분히 있는 경우 새 최소 또는 최대 설정이 적용 됩니다. 크기 조정 이벤트 후에는 새로운 개수 제한이 적용 됩니다.

애플리케이션 및 서비스의 성능을 모니터링하고 클러스터 자동 크기 조정기 노드 수를 필요한 성능과 일치하도록 조정합니다.

## <a name="using-the-autoscaler-profile"></a>자동 크기 조정기 프로필 사용

클러스터 전체 자동 크기 조정기 프로필의 기본값을 변경하여 클러스터 자동 크기 조정기의 세부 정보를 구성할 수도 있습니다. 예를 들어 10분 후 노드 사용량이 저조하면 스케일 다운 이벤트가 발생합니다. 15분마다 실행된 워크로드가 있는 경우 15분 또는 20분 후에 사용량이 저조한 노드를 스케일 다운하도록 자동 크기 조정기 프로필을 변경할 수 있습니다. 클러스터 자동 크기 조정기를 사용하도록 설정하면 다른 설정을 지정하지 않는 한 기본 프로필이 사용됩니다. 클러스터 자동 크기 조정기 프로필에서 다음과 같은 설정을 업데이트할 수 있습니다.

| 설정                          | 설명                                                                              | 기본값 |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scan-interval                    | 스케일 업 또는 다운을 위해 클러스터를 다시 평가하는 빈도                                    | 10초    |
| scale-down-delay-after-add       | 스케일 업 후 스케일 다운 평가가 다시 시작되기 전까지 경과 시간                               | 10분    |
| scale-down-delay-after-delete    | 노드 삭제 후 스케일 다운 평가가 다시 시작되기 전까지 경과 시간                          | scan-interval |
| scale-down-delay-after-failure   | 스케일 다운 실패 후 스케일 다운 평가가 다시 시작되기 전까지 경과 시간                     | 3분     |
| scale-down-unneeded-time         | 불필요한 노드를 스케일 다운하기 전까지 경과 시간                  | 10분    |
| scale-down-unready-time          | 준비되지 않은 노드를 스케일 다운하기 전까지 경과 시간         | 20분    |
| scale-down-utilization-threshold | 요청된 리소스의 합계를 용량으로 나눈 노드 사용률 수준으로, 이 수준 미만의 노드는 스케일 다운 대상으로 고려할 수 있음 | 0.5 |
| max-graceful-termination-sec     | 클러스터 autoscaler가 노드 규모를 축소 하려고 할 때 pod 종료를 대기 하는 최대 시간 (초)입니다. | 600초   |
| balance-similar-node-groups      | 비슷한 노드 풀을 검색 하 고 두 노드 간의 노드 수를 분산 합니다.                 | false         |
| 기호                         | 수직 확장에 사용할 노드 풀 [확장기](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) 의 유형입니다. 가능한 값: `most-pods` , `random` , `least-waste` , `priority` | random | 
| skip-노드-로컬 저장소    | True 클러스터 autoscaler가 로컬 저장소가 있는 pod를 사용 하 여 노드를 삭제 하지 않는 경우 (예: EmptyDir 또는 HostPath) | true |
| skip-노드-시스템-pod      | True 클러스터 autoscaler가 kube에서 pod를 사용 하 여 노드를 삭제 하지 않는 경우 (DaemonSet 또는 미러 pod 제외) | true | 
| 최대-비어 있음-대량 삭제            | 동시에 삭제할 수 있는 빈 노드의 최대 수                       | 10 개 노드      |
| 새-pod-축소-지연           | Kubernetes 스케줄러가 모든 pod를 예약 하기 전에 CA를 사용 하지 않으려는 버스트/일괄 처리 규모와 같은 시나리오의 경우, 특정 기간 전에는 예약 되지 않은 pod를 무시 하도록 CA에 지시할 수 있습니다.                                                                                                                | 0초    |
| 최대-전체-준비 되지 않음-백분율     | 클러스터에서 준비 되지 않은 노드의 최대 백분율입니다. 이 비율을 초과 하면 CA가 작업을 중단 합니다. | 45% |
| 최대 노드 프로 비전 시간          | Autoscaler가 노드가 프로 비전 될 때까지 대기 하는 최대 시간입니다.                           | 15분    |   
| 확인-전체-준비 되지 않음-개수           | 최대 합계-준비 되지 않음 비율에 관계 없이 허용 된 준비 되지 않은 노드 수            | 3 개 노드       |

> [!IMPORTANT]
> 클러스터 자동 크기 조정기 프로필은 클러스터 자동 크기 조정기를 사용하는 모든 노드 풀에 영향을 줍니다. 노드 풀별로 자동 크기 조정기 프로필을 설정할 수는 없습니다.
>
> Cluster autoscaler profile에는 버전이 Azure CLI *2.11.1* 이상이 필요 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>기존 AKS 클러스터에 클러스터 자동 크기 조정기 프로필 설정

클러스터에 클러스터 자동 크기 조정기 프로필을 설정하려면 *cluster-autoscaler-profile* 매개 변수가 지정된 [az aks update][az-aks-update-preview] 명령을 사용하세요. 다음 예에서는 프로필에 검색 간격 설정을 30초로 구성합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

클러스터의 노드 풀에서 자동 크기 조정기를 사용하도록 설정하면 해당 클러스터가 클러스터 자동 크기 조정기 프로필도 사용합니다. 다음은 그 예입니다.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> 클러스터 자동 크기 조정기 프로필을 설정하면 클러스터 자동 크기 조정기가 설정된 모든 기존 노드 풀이 프로필을 즉시 사용하기 시작합니다.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>AKS 클러스터를 만들 때 클러스터 자동 크기 조정기 프로필 설정

클러스터를 만들 때 *cluster-autoscaler-profile* 매개 변수를 사용할 수도 있습니다. 다음은 그 예입니다.

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

위의 명령은 AKS 클러스터를 만들고 클러스터 전체 자동 크기 조정기 프로필의 검색 간격을 30초로 정의합니다. 또한 이 명령은 초기 노드 풀에서 클러스터 자동 크기 조정기를 사용하도록 설정하고, 최소 노드 수를 1로, 최대 노드 수를 3으로 설정합니다.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>클러스터 자동 크기 조정기 프로필을 기본값으로 초기화

클러스터에서 클러스터 자동 크기 조정기 프로필을 초기화하려면 [az aks update][az-aks-update-preview] 명령을 사용합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 사용 안 함

클러스터 autoscaler을 더 이상 사용 하지 않으려면 [az aks update][az-aks-update-preview] 명령을 사용 하 여 매개 변수를 지정 하 여 사용 하지 않도록 설정할 수 있습니다 `--disable-cluster-autoscaler` . 클러스터 자동 크기 조정기가 사용하지 않도록 설정되면 노드가 제거되지 않습니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

[az aks scale][az-aks-scale] 명령을 사용하여 클러스터 자동 크기 조정기를 사용하지 않도록 설정한 후 수동으로 클러스터의 크기를 조정할 수 있습니다. Horizontal Pod Autoscaler를 사용하는 경우 해당 기능은 클러스터 자동 크기 조정기가 사용되지 않는 상태로 계속 실행되지만, 모든 노드 리소스가 사용 중인 경우 결국 Pod를 예약할 수 없습니다.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>사용하지 않도록 설정된 클러스터 자동 크기 조정기 다시 사용

기존 클러스터에서 클러스터 autoscaler를 다시 사용 하도록 설정 하려는 경우 [az aks update][az-aks-update-preview] 명령을 사용 하 여 `--enable-cluster-autoscaler` , `--min-count` 및 매개 변수를 지정 하 여 다시 사용 하도록 설정할 수 있습니다 `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>클러스터 자동 크기 조정기 로그 및 상태 검색

자동 크기 조정기 이벤트를 진단하고 디버깅하려면 자동 크기 조정기 추가 기능에서 로그 및 상태를 검색하면 됩니다.

AKS는 사용자를 대신하여 클러스터 자동 크기 조정기를 관리하고 관리형 컨트롤 플레인에서 실행합니다. 제어 평면 노드에서 CA의 로그 및 작업을 볼 수 있도록 설정할 수 있습니다.

클러스터 자동 크기 조정기에서 Log Analytics로 로그가 푸시되도록 구성하려면 다음 단계를 수행합니다.

1. 클러스터 자동 크기 조정기 로그를 Log Analytics로 푸시하도록 리소스 로그 규칙을 설정합니다. [지침은 여기에 자세히 설명][aks-view-master-logs]되어 있습니다. "로그" 옵션을 선택할 때 `cluster-autoscaler`에 대한 확인란을 선택해야 합니다.
1. Azure Portal를 통해 클러스터에서 "로그" 섹션을 선택 합니다.
1. 다음 예제 쿼리를 Log Analytics에 입력합니다.

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

검색할 로그가 있다면 다음 예제와 유사한 로그가 표시됩니다.

![Log Analytics 로그](media/autoscaler/autoscaler-logs.png)

또한 클러스터 autoscaler는 라는에 상태를 기록 `configmap` `cluster-autoscaler-status` 합니다. 이러한 로그를 검색하려면 다음 `kubectl` 명령을 실행합니다. 클러스터 자동 크기 조정기에 구성된 각 노드 풀의 상태가 보고됩니다.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

클러스터 자동 크기 조정기에서 로깅되는 항목에 대해 자세히 알아보려면 [Kubernetes/자동 크기 조정기 GitHub 프로젝트][kubernetes-faq]의 FAQ를 참조하세요.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>여러 노드 풀이 설정된 클러스터 자동 크기 조정기 사용

클러스터 자동 크기 조정기는 설정된 [여러 노드 풀][aks-multiple-node-pools]과 함께 사용할 수 있습니다. 이 문서에 따라 여러 노드 풀을 사용하도록 설정하고 기존 클러스터에 노드 풀을 더 추가하는 방법을 알아보세요. 두 기능을 함께 사용하는 경우 클러스터의 각 개별 노드 풀에서 클러스터 자동 크기 조정기를 사용하도록 설정하고, 각각에 고유한 자동 크기 조정 규칙을 전달할 수 있습니다.

아래 명령에서는 사용자가 이 문서의 앞부분에 나와 있는 [초기 지침](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)을 따랐으며 기존 노드 풀의 최대 개수를 *3* 개에서 *5* 개로 업데이트하려 한다고 가정합니다. [az aks nodepool update][az-aks-nodepool-update] 명령을 사용하여 기존 노드 풀의 설정을 업데이트합니다.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

[az aks nodepool update][az-aks-nodepool-update]를 사용하고 `--disable-cluster-autoscaler` 매개 변수를 전달하여 클러스터 자동 크기 조정기를 사용하지 않도록 설정할 수 있습니다.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

기존 클러스터에서 클러스터 autoscaler를 다시 사용 하도록 설정 하려는 경우 [az aks nodepool update][az-aks-nodepool-update] 명령을 사용 하 여 `--enable-cluster-autoscaler` , `--min-count` 및 매개 변수를 지정 하 여 다시 사용 하도록 설정할 수 있습니다 `--max-count` .

> [!NOTE]
> 여러 영역에 걸쳐 있는 nodepools에서 클러스터 autoscaler를 사용 하 고 볼륨 토폴로지 일정과 같은 영역에 관련 된 예약 기능을 활용 하는 경우 영역 당 하나의 nodepools를 사용 하 고 autoscaler 프로필을 통해를 사용 하도록 설정 하는 것이 좋습니다 `--balance-similar-node-groups` . 이렇게 하면 autoscaler이 성공적으로 확장 되 고 nodepools의 크기를 조정 하 고 유지 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 노드 수를 자동으로 조정하는 방법을 설명했습니다. Horizontal Pod Autoscaler를 사용하여 애플리케이션을 실행하는 Pod 수를 자동으로 조정할 수도 있습니다. Horizontal Pod Autoscaler를 사용하는 단계는 [AKS에서 애플리케이션 크기 조정][aks-scale-apps]을 참조하세요.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
