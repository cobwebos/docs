---
title: 'Azure DevOps (预览版的持续部署) '
titleSuffix: Azure Cognitive Services
description: 本文介绍如何为自定义命令应用程序设置持续部署。 你可以创建脚本来支持持续部署工作流。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: bd1ab5110313380c90e71ed161935c7274a845b7
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839242"
---
# <a name="continuous-deployment-with-azure-devops"></a>使用 Azure DevOps 持续部署

本文介绍如何为自定义命令应用程序设置持续部署。 提供支持 CI/CD 工作流的脚本。

## <a name="prerequisite"></a>先决条件
> [!div class = "checklist"]
> * 用于开发 (开发的自定义命令应用程序) 
> * 生产 (生产) 的自定义命令应用程序
> * 注册 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)

## <a name="exportimportpublish"></a>导出/导入/发布

脚本托管于 [认知服务语音助手-自定义命令](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)。 将 bash 目录中的脚本克隆到存储库。 请确保保留相同的路径。

### <a name="set-up-a-pipeline"></a>设置管道 

1. 中转到 **Azure DevOps** ，并单击 "新建管道"
1. 在 " **连接** " 部分中，选择这些脚本所在的存储库所在的位置
1. 在 " **选择** " 部分中，选择你的存储库
1. 在 " **配置** " 部分，选择 "Starter 管道"
1. 接下来，你将获得一个带有 YAML 文件的编辑器，请将 "步骤" 部分替换为此脚本。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. 请注意，这些脚本假设你使用的是区域，如果不是这样，则会 `westus2` 相应地更新任务的参数

    > [!div class="mx-imgBorder"]
    > ![突出显示参数中的区域值的屏幕截图。](media/custom-commands/cicd-new-pipeline-yaml.png)

1. 在 "保存并运行" 按钮中，打开下拉列表，然后单击 "保存"

### <a name="hook-up-the-pipeline-with-your-application"></a>与应用程序挂钩管道

1. 导航到管道的主页。
1. 在右上角下拉列表中，选择 " **编辑管道**"。 它可用于 YAML 编辑器。 
1. 在右上角 "运行" 按钮旁，选择 " **变量**"。 单击 " **新建变量**"。
1. 添加以下变量：
    
    | 变量 | 描述 |
    | ------- | --------------- | ----------- |
    | SourceAppId | 开发应用程序的 ID |
    | TargetAppId | 生产应用程序的 ID |
    | SubscriptionKey | 用于这两个应用程序的订阅密钥 |
    | 环境 | 应用程序的区域性 (即 en-us)  |

    > [!div class="mx-imgBorder"]
    > ![发送活动有效负载](media/custom-commands/cicd-edit-pipeline-variables.png)

1. 单击 "运行"，然后单击正在运行的 "作业"。 

    应会看到运行的任务列表，其中包含： "导出源应用"、"导入到目标应用" & "训练和发布目标应用"

## <a name="deploy-from-source-code"></a>从源代码进行部署

如果要将应用程序的定义保留在存储库中，我们提供了来自源代码的部署的脚本。 由于脚本在 bash 中，因此，如果您使用的是 Windows，则需要安装 [Linux 子系统](https://docs.microsoft.com/windows/wsl/install-win10)。

脚本托管于 [认知服务语音助手-自定义命令](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)。 将 bash 目录中的脚本克隆到存储库。 请确保保留相同的路径。

### <a name="prepare-your-repository"></a>准备存储库

1. 为应用程序创建一个目录，在本示例中，创建一个名为 "apps" 的目录。
1. 更新以下 bash 脚本的参数，并运行。 它会将应用程序的对话框模型导入到文件 myapp.js
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | 参数 | 描述 |
    | ------- | --------------- | ----------- |
    | region | 应用程序的区域，即 westus2。 |
    | subscriptionkey | 语音资源的订阅密钥。 |
    | appid | 要导出的自定义命令的应用程序 ID。 |

1. 将这些更改推送到存储库。

### <a name="set-up-a-pipeline"></a>设置管道 

1. 中转到 **Azure DevOps** ，并单击 "新建管道"
1. 在 " **连接** " 部分中，选择这些脚本所在的存储库所在的位置
1. 在 " **选择** " 部分中，选择你的存储库
1. 在 " **配置** " 部分，选择 "Starter 管道"
1. 接下来，你将获得一个带有 YAML 文件的编辑器，请将 "步骤" 部分替换为此脚本。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > 这些脚本假设你正在使用区域 westus2，如果不是这样，则会相应地更新任务的参数

1. 在 "保存并运行" 按钮中，打开下拉列表，然后单击 "保存"

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>将管道与目标应用程序挂钩

1. 导航到管道的主页。
1. 在右上角下拉列表中，选择 " **编辑管道**"。 它可用于 YAML 编辑器。 
1. 在右上角 "运行" 按钮旁，选择 " **变量**"。 单击 " **新建变量**"。
1. 添加以下变量：

    | 变量 | 描述 |
    | ------- | --------------- | ----------- |
    | TargetAppId | 生产应用程序的 ID |
    | SubscriptionKey | 用于这两个应用程序的订阅密钥 |
    | 环境 | 应用程序的区域性 (即 en-us)  |

1. 单击 "运行"，然后单击正在运行的 "作业"。
    应会看到运行的任务列表，其中包含： "导入应用" & "训练和发布应用"

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [请参阅 GitHub 上的示例](https://aka.ms/speech/cc-samples)