---
title: Azure Files 확장성 및 성능 목표
description: 용량, 요청 속도 및 인바운드/아웃바운드 대역폭 제한을 포함하여 Azure Files의 확장성 및 성능 목표를 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6ef255d78d3dd3ff6fcc5eba7aad522018185299
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518898"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure Files 확장성 및 성능 목표
[Azure Files](storage-files-introduction.md) 는 클라우드에서 SMB 및 NFS 파일 시스템 프로토콜을 통해 액세스할 수 있는 완전히 관리 되는 파일 공유를 제공 합니다. 이 문서에서는 Azure Files 및 Azure 파일 동기화의 확장성 및 성능 목표에 대해 설명합니다.

여기서 나열하는 확장성 및 성능 목표는 고성능 목표이지만 다른 배포 변수의 영향을 받을 수 있습니다. 예를 들어, 파일에 대 한 처리량은 Azure 파일 공유를 호스트 하는 서버 뿐 아니라 사용 가능한 네트워크 대역폭에 의해 제한 될 수도 있습니다. Azure Files의 확장성 및 성능이 요구 사항을 충족하는지 확인하려면 사용 패턴을 테스트하는 것이 좋습니다. 또한 시간이 지남에 따라 이러한 제한을 높이기 위해 노력하고 있습니다. 

## <a name="azure-files-scale-targets"></a>Azure Files 크기 조정 목표
Azure 파일 공유는 공유 스토리지 풀을 나타내는 최상위 개체인 스토리지 계정에 배포됩니다. 이 저장소 풀은 여러 파일 공유를 배포 하는 데 사용할 수 있습니다. 따라서 저장소 계정, Azure 파일 공유 및 파일의 세 가지 범주를 고려해 야 합니다.

### <a name="storage-account-scale-targets"></a>저장소 계정 크기 조정 대상
Azure는 고객이 가질 수 있는 다양 한 저장소 시나리오에 대해 여러 유형의 저장소 계정을 지원 하지만 Azure Files에는 두 가지 주요 유형의 저장소 계정이 있습니다. 만드는 데 필요한 저장소 계정 유형은 표준 파일 공유를 만들지, 아니면 프리미엄 파일 공유를 만들지에 따라 결정 됩니다. 

