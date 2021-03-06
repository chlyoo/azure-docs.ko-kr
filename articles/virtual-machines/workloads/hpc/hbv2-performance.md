---
title: HBv2 시리즈 VM 크기 성능
description: Azure에서 HBv2 시리즈 VM 크기에 대 한 성능 테스트 결과에 대해 알아봅니다.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b782f3d435b7b3737de09b7a12d0fb17aa5f1edc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963320"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>HBv2 시리즈 가상 머신 크기

[HBv2 시리즈](../../hbv2-series.md) 크기 vm에서 몇 가지 성능 테스트를 실행 했습니다. 다음은 이러한 성능 테스트의 결과 중 일부입니다.


| 작업                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| 스트림 조로 묶어                                    | 350 g b/초 (CCX 당 21-23 g b/초)                                     |
| High-Performance Linpack (HPL)                  | 4 테라플롭 (Rpeak, FP64), 8 테라플롭 (Rpeak, FP32)               |
| RDMA 대기 시간 & 대역폭                        | 1.2 마이크로초, 190 g b/초                                        |
| 로컬 NVMe SSD의 FIO                           | 2.7 m b/초 읽기, 1.1 g b/초 쓰기 102k IOPS 읽기, 115 IOPS 쓰기 |
| IOR 8 * Azure 프리미엄 SSD (P40 Managed Disks, RAID0) * *  | 1.3 m b/초 읽기, 2.5 g b/쓰기 101k IOPS 읽기, 105k IOPS 쓰기 |


## <a name="mpi-latency"></a>MPI 대기 시간

OSU 마이크로 벤치 마크 제품군의 MPI 대기 시간 테스트를 실행 합니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="Azure HB의 MPI 대기 시간.":::


## <a name="mpi-bandwidth"></a>MPI 대역폭

OSU 마이크로 벤치 마크 제품군의 MPI 대역폭 테스트가 실행 됩니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Azure HB의 MPI 대역폭.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 패키지](https://community.mellanox.com/s/article/perftest-package) 에는 대기 시간 (ib_send_lat) 및 대역폭 (ib_send_bw)과 같은 많은 InfiniBand 테스트가 있습니다. 예제 명령은 다음과 같습니다. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 발표 및 일부 HPC (고성능 컴퓨팅) 예제 및 결과에 대해 읽어 보세요.
- 실행 중인 HPC 워크 로드에 대 한 높은 수준의 아키텍처 보기는 [Azure의 hpc (고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조 하세요.