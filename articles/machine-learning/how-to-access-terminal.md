---
title: 작업 영역에서 계산 인스턴스 터미널에 액세스 하는 방법
titleSuffix: Azure Machine Learning
description: Git 작업에 대 한 계산 인스턴스에서 터미널을 사용 하 여 패키지를 설치 하 고 커널을 추가 합니다.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101563"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>작업 영역에서 계산 인스턴스 터미널에 액세스

작업 영역에서 계산 인스턴스의 터미널에 액세스 하 여 다음을 수행 합니다.

* Git 및 버전 파일의 파일을 사용 합니다. 이러한 파일은 작업 영역 파일 시스템에 저장 되며 단일 계산 인스턴스로 제한 되지 않습니다.
* 계산 인스턴스에 패키지를 설치 합니다.
* 계산 인스턴스에서 추가 커널을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

## <a name="access-a-terminal"></a>터미널에 액세스

터미널에 액세스하려면 다음을 수행합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 작업 영역 열기
1. 왼쪽에서 **Notebook** 을 선택합니다.
1. **Open terminal** 이미지를 선택 합니다.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="터미널 창 열기":::

1. 계산 인스턴스가 실행 되는 경우 해당 계산 인스턴스에 대 한 터미널 창이 표시 됩니다.
1. 계산 인스턴스가 실행 되 고 있지 않으면 오른쪽의 **compute** 섹션을 사용 하 여 계산 인스턴스를 시작 하거나 만듭니다.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="계산 인스턴스 시작 또는 만들기":::

위의 단계 외에도에서 터미널에 액세스할 수 있습니다.

* RStudio: 왼쪽 위에서 **터미널** 탭을 선택합니다.
* Jupyter Lab:  시작 관리자 탭의 **기타** 제목 아래에 있는 **터미널** 타일을 선택합니다.
* Jupyter:  파일 탭에서 오른쪽 위에 있는 **새로 만들기 > 터미널** 을 선택합니다.
* 컴퓨터에 SSH를 사용 하도록 설정 하는 경우 컴퓨터에 SSH를 사용 합니다.

## <a name="copy-and-paste-in-the-terminal"></a>터미널에서 복사 하 여 붙여넣기

> * Windows: `Ctrl-Insert` 복사 하 여 사용 `Ctrl-Shift-v` 하거나 `Shift-Insert` 붙여 넣을 수 있습니다.
> * Mac OS: `Cmd-c`: 복사, `Cmd-v`: 붙여넣기
> * FireFox/IE에서 클립보드 사용 권한을 제대로 지원하지 않을 수 있습니다.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Git 및 버전 파일의 파일 사용

터미널에서 모든 Git 작업에 액세스 합니다. 모든 Git 파일 및 폴더는 작업 영역 파일 시스템에 저장됩니다. 이 저장소를 사용 하면 작업 영역의 모든 계산 인스턴스에서 이러한 파일을 사용할 수 있습니다.

> [!NOTE]
> 모든 Jupyter 환경에서 볼 수 있도록 **~/cloudfiles/code/Users** 폴더의 어디에나 파일 및 폴더를 추가합니다.

[작업 영역 파일 시스템에 Git 리포지토리를 복제하는 방법](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)에 대해 자세히 알아보세요.

## <a name="install-packages"></a>패키지 설치

 터미널 창에서 패키지를 설치 합니다. Python **3.6-AzureML** 환경에 python 패키지를 설치 합니다.  **R** 환경에 R 패키지를 설치합니다.

또는 Jupyter Notebook 또는 RStudio에 직접 패키지를 설치할 수 있습니다.

* RStudio는 오른쪽 아래에 있는 **패키지** 탭 또는 왼쪽 위의 **콘솔** 탭을 사용합니다.  
* Python: 설치 코드를 추가 하 고 Jupyter Notebook 셀에를 실행 합니다.

> [!NOTE]
> 노트북 내에서 패키지를 관리 하려면 **% pip** 또는 **% conda** magic 함수를 사용 하 여 현재 실행 중인 **커널** 에 패키지를 자동으로 설치 합니다 .이는 모든 패키지를 참조 하는 **! pip** 또는 **! conda** 이 아닌 현재 실행 중인 커널 외부의 패키지를 포함 합니다.

## <a name="add-new-kernels"></a>새 커널 추가

> [!WARNING]
>  계산 인스턴스를 사용자 지정 하는 동안 **azureml_py36** conda 환경 또는 **Python 3.6-azureml** 커널을 삭제 하지 않아야 합니다. Jupyter/JupyterLab 기능에 필요 합니다.

새 Jupyter 커널을 계산 인스턴스에 추가 하려면 다음을 수행 합니다.

1. 터미널 창을 사용하여 새 환경을 만듭니다.  예를 들어 아래 코드는를 만듭니다 `newenv` .

    ```shell
    conda create --name newenv
    ```

1. 환경을 활성화합니다.  예를 들어 `newenv`를 만든 후에는 다음을 수행합니다.

    ```shell
    conda activate newenv
    ```

1. 새 환경에 pip 및 ipykernel 패키지를 설치 하 고 해당 conda env의 커널 만들기

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

[사용할 수 있는 Jupyter 커널](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)을 설치할 수 있습니다.

## <a name="manage-terminal-sessions"></a>터미널 세션 관리

 터미널 도구 모음에서 **활성 세션 보기** 를 선택 하 여 모든 활성 터미널 세션 목록을 표시 합니다. 활성 세션이 없는 경우에는이 탭을 사용할 수 없습니다.

사용 하지 않는 세션을 모두 닫아 계산 인스턴스의 리소스를 보존 합니다.