- **범용 버전 2(GPv2) 스토리지 계정**: GPv2 스토리지 계정을 사용하면 표준/하드 디스크 기반(HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. GPv2 스토리지 계정은 Azure 파일 공유 저장 외에도 Blob 컨테이너, 큐 또는 테이블과 같은 다른 스토리지 리소스를 저장할 수 있습니다. 파일 공유는 트랜잭션 최적화 (기본값), 핫 또는 쿨 계층에 배포 될 수 있습니다.

- **FileStorage 스토리지 계정**: FileStorage 스토리지 계정을 사용하면 프리미엄/반도체 디스크 기반(SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장하는 데만 사용할 수 있습니다. 다른 스토리지 리소스(Blob 컨테이너, 큐, 테이블 등)는 FileStorage 계정에 배포할 수 없습니다.

| attribute | GPv2 저장소 계정 (표준) | FileStorage storage 계정 (프리미엄) |
|-|-|-|
| 구독당 지역별 스토리지 계정 수 | 250 | 250 |
| 최대 스토리지 계정 용량 | 5 PiB<sup>1</sup> | 100 TiB (프로 비전 됨) |
| 최대 파일 공유 수 | 제한 없음 | 무제한, 모든 공유의 프로 비전 된 총 크기는 최대 저장소 계정 용량 보다 작아야 합니다. |
| 최대 동시 요청 빈도 | 2만 IOPS<sup>1</sup> | 100,000 IOPS |
| 최대 수신 | <ul><li>US/유럽: 10 Gbp/sec<sup>1</sup></li><li>기타 지역 (LRS/ZRS): 10 Gbp/sec<sup>1</sup></li><li>기타 지역 (GRS): 5 Gbp/sec<sup>1</sup></li></ul> | 4136 MiB/초 |
| 최대 송신 | 50 Gbp/초<sup>1</sup> | 6204 MiB/초 |
| 최대 가상 네트워크 규칙 수 | 200 | 200 |
| 최대 IP 주소 규칙 수 | 200 | 200 |
| 관리 읽기 작업 | 5분당 800 | 5분당 800 |
| 관리 쓰기 작업 | 시간당 10 초/1200 | 시간당 10 초/1200 |
| 관리 목록 작업 | 5분당 100 | 5분당 100 |

<sup>1</sup> 범용 버전 2 저장소 계정은 요청에의 한 수신에 대 한 더 높은 용량 제한과 더 높은 제한을 지원 합니다. 계정 제한을 늘리려면 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의하세요.

### <a name="azure-file-share-scale-targets"></a>Azure 파일 공유 크기 조정 목표
| attribute | 표준 파일 공유<sup>1</sup> | 프리미엄 파일 공유 |
|-|-|-|
| 파일 공유의 최소 크기 | 최소 | 100 GiB (프로 비전 됨) |
| 프로 비전 된 크기 증가/감소 단위 | N/A | GiB 1 |
| 파일 공유의 최대 크기 | <ul><li>100 TiB, large file share 기능이 사용 하도록 설정 됨<sup>2</sup></li><li>5 TiB, 기본값</li></ul> | 100TiB |
| 파일 공유의 최소 파일 수 | 제한 없음 | 제한 없음 |
| 최대 요청 빈도 (최대 IOPS) | <ul><li>1만, 대량 파일 공유 기능 사용<sup>2</sup></li><li>100 밀리초 당 1000 또는 100 요청, 기본값</li></ul> | <ul><li>기준 IOPS: 400 + GiB 당 IOPS 1, 최대 10만</li><li>IOPS 버스트: 최대 (4000, GiB 당 3, 000iops), 최대 10만</li></ul> |
| 단일 파일 공유에 대한 최대 수신 속도 | <ul><li>최대 300 MiB/초, 대량 파일 공유 기능 사용<sup>2</sup></li><li>최대 60 MiB/초, 기본값</li></ul> | 40 MiB/s + 0.04 * 프로 비전 된 GiB |
| 단일 파일 공유에 대한 최대 송신 속도 | <ul><li>최대 300 MiB/초, 대량 파일 공유 기능 사용<sup>2</sup></li><li>최대 60 MiB/초, 기본값</li></ul> | 60 MiB/s + 0.06 * 프로 비전 된 GiB |
| 최대 공유 스냅샷 수 | 200 스냅숏 | 200 스냅숏 |
| 최대 개체(디렉터리 및 파일) 이름 길이 | 2,048자 | 2,048자 |
| 최대 경로 이름 구성 요소(경로 \A\B\C\D의 각 문자가 구성 요소) | 255자 | 255자 |
| 하드 링크 제한(NFS 전용) | N/A | 178 |
| 최대 SMB 다중 채널 채널 수 | 해당 없음 | 4 |
| 파일 공유당 저장된 액세스 정책의 최대 수 | 5 | 5 |

<sup>1</sup> 표준 파일 공유에 대 한 한도는 표준 파일 공유에 사용할 수 있는 세 가지 계층 (트랜잭션 최적화, 핫 및 쿨) 모두에 적용 됩니다.

<sup>2</sup> 표준 파일 공유에 대 한 기본값은 5 TiB, 표준 파일 공유 크기를 100 TiB까지 늘리는 방법에 대 한 자세한 내용은 [대용량 파일 공유 사용 및 만들기](./storage-files-how-to-create-large-file-share.md) 를 참조 하세요.

### <a name="file-scale-targets"></a>파일 배율 대상
| attribute | 표준 파일 공유의 파일  | 프리미엄 파일 공유의 파일  |
|-|-|-|
| 최대 파일 크기 | 4TiB | 4TiB |
| 최대 동시 요청 빈도 | 1000 IOPS | 최대 8000<sup>1</sup> |
| 파일에 대 한 최대 수신 | 60 MiB/초 | 200 MiB/sec (SMB 다중 채널 미리 보기가 포함 된 최대 1 GiB/s)<sup>2</sup>|
| 파일에 대 한 최대 송신 | 60 MiB/초 | 300 MiB/sec (SMB 다중 채널 미리 보기가 포함 된 최대 1 GiB/s)<sup>2</sup> |
| 최대 동시 핸들 | 2000 핸들 | 2000 핸들  |

<sup>1은 읽기 및 쓰기 IOs에 적용 됩니다 (일반적으로 64 KiB 보다 작거나 같은 더 작은 IO 크기). 읽기 및 쓰기 이외의 메타 데이터 작업은 더 낮을 수 있습니다.</sup> 
 <sup>2 컴퓨터 네트워크 제한, 사용 가능한 대역폭, IO 크기, 큐 크기 및 기타 요인에 따라 결정 됩니다. 자세한 내용은 [SMB 다중 채널 성능](./storage-files-smb-multichannel-performance.md)을 참조 하세요.</sup>

## <a name="azure-file-sync-scale-targets"></a>Azure 파일 동기화의 크기 조정 목표
아래 표에는 Microsoft의 테스트 경계와 하드 한도인 목표가 표시되어 있습니다.

| 리소스 | 대상 | 하드 한도 |
|----------|--------------|------------|
| 지역당 스토리지 동기화 서비스 수 | 100개 스토리지 동기화 서비스 | 예 |
| 스토리지 동기화 서비스당 동기화 그룹 수 | 200개 동기화 그룹 | 예 |
| 스토리지 동기화 서비스당 등록된 서버 | 서버 99대 | 예 |
| 동기화 그룹당 클라우드 엔드포인트 수 | 1개 클라우드 엔드포인트 | 예 |
| 동기화 그룹당 서버 엔드포인트 수 | 100개 서버 엔드포인트 | 예 |
| 서버당 서버 엔드포인트 수 | 30개 서버 엔드포인트 | 예 |
| 동기화 그룹당 파일 시스템 개체(디렉터리 및 파일) 수 | 1억 개 개체 | 아니요 |
| 디렉터리에 있는 파일 시스템 개체(디렉터리 및 파일)의 최대 수 | 500만 개 개체 | 예 |
| 최대 개체(디렉터리 및 파일) 보안 설명자 크기 | 64KiB | 예 |
| 파일 크기 | 100GiB | 아니요 |
| 계층화할 파일에 대한 최소 파일 크기 | V9 이상: 파일 시스템 클러스터 크기를 기준으로 합니다(이중 파일 시스템 클러스터 크기). 예를 들어 파일 시스템 클러스터 크기가 4 KiB 경우 최소 파일 크기는 8 KiB가 됩니다.<br> V8 이하: 64KiB  | 예 |

> [!Note]  
> Azure 파일 동기화 엔드포인트는 Azure 파일 공유의 크기로 확장할 수 있습니다. Azure 파일 공유 크기 제한에 도달하면 동기화가 작동할 수 없습니다.

### <a name="azure-file-sync-performance-metrics"></a>Azure 파일 동기화 성능 메트릭
Azure 파일 동기화 에이전트가 Azure 파일 공유에 연결된 Windows Server 컴퓨터에서 실행되므로 유효한 동기화 성능은 인프라에 포함된 많은 요소(Windows Server 및 기본 디스크 구성,서버와 Azure Storage 간의 네트워크 대역폭, 파일 크기, 데이터 세트의 총 크기 및 데이터 세트의 작업 등)에 따라 달라집니다. Azure 파일 동기화가 파일 수준에서 작동하므로 Azure 파일 동기화 기반 솔루션의 성능 특성은 초당 처리된 개체(예: 파일 및 디렉터리)의 수에서 정확하게 측정됩니다.

Azure 파일 동기화의 경우 다음과 같은 두 단계에서 성능이 중요합니다.

1. **일회성 초기 프로비전**: 초기 프로비전에 대한 성능을 최적화하기 위해 최적의 배포 세부 정보는 [Azure 파일 동기화에 온보딩](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync)을 참조하세요.
2. **진행 중인 동기화**: Azure 파일 공유에서 데이터를 처음으로 시드한 후에 Azure 파일 동기화는 여러 엔트포인트를 동기화된 상태로 유지합니다.

각 단계에 대한 배포를 계획하기 위해 구성이 포함된 시스템의 내부 테스트 중에 확인되는 결과는 아래와 같습니다.

| 시스템 구성 | 세부 정보 |
|-|-|
| CPU | 64개의 MiB L3 캐시를 포함한 64개의 가상 코어 |
| 메모리 | 128GiB |
| 디스크 | 배터리 지원 캐시를 사용하는 RAID 10을 포함한 SAS 디스크 |
| 네트워크 | 1Gbps 네트워크 |
| 작업 | 범용 파일 서버|

| 일회성 초기 프로비전  | 세부 정보 |
|-|-|
| 개체 수 | 2500만 개 개체 |
| 데이터 세트 크기| ~ 4.7 TiB |
| 평균 파일 크기 | ~ 200 KiB (가장 큰 파일: 100 GiB) |
| 초기 클라우드 변경 열거 | 초당 20개 개체  |
| 처리량 업로드 | 동기화 그룹당 초당 20 개의 개체 |
| 네임 스페이스 다운로드 처리량 | 초당 개체 400개 |

### <a name="initial-one-time-provisioning"></a>일회성 초기 프로비전
**초기 클라우드 변경 내용 열거**: 새 동기화 그룹을 만들 때 첫 번째 단계는 실행 되는 첫 번째 단계입니다. 이 프로세스에서 시스템은 Azure 파일 공유의 모든 항목을 열거 합니다. 이 과정에서 동기화 작업이 수행 되지 않습니다. 즉, 클라우드 끝점에서 서버 끝점으로 다운로드 되는 항목이 없으며 서버 끝점에서 클라우드 끝점으로 항목이 업로드 되지 않습니다. 초기 클라우드 변경 열거가 완료 되 면 동기화 활동이 다시 시작 됩니다.
성능 속도는 초당 20 개의 개체입니다. 고객은 클라우드 공유의 항목 수를 확인 하 고 다음] 열의 공식을를 사용 하 여 시간 (일)을 확보 하 여 초기 클라우드 변경 열거를 완료 하는 데 걸리는 시간을 예측할 수 있습니다. 

   **초기 클라우드 열거의 시간 (일) = (클라우드 끝점의 개체 수)/(20 * 60 * 60 * 24)**

**네임 스페이스 다운로드 처리량** 기존 동기화 그룹에 새 서버 끝점을 추가 하면 Azure 파일 동기화 에이전트가 클라우드 끝점에서 파일 콘텐츠를 다운로드 하지 않습니다. 먼저 전체 네임스페이스를 동기화한 다음, 백그라운드 회수를 트리거하여 전체 파일을 다운로드하거나 클라우드 계층화를 사용하는 경우 서버 엔드포인트에서 설정된 클라우드 계층화 정책에 파일을 다운로드합니다.

| 진행 중인 동기화  | 세부 정보  |
|-|--|
| 동기화된 개체 수| 125000개 개체(~1% 변동) |
| 데이터 세트 크기| 50GiB |
| 평균 파일 크기 | ~500KiB |
| 처리량 업로드 | 동기화 그룹당 초당 20 개의 개체 |
| 전체 다운로드 처리량* | 초당 개체 60개 |

*클라우드 계층화를 사용하면 일부 파일 데이터만을 다운로드할 때 성능이 더 개선될 수도 있습니다. Azure 파일 동기화는 엔드포인트 중 하나에서 캐시된 파일의 데이터가 변경될 때에만 해당 데이터를 다운로드합니다. 계층되거나 새로 생성된 파일의 경우 에이전트는 파일 데이터를 다운로드하지 않습니다. 대신 모든 서버 엔드포인트에 네임스페이스만을 동기화합니다. 에이전트는 사용자가 액세스할 때 계층화된 파일의 부분 다운로드도 지원합니다. 

> [!Note]  
> 위의 숫자는 발생한 성능을 나타내지 않습니다. 이 섹션의 시작 부분에 설명된 대로 실제 성능은 여러 요인에 따라 달라집니다.

배포에 대한 일반 지침으로 몇 가지 사항에 유의해야 합니다.

- 개체 처리량은 서버의 동기화 그룹 수에 비례하여 크기를 조정합니다. 서버의 여러 동기화 그룹으로 데이터를 분할하면 처리량이 향상됩니다. 처리량은 서버 및 네트워크에 의해서도 제한됩니다
- 개체 처리량은 초당 MiB 처리량에 반비례합니다. 더 작은 파일의 경우 초당 처리된 개체 수 측면에서 더 높은 처리량이 발생하지만 초당 MiB 처리량은 더 낮습니다. 반대로 큰 파일의 경우 초당 처리되는 개체는 적지만 초당 MiB 처리량은 높습니다. 초당 MiB 처리량은 Azure Files 크기 조정 목표에 의해 제한됩니다.

## <a name="see-also"></a>추가 정보
- [Azure Files 배포 계획](storage-files-planning.md)
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)