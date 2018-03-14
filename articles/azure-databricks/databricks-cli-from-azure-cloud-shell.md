---
title: "从 Azure Cloud Shell 中使用 Databricks CLI | Microsoft Docs"
description: "了解如何从 Azure Cloud Shell 中使用 Databricks CLI。"
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 212789e8189abf362bb8eaf12e4c551b113b7adb
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>从 Azure Cloud Shell 中使用 Databricks CLI

了解如何从 Azure Cloud Shell 中使用 Databricks CLI 对 Databricks 执行操作。

## <a name="prerequisites"></a>先决条件

* 一个 Azure Databricks 工作区和群集。 有关说明，请参阅 [Azure Databricks 入门](quickstart-create-databricks-workspace-portal.md)。 

* 在 Databricks 中设置个人访问令牌。 有关说明，请参阅[令牌管理](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)。

## <a name="use-the-azure-cloud-shell"></a>使用 Azure Cloud Shell

1. 登录到 [Azure 门户](https://portal.azure.com)。
 
2. 在右上角单击 **Cloud Shell** 图标。

   ![启动 Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "从 Excel 启动 ODBC")

3. 请确保已选择 **Bash** 作为 Cloud Shell 环境。 可以从下拉列表选项中进行选择，如以下屏幕截图中所示。

   ![启动 Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "从 Excel 启动 ODBC") 

4. 创建可在其中安装 Databtricks CLI 的虚拟环境。 在下面的代码片段中，将创建一个名为 `databrickscli` 的虚拟环境。

       virtualenv -p /usr/bin/python2.7 databrickscli

5. 切换到所创建的虚拟环境。

       source databrickscli/bin/activate

6. 安装 Databricks CLI。

       pip install databricks-cli

7. 使用访问令牌通过 Databricks 设置身份验证，该访问令牌必须已创建并已作为先决条件的一部分列出。 请使用以下命令：

       databricks configure --token

    你会收到以下提示：

    * 系统会提示输入 Databricks 主机。 按格式 `https://eastus2.azuredatabricks.net` 输入值。 在这里，**美国东部 2** 是你在其中创建 Azure Databricks 工作区的 Azure 区域。

    * 系统会提示输入用户名。 输入 **token**。

    * 最后，系统会提示输入密码。 输入前面创建的令牌。

完成这些步骤后，便可以开始从 Azure Cloud Shell 中使用 Databricks CLI 了。

## <a name="use-databricks-cli"></a>使用 Databricks CLI

现在可以开始使用 Databricks CLI。 例如，运行以下命令以列出你在工作区中拥有的所有 Databricks 群集。

    databricks clusters list

还可以使用以下命令访问 Databricks 文件系统 (DBFS)。

    databricks fs ls


有关命令的完整参考，请参阅 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)。


## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure CLI 的详细信息，请参阅 [Azure CLI 概述](../cloud-shell/overview.md)
* 若要查看 Azure CLI 命令的列表，请参阅 [Azure CLI 参考](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
* 若要查看 Databricks CLI 命令的列表，请参阅 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


