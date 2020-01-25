---
title: Azure DevOps Services로 데이터 과학 코드 테스트 - Team Data Science Process
description: Team Data Science Process 및 Azure DevOps Services와 함께 UCI 성인 소득 예측 데이터 세트를 활용하여 Azure에서 데이터 과학 코드 테스트
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721940"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Team Data Science Process 및 Azure DevOps Services를 통해 Azure에서 데이터 과학 코드 테스트
이 문서에서는 데이터 과학 워크플로에서 코드를 테스트하기 위한 예비 지침을 제공합니다. 이러한 테스트를 통해 데이터 과학자는 체계적이며 효율적인 방식으로 품질 및 예상되는 코드의 결과를 확인할 수 있습니다. 코드 테스트가 어떻게 수행되는지 보여 주기 위해 이전에 게시한 [UCI 성인 소득 데이터 세트를 사용하는 TDSP(Team Data Science Process) 프로젝트](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)를 사용합니다. 

## <a name="introduction-on-code-testing"></a>코드 테스트 소개
“단위 테스트”는 소프트웨어 개발을 위한 오랜 관행입니다. 但对于数据科学，通常并不清楚 "单元测试" 的含义，以及应该如何针对数据科学生命周期的不同阶段测试代码，例如：

* 데이터 준비
* 데이터 품질 검사
* 모델링
* 모델 배포 

이 문서에서는 “단위 테스트”라는 용어를 “코드 테스트”로 대체합니다. 테스트란 데이터 과학 수명 주기의 특정 단계에 대한 코드가 결과를 “예상대로” 생성하는지 평가하는 데 도움이 되는 함수를 가리킵니다. 테스트를 작성하는 사람은 함수의 결과에 따라 “예상대로”가 무엇인지를 정의합니다(예: 데이터 품질 확인 또는 모델링).

이 문서는 유용한 리소스로 참조를 제공합니다.

## <a name="azure-devops-for-the-testing-framework"></a>테스트 프레임워크에 대한 Azure DevOps
이 문서에서는 Azure DevOps를 사용하여 테스트를 수행하고 자동화하는 방법을 설명합니다. 다른 도구를 사용할 수도 있습니다. 또한 Azure DevOps 및 빌드 에이전트를 사용하여 자동 빌드를 설정하는 방법을 보여 줍니다. 빌드 에이전트의 경우 Azure DSVM(Data Science Virtual Machines)를 사용합니다.

## <a name="flow-of-code-testing"></a>코드 테스트의 흐름
데이터 과학 프로젝트에서 코드 테스트의 전체 워크플로는 다음과 같습니다. 

