---
title: 在 Azure 上使用 UCI 成人收入预测数据集进行数据科学代码测试 - Team Data Science Process 与 Visual Studio Team Services
description: 使用 UCI 成人收入预测数据进行数据科学代码测试
services: machine-learning, team-data-science-process
documentationcenter: ''
author: weig
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2018
ms.author: weig
ms.openlocfilehash: 46d156ce09b1ebcdcceb27ede6e7fa1595d30da6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439491"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>使用 UCI 成人收入预测数据集进行数据科学代码测试
本文提供的初步指导适用于在数据科学工作流中测试代码。 数据科学家可以通过此类测试以系统且有效的方式查看其代码的质量和预期结果。 我们使用的 Team Data Science Process (TDSP) [项目使用 UCI 成人收入数据集](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)，该数据集是我们以前发布的，目的是演示代码测试方法。 

## <a name="introduction-on-code-testing"></a>代码测试简介
“单元测试”是一种长期存在的用于软件开发的做法。 但对于数据科学来说，通常并不清楚此做法的具体涵义，也不清楚应如何进行代码测试以应对不同阶段的数据科学生命周期，例如：

* 数据准备工作
* 数据质量检查
* 建模
* 模型部署 

本文将术语“单元测试”替换为“代码测试”。 代码测试是指以函数的形式进行测试，以便评估数据科学生命周期的某个步骤的代码是否生成“预期的”结果。 编写测试的人订阅负责定义什么才是“预期的”结果，具体取决于函数（例如，数据质量检查或建模）的结果。

本文提供的参考可以作为有用的资源。

## <a name="visual-studio-team-services-for-the-testing-framework"></a>使用 Visual Studio Team Services 作为测试框架
本文介绍如何使用 Visual Studio Team Services (VSTS) 来执行测试并使之自动化。 你可以自行决定是否使用替代工具。 我们还介绍如何使用 VSTS 和生成代理来设置自动生成。 对于生成代理，我们使用 Azure 数据科学虚拟机 (DSVM)。

## <a name="flow-of-code-testing"></a>代码测试流
在数据科学项目中进行代码测试的总体工作流如下所示： 

