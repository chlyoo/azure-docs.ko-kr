---
title: 모델 빌드 및 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 모델을 학습 하는 방법을 알아봅니다. 다양 한 학습 방법을 탐색 하 고 프로젝트에 적합 한 방법을 선택 합니다.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: a84e4e767092a1507ef0608c21ff1896cb1dde0d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880947"
---
# <a name="train-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델 학습

Azure Machine Learning은 SDK를 사용 하는 코드 우선 솔루션에서 자동화 된 기계 학습 및 비주얼 디자이너와 같은 낮은 코드 솔루션까지 모델을 학습 하는 여러 가지 방법을 제공 합니다. 다음 목록을 사용하여 적합한 학습 방법을 결정하세요.

+ [Python용 Azure Machine Learning SDK](#python-sdk): Python SDK는 모델을 학습시키는 여러 가지 방법을 제공하며, 방법마다 기능이 다릅니다.

    | 학습 방법 | Description |
    | ----- | ----- |
    | [실행 구성](#run-configuration) | **모델을 학습 하는 일반적인 방법은** 학습 스크립트 및 실행 구성을 사용 하는 것입니다. 실행 구성은 모델 학습에 사용되는 학습 환경을 구성하는 데 필요한 정보를 제공합니다. 실행 구성에서 학습 스크립트, 계산 대상 및 Azure ML 환경을 지정 하 고 학습 작업을 실행할 수 있습니다. |
    | [자동화된 기계 학습](#automated-machine-learning) | 자동화된 기계 학습을 사용하면 **방대한 데이터 과학 또는 프로그래밍 지식 없이도 모델을 학습** 시킬 수 있습니다. 데이터 과학 및 프로그래밍에 대한 배경 지식이 있는 분들에게는 알고리즘 선택 및 하이퍼 매개 변수 튜닝을 자동화하여 시간과 리소스를 절약하는 방법을 제공합니다. 자동화된 기계 학습을 사용할 때 실행 구성 정의에 대해 걱정할 필요가 없습니다. |
    | [기계 학습 파이프라인](#machine-learning-pipeline) | 파이프라인은 다른 학습 방법이 아니라 **모듈식 재사용 가능 단계를 사용하여 워크플로를 정의하는 방법** 으로, 학습을 포함할 수 있습니다. 기계 학습 파이프라인은 자동화 된 기계 학습 및 실행 구성을 사용 하 여 모델을 학습 하도록 지원 합니다. 파이프라인은 특별히 학습에 집중하지 않으므로, 파이프라인을 사용하는 이유는 다른 학습 방법보다 다양합니다. 일반적으로 다음과 같은 경우에 파이프라인을 사용합니다.<br>* 장기 실행 학습 작업 또는 데이터 준비와 같은 **무인 프로세스를 예약** 하려고 합니다.<br>* 이기종 컴퓨팅 리소스 및 스토리지 위치에서 조정되는 **여러 단계** 를 사용합니다.<br>* 재학습 또는 일괄 처리 채점 같은 특정 시나리오에서 파이프라인을 **재사용 가능 템플릿** 으로 사용합니다.<br>워크플로의 * **데이터 원본, 입력 및 출력을 추적하고 버전을 관리합니다**.<br>* **특정 단계를 독립적으로 작업하는 다른 팀에서 워크플로를 구현** 합니다. 그런 다음, 파이프라인에서 여러 단계를 조인하여 워크플로를 구현할 수 있습니다. |

+ [R에 대 한 AZURE MACHINE LEARNING sdk (미리 보기)](#r-sdk-preview): r 용 sdk는 reticulate 패키지를 사용 하 여 Azure Machine Learning의 Python SDK에 바인딩합니다. 따라서 모든 R 환경에서 Python SDK를 사용하여 구현된 핵심 개체 및 메서드에 액세스할 수 있습니다.

+ **Designer**: Azure Machine Learning designer는 개념 증명을 빌드하기 위한 Machine Learning 또는 코딩 경험이 적은 사용자를 위한 쉬운 진입점을 제공 합니다. 끌어서 놓기 웹 기반 UI를 사용하여 모델을 학습시킬 수 있습니다. 디자인의 일부로 Python 코드를 사용할 수도 있고, 코드를 작성하지 않고 모델을 학습시킬 수도 있습니다.

+ **CLI**: 기계 학습 CLI는 Azure Machine Learning을 사용하는 일반적인 작업에 대한 명령을 제공하며, **스크립팅 및 자동화 작업** 에 종종 사용됩니다. 예를 들어 학습 스크립트 또는 파이프라인을 만든 후에는 CLI를 사용하여 일정에 따라 또는 학습에 사용되는 데이터 파일이 업데이트될 때 학습 실행을 시작할 수 있습니다. 학습 모델에는 학습 작업을 제출하는 명령을 제공합니다. 실행 구성 또는 파이프라인을 사용하여 작업을 제출할 수 있습니다.

각 학습 방법에서 다양한 유형의 컴퓨팅 리소스를 학습에 사용할 수 있습니다. 이러한 리소스를 총칭하여 [__컴퓨팅 대상__](concept-azure-machine-learning-architecture.md#compute-targets)이라고 합니다. 컴퓨팅 대상은 로컬 컴퓨터 또는 클라우드 리소스(예: Azure Machine Learning 컴퓨팅, Azure HDInsight 또는 원격 가상 머신)입니다.

## <a name="python-sdk"></a>Python SDK

Python용 Azure Machine Learning SDK를 사용하여 Azure Machine Learning에서 기계 학습 워크플로를 빌드하고 실행할 수 있습니다. 대화형 Python 세션, Jupyter Notebook, Visual Studio Code 또는 다른 IDE에서 서비스와 상호 작용할 수 있습니다.

* [Python용 Azure Machine Learning SDK란?](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
* [SDK 설치/업데이트](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Azure Machine Learning용 개발 환경 구성](how-to-configure-environment.md)

### <a name="run-configuration"></a>실행 구성

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)를 사용 하 여 Azure Machine Learning를 사용 하는 일반적인 학습 작업을 정의할 수 있습니다. 그런 다음 계산 대상에서 모델을 학습 하는 학습 스크립트와 함께 스크립트 실행 구성이 사용 됩니다.

로컬 컴퓨터에 대해 실행 구성을 시작한 다음, 필요에 따라 클라우드 기반 컴퓨팅 대상의 실행 구성으로 전환할 수 있습니다. 컴퓨팅 대상을 변경할 때 사용하는 실행 구성만 변경하면 됩니다. 실행은 입력, 출력 및 로그처럼 학습 작업에 대한 정보를 기록합니다.

* [실행 구성이란?](concept-azure-machine-learning-architecture.md#run-configurations)
* [자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md)
* [예제: 학습 모델의 Jupyter Notebook 및 Python 예제](https://github.com/Azure/azureml-examples)
* [방법: 학습 실행 구성](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

반복, 하이퍼 매개 변수 설정, 기능화 및 기타 설정을 정의합니다. 학습하는 동안 Azure Machine Learning은 여러 가지 알고리즘과 매개 변수를 병렬로 시도합니다. 사용자가 정의한 종료 조건에 도달하면 학습이 중지됩니다.

> [!TIP]
> Python SDK 외에도 [Azure Machine Learning 스튜디오](https://ml.azure.com)를 통해 자동화된 ML을 사용할 수 있습니다.

* [자동화된 Machine Learning이란?](concept-automated-ml.md)
* [자습서: 자동화된 Machine Learning을 사용하여 처음으로 분류 모델 만들어 보기](tutorial-first-experiment-automated-ml.md)
* [자습서: 자동화된 Machine Learning을 사용하여 택시 요금 예측](tutorial-auto-train-models.md)
* [예제: 자동화된 기계 학습을 위한 Jupyter Notebook 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [방법: Python에서 자동화된 ML 실험 구성](how-to-configure-auto-train.md)
* [방법: 시계열 예측 모델 자동 학습](how-to-auto-train-forecast.md)
* [방법: Azure Machine Learning Studio를 사용하여 자동화된 Machine Learning 실험 만들기, 탐색 및 배포](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>기계 학습 파이프라인

Machine learning 파이프라인은 앞에서 언급 한 학습 메서드를 사용할 수 있습니다. 파이프라인은 워크플로 만들기에 초점이 더 맞춰져 있기 때문에 모델 학습보다 더 많은 기능을 포함하고 있습니다. 파이프라인에서 자동화 된 기계 학습 또는 실행 구성을 사용 하 여 모델을 학습 시킬 수 있습니다.

* [Azure Machine Learning의 ML 파이프라인이란?](concept-ml-pipelines.md)
* [Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인 만들기 및 실행](./how-to-create-machine-learning-pipelines.md)
* [자습서: 일괄 처리 채점에 Azure Machine Learning 파이프라인 사용](tutorial-pipeline-batch-scoring-classification.md)
* [예제: 기계 학습 파이프라인에 대한 Jupyter Notebook 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [예제: 자동화된 기계 학습을 사용하는 파이프라인](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>학습 작업을 제출할 때 수행 되는 작업 이해

Azure 교육 수명 주기는 다음으로 구성 됩니다.

1. _Amlignore_ 또는 _. .gitignore_ 에 지정 된 파일을 무시 하 고 프로젝트 폴더의 파일을 압축 합니다.
1. 계산 클러스터 확장 
1. Dockerfile을 계산 노드에 빌드 또는 다운로드 
    1. 시스템은 다음의 해시를 계산 합니다. 
        - 기본 이미지 
        - 사용자 지정 docker 단계 ( [사용자 지정 docker 기본 이미지를 사용 하 여 모델 배포](./how-to-deploy-custom-docker-image.md)참조)
        - Conda definition YAML ( [Azure Machine Learning에서 소프트웨어 환경 만들기 & 사용](./how-to-use-environments.md)을 참조 하세요.)
    1. 시스템은 작업 영역 Azure Container Registry (ACR) 조회에서이 해시를 키로 사용 합니다.
    1. 찾을 수 없는 경우 전역 ACR에서 일치 하는 항목을 찾습니다.
    1. 이 파일을 찾을 수 없는 경우 시스템은 새 이미지를 빌드합니다 (작업 영역 ACR에 캐시 및 등록 됨).
1. 계산 노드의 임시 저장소에 압축 된 프로젝트 파일 다운로드
1. 프로젝트 파일 압축 풀기
1. 실행 하는 계산 노드 `python <entry script> <arguments>`
1. `./outputs`작업 영역과 연결 된 저장소 계정에 기록 된 로그, 모델 파일 및 기타 파일 저장
1. 임시 저장소 제거를 포함 하 여 계산 확장 

로컬 컴퓨터에서 학습 하도록 선택 하는 경우 ("로컬 실행으로 구성") Docker를 사용할 필요가 없습니다. 선택 하는 경우 Docker를 로컬로 사용할 수 있습니다 (예제는 [ML 파이프라인 구성](./how-to-debug-pipelines.md) 섹션 참조).

## <a name="r-sdk-preview"></a>R SDK (미리 보기)

R SDK는 Azure Machine Learning에서 R 언어를 사용할 수 있습니다. SDK는 그물형 패키지를 사용하여 Azure Machine Learning의 Python SDK에 바인딩합니다. 이를 통해 모든 R 환경에서 Python SDK에 구현 된 핵심 개체 및 메서드에 액세스할 수 있습니다.

자세한 내용은 다음 문서를 참조하세요.

* [R용 Azure Machine Learning SDK 참조](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

디자이너를 사용 하면 웹 브라우저에서 끌어서 놓기 인터페이스를 사용 하 여 모델을 학습 시킬 수 있습니다.

+ [디자이너란?](concept-designer.md)
+ [자습서: 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>많은 모델 솔루션 가속기

[많은 모델 솔루션 가속기](https://aka.ms/many-models)(미리 보기)는 Azure Machine Learning을 기준으로 하며 수백 또는 수천 개의 기계 학습 모델을 학습, 운영 및 관리할 수 있습니다.

예를 들어 다음 시나리오에서 __각 인스턴스 또는 개인__ 에 대한 모델을 빌드하면 결과가 향상될 수 있습니다.

* 개별 스토어별 매출 예측
* 수백 개의 유정에 대한 예측 유지 관리
* 개별 사용자 환경 조정

자세한 내용은 GitHub의 [많은 모델 솔루션 가속기](https://aka.ms/many-models)를 참조하세요.

## <a name="cli"></a>CLI

기계 학습 CLI는 Azure CLI용 확장이며 Azure Machine Learning을 사용하기 위한 플랫폼 간 CLI 명령을 제공합니다. 일반적으로 CLI를 사용하여 기계 학습 모델 학습 등의 작업을 자동화할 수 있습니다.

* [Azure Machine Learning용 CLI 확장 사용](reference-azure-machine-learning-cli.md)
* [Azure의 MLOps](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS 코드

VS Code 확장을 사용 하 여 교육 작업을 실행 하 고 관리할 수 있습니다. 자세한 내용은 [VS Code 리소스 관리 방법 가이드](how-to-manage-resources-vscode.md#experiments) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[학습 실행을 구성](how-to-set-up-training-targets.md)하는 방법에 대해 알아봅니다.