![코드 테스트의 흐름 차트](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>자세한 단계

다음 단계를 사용하여 빌드 에이전트와 Azure DevOps를 사용하여 자동화된 빌드 및 코드 테스트를 설정하고 실행합니다.

1. Visual Studio 데스크톱 애플리케이션에서 프로젝트를 만듭니다.

    ![Visual Studio의 “새 프로젝트 만들기” 화면](./media/code-test/create_project.PNG)

   프로젝트를 만든 후 오른쪽 창에 솔루션 탐색기에서 찾을 수 있습니다.
    
    ![프로젝트 만들기 단계](./media/code-test/create_python_project_in_vs.PNG)

    ![솔루션 탐색기](./media/code-test/solution_explorer_in_vs.PNG)

1. Azure DevOps 프로젝트 코드 리포지토리로 프로젝트 코드를 피드합니다. 

    ![프로젝트 코드 리포지토리](./media/code-test/create_repo.PNG)

1. 데이터 수집, 기능 엔지니어링 및 레이블 열 만들기와 같은 일부 데이터 준비 작업을 완료했다고 가정합니다. 사용자의 코드가 원하는 결과를 생성하는지 확인하려고 합니다. 데이터 처리 코드가 올바르게 작동하는지 여부를 테스트하는 데 사용할 수 있는 일부 코드는 다음과 같습니다.

    * 열 이름이 올바른지 확인합니다.
    
      ![일치하는 열 이름에 대한 코드](./media/code-test/check_column_names.PNG)

    * 응답 수준이 올바른지 확인합니다.

      ![일치하는 수준에 대한 코드](./media/code-test/check_response_levels.PNG)

    * 응답 비율이 적당한지 확인합니다.

      ![응답 비율에 대한 코드](./media/code-test/check_response_percentage.PNG)

    * 데이터에 있는 각 열의 누락 비율을 확인합니다.
    
      ![누락된 속도에 대한 코드](./media/code-test/check_missing_rate.PNG)


1. 데이터 처리 및 기능 엔지니어링 작업을 완료했고 우수한 모델을 학습한 후에는 학습한 모델이 새 데이터 세트를 올바르게 계산하는지 확인합니다. 다음 두 가지 테스트를 사용하여 예측 수준 및 레이블 값의 분포를 확인할 수 있습니다.

    * 예측 수준을 확인합니다.
    
      ![예측 수준 검사에 대한 코드](./media/code-test/check_prediction_levels.PNG)

    * 예측 값의 분포를 확인합니다.

      ![예측 값 검사에 대한 코드](./media/code-test/check_prediction_values.PNG)

1. **test_funcs.py**라는 Python 스크립트에 모든 테스트 함수를 배치합니다.

    ![테스트 함수에 대한 Python 스크립트](./media/code-test/create_file_test_func.PNG)


1. 테스트 코드가 준비되면 Visual Studio에서 테스트 환경을 설정할 수 있습니다.

   **test1.py**라고 하는 Python 파일을 만듭니다. 이 파일에서 수행하려는 모든 테스트를 포함하는 클래스를 만듭니다. 다음 예제에는 준비된 6개의 테스트가 나와 있습니다.
    
    ![클래스의 테스트 목록이 포함된 Python 파일](./media/code-test/create_file_test1_class.PNG)

1. 그러한 테스트는 **codetest.testCase**를 클래스 이름 뒤에 배치하여 자동으로 검색할 수 있습니다. 오른쪽 창에서 테스트 탐색기를 열고 **모두 실행**을 선택합니다. 모든 테스트가 순차적으로 실행되고 테스트가 성공적인지 아닌지를 사용자에게 알립니다.

    ![테스트 실행](./media/code-test/run_tests.PNG)

1. Git 명령을 사용하여 프로젝트 리포지토리에 사용자 코드를 체크 인합니다. 가장 최근 작업이 Azure DevOps에서 즉시 반영됩니다.

    ![코드에서의 검사를 위한 Git 명령](./media/code-test/git_check_in.PNG)

    ![Azure DevOps의 가장 최근 작업](./media/code-test/git_check_in_most_recent_work.PNG)

1. 자동 빌드를 설정하고 Azure DevOps에서 테스트합니다.

    a. 프로젝트 리포지토리에서 **빌드 및 릴리스**를 선택한 다음, **+새로 만들기**를 선택하여 새 빌드 프로세스를 만듭니다.

    ![새 빌드 프로세스를 시작하기 위한 선택 항목](./media/code-test/create_new_build.PNG)

    b. 화면에 표시되는 메시지에 따라 소스 코드 위치, 프로젝트 이름, 리포지토리 및 분기 정보를 선택합니다.
    
    ![원본, 이름, 리포지토리 및 분기 정보](./media/code-test/fill_in_build_info.PNG)

    다. 템플릿을 선택합니다. Python 프로젝트 템플릿이 없으므로 **빈 프로세스**를 선택하여 시작합니다. 

    ![템플릿 목록 및 “빈 프로세스” 단추](./media/code-test/start_empty_process_template.PNG)

    d. 빌드에 이름을 지정하고 에이전트를 선택합니다. 如果要使用 DSVM 来完成生成过程，则可以在此处选择默认值。 에이전트 설정에 대한 자세한 내용은 [빌드 및 릴리스 에이전트](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts)를 참조하세요.
    
    ![빌드 및 에이전트 선택](./media/code-test/select_agent.PNG)

    e. 왼쪽 창에서 **+** 를 선택하여 이 빌드 단계에 대한 작업을 추가합니다. 由于我们要运行 Python 脚本**test1.py**来完成所有检查，因此此任务使用 PowerShell 命令来运行 Python 代码。
    
    ![선택된 PowerShell이 포함된 “작업 추가” 창](./media/code-test/add_task_powershell.PNG)

    f. PowerShell 세부 정보에서 PowerShell의 이름 및 버전 같은 필요한 정보를 입력합니다. 형식으로 **인라인 스크립트**를 선택합니다. 
    
    **인라인 스크립트** 아래 상자에서 **python test1.py**를 입력할 수 있습니다. Python에 대한 환경 변수가 올바르게 설정되어 있는지 확인합니다. Python의 다른 버전 또는 커널이 필요한 경우 그림에 나와 있는 것처럼 경로를 명시적으로 지정할 수 있습니다. 
    
    ![PowerShell 세부 정보](./media/code-test/powershell_scripts.PNG)

    g. 选择 "**保存 & 队列**" 以完成生成管道过程。

    ![“저장 및 큐” 단추](./media/code-test/save_and_queue_build_definition.PNG)

이제 코드 리포지토리에 새 커밋이 푸시될 때마다 빌드 프로세스가 자동으로 시작됩니다. （此处我们使用 master 作为存储库，但你可以定义任何分支。）此过程在代理计算机中运行**test1.py**文件，以确保代码中定义的所有内容都能正常运行。 

알림이 제대로 설정되어 있는 경우 빌드가 완료되면 사용자에게 이메일로 알립니다. 또한 Azure DevOps에서 빌드 상태를 확인할 수 있습니다. 실패한 경우 빌드의 세부 정보를 확인 수 있으며 끊어진 부분을 찾을 수 있습니다.

![빌드 성공의 이메일 알림](./media/code-test/email_build_succeed.PNG)

![빌드 성공의 Azure DevOps 알림](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>다음 단계
* [UCI 소득 예측 리포지토리](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)를 참조하여 데이터 과학 시나리오에 대한 단위 테스트의 구체적인 예를 확인하세요.
* 앞서 나온 사용자의 데이터 과학 프로젝트에서 UCI 소득 예측 시나리오의 개요 및 예제를 따릅니다.

## <a name="references"></a>참조
* [Team Data Science Process](https://aka.ms/tdsp)
* [Visual Studio 테스트 도구](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps 테스트 리소스](https://www.visualstudio.com/team-services/)
* [데이터 과학 가상 머신](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