![代码测试流程图](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>详细步骤

请执行以下步骤，使用生成代理和 VSTS 来设置并运行代码测试和自动化生成：

1. 在 Visual Studio 桌面应用程序中创建一个项目：

    ![Visual Studio 中的“创建新项目”屏幕](./media/code-test/create_project.PNG)

   创建项目以后，即可在右窗格的解决方案资源管理器中找到它：
    
    ![创建项目的步骤](./media/code-test/create_python_project_in_vs.PNG)

    ![解决方案资源管理器](./media/code-test/solution_explorer_in_vs.PNG)

1. 将项目代码馈送到 VSTS 项目代码存储库中： 

    ![项目代码存储库](./media/code-test/create_repo.PNG)

1. 假定你已经完成了一些数据准备工作，例如数据引入、特征工程、标签列的创建。 你需要确保代码生成预期的结果。 下面是一些可以用来测试数据处理代码是否正常运行的代码：

    * 检查列名是否正确：
    
      ![进行列名匹配的代码](./media/code-test/check_column_names.PNG)

    * 检查响应级别是否正确：

      ![进行级别匹配的代码](./media/code-test/check_response_levels.PNG)

    * 检查响应百分比是否合理：

      ![检查响应百分比的代码](./media/code-test/check_response_percentage.PNG)

    * 检查数据中每个列的未命中率：
    
      ![检查未命中率的代码](./media/code-test/check_missing_rate.PNG)


1. 完成数据处理和特征工程工作并训练出良好模型以后，请确保训练的模型能够正确地为新数据集评分。 可以使用下述两项测试来检查标签值的预测级别和分布：

    * 检查预测级别：
    
      ![用于检查预测级别的代码](./media/code-test/check_prediction_levels.PNG)

    * 检查预测值的分布：

      ![用于检查预测值的代码](./media/code-test/check_prediction_values.PNG)

1. 将所有测试函数一起放到名为 **test_funcs.py** 的 Python 脚本中：

    ![用于测试函数的 Python 脚本](./media/code-test/create_file_test_func.PNG)


1. 准备好测试代码以后，即可在 Visual Studio 中设置测试环境。

   创建名为 **test1.py** 的 Python 文件。 在此文件中创建一个类，其中包括要完成的所有测试。 以下示例显示了六个准备好的测试：
    
    ![类中包含一系列测试的 Python 文件](./media/code-test/create_file_test1_class.PNG)

1. 如果将 **codetest.testCase** 置于类名之后，则可自动发现这些测试。 打开右窗格中的测试资源管理器，然后选择“全部运行”。 所有测试都会按顺序运行，并且会告知测试是否成功。

    ![运行测试](./media/code-test/run_tests.PNG)

1. 使用 Git 命令将代码签入项目存储库中。 最新的工作会很快反映在 VSTS 中。

    ![用于签入代码的 Git 命令](./media/code-test/git_check_in.PNG)

    ![VSTS 中的最新工作](./media/code-test/git_check_in_most_recent_work.PNG)

1. 在 VSTS 中设置自动生成和测试：

    a. 在项目存储库中选择“生成并发布”，然后选择“+新建”以创建新的生成过程。

       ![Selections for starting a new build process](./media/code-test/create_new_build.PNG)

    b. 按提示选择源代码位置、项目名称、存储库和分库信息。
    
       ![Source, name, repository, and branch information](./media/code-test/fill_in_build_info.PNG)

    c. 选择模板。 由于没有 Python 项目模板，请一开始选择“空进程”。 

       ![List of templates and "Empty process" button](./media/code-test/start_empty_process_template.PNG)

    d. 为生成命名并选择代理。 如果需要使用 DSVM 来完成生成过程，可以选择此处的默认设置。 有关如何设置代理的详细信息，请参阅 [Build and release agents](https://docs.microsoft.com/vsts/build-release/concepts/agents/agents?view=vsts)（生成并发布代理）。
    
       ![Build and agent selections](./media/code-test/select_agent.PNG)

    e. 在左窗格中选择“+”，添加适合此生成阶段的任务。 由于我们将运行 Python 脚本 **test1.py** 来完成所有检查，因此此任务将使用 PowerShell 命令来运行 Python 代码。
    
       !["Add tasks" pane with PowerShell selected](./media/code-test/add_task_powershell.PNG)

    f. 在 PowerShell 详细信息中填写所需的信息，例如 PowerShell 的名称和版本。 选择“内联脚本”作为类型。 
    
       In the box under **Inline Script**, you can type **python test1.py**. Make sure the environment variable is set up correctly for Python. If you need a different version or kernel of Python, you can explicitly specify the path as shown in the figure: 
    
       ![PowerShell details](./media/code-test/powershell_scripts.PNG)

    g. 选择“保存并排队”，完成生成定义过程。

       !["Save & queue" button](./media/code-test/save_and_queue_build_definition.PNG)

现在，每次将新提交的内容推送到代码存储库时，生成过程就会自动启动。 （在这里，我们使用 master 作为存储库，但你可以定义任何分库。）此过程运行代理计算机中的 **test1.py** 文件，目的是确保代码中定义的所有内容都能正确运行。 

如果警报设置正确，系统会在生成完成以后通过电子邮件通知你。 也可在 VSTS 中检查生成状态。 如果生成失败，则可检查生成的详细信息，找出出错的片段。

![生成成功的电子邮件通知](./media/code-test/email_build_succeed.PNG)

![生成成功的 VSTS 通知](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>后续步骤
* 如需通过具体的示例来了解数据科学方案的单元测试，请参阅 [UCI 收入预测存储库](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)。
* 请按你自己的数据科学项目的 UCI 收入预测方案中的上述大纲和示例进行操作。

## <a name="references"></a>参考
* [团队数据科学过程](https://aka.ms/tdsp)
* [Visual Studio 测试工具](https://www.visualstudio.com/vs/features/testing-tools/)
* [VSTS 测试资源](https://www.visualstudio.com/team-services/)
* [数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)