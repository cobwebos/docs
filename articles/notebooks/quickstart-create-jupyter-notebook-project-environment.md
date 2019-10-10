---
title: 创建具有自定义环境的 Azure Notebooks 项目
description: 在配置了一组特定的已安装包和启动脚本的 Azure Notebooks 中创建新项目。
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6ec0d2fae285889a731e9b881ea90f15b96c85f2
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973070"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>快速入门：在自定义环境下创建项目

Azure Notebooks 中的项目由一系列文件（例如笔记本、数据文件、文档、图像等）以及一个可以使用特定设置命令配置的环境组成。 通过使用项目定义环境，将项目克隆到自己的 Azure Notebooks 帐户中的任何人都可以获得重新创建必要环境所需的所有信息。

## <a name="create-a-project"></a>创建一个项目

1. 转到 [Azure Notebooks](https://notebooks.azure.com) 并登录。 （有关详细信息，请参阅[快速入门 - 登录 Azure Notebooks](quickstart-sign-in-azure-notebooks.md)）。

1. 在公用个人资料页面中，选择页面顶部的“我的项目”  ：

    ![浏览器窗口顶部的“我的项目”链接](media/quickstarts/my-projects-link.png)

1. 在“我的项目”页面上，选择“+ 新建项目”（键盘快捷方式：N）；如果浏览器窗口较窄，该按钮可能仅显示为 +    ：

    ![“我的项目”页面上的“新建项目”命令](media/quickstarts/new-project-command.png)

1. 在出现的“创建新项目”弹出窗口中，输入或设置以下详细信息，然后选择“创建”   ：

    - **项目名称**：具有自定义环境的项目
    - **项目 ID**：project-custom-environment
    - **公共项目**：（已清除）
    - **创建 README.md**：（已清除）

1. 几分钟后，Azure Notebooks 导航到新项目。 通过选择“+ 新建”下拉列表（可能仅显示为 **+** ）并选择“笔记本”，向该项目添加笔记本   。

1. 为笔记本命名，例如 *Custom environment.ipynb*，选择“Python 3.6”作为语言，然后选择“新建”   。

## <a name="add-a-custom-setup-step"></a>添加自定义设置步骤

1. 在项目页面上，选择“项目设置”  。

    ![“项目设置”命令](media/quickstarts/project-settings-command.png)

1. 在“项目设置”弹出窗口中，选择“环境”选项卡，然后在“环境设置步骤”下，选择“+ 添加”     ：

    ![用于添加新环境设置步骤的命令](media/quickstarts/environment-add-command.png)

1. “+ 添加”命令可创建步骤，该步骤由操作以及从项目文件中选择的目标文件定义  。 支持以下操作：

    | Operation | 说明 |
    | --- | --- |
    | Requirements.txt | Python 项目在 requirements.txt 文件中定义它们的依赖项。 如果使用此选项，那么，除了从项目的文件列表中选择相应的文件外，还需在出现的附加下拉列表中选择 Python 版本。 如有必要，可选择“取消”返回到项目，上传或创建文件，然后返回到“项目设置” > “环境”选项卡并创建新的步骤    。 完成此步骤后，运行项目中的笔记本时会自动运行 `pip install -r <file>` |
    | Shell 脚本 | 用于指示 bash shell 脚本（通常为具有 *.sh* 扩展名的文件），该脚本包含你希望为初始化环境而运行的所有命令。 |
    | Environment.yml | 使用 conda 管理环境的 Python 项目使用 *environments.yml* 文件来描述依赖项。 如果使用此选项，请从项目的文件列表中选择相应的文件。 |

1. 若要删除任何设置步骤，请选择步骤右侧的“X”  。

1. 完成所有设置步骤后，选择“保存”  。 （选择“取消”可放弃更改）  。

1. 若要测试环境，请创建并运行一个新的笔记本，然后使用依赖于环境中的某个包的语句创建一个代码单元，例如使用 Python `import` 语句。 如果语句成功，则表示在环境中成功安装了必需的包。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure Notebooks 中管理和配置项目](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [教程：创建并运行 Jupyter Notebook 以执行线性回归](tutorial-create-run-jupyter-notebook.md)
