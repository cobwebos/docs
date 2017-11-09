---
title: "自动删除资源组 | Microsoft Docs"
description: "Azure 自动化方案的 PowerShell 工作流版本，包括用于删除订阅中所有资源组的 Runbook。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: b848e345-fd5d-4b9d-bc57-3fe41d2ddb5c
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2016
ms.author: magoedte
ms.openlocfilehash: 8b23e55a597f293b17183e80eea6c2763aabe9ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Azure 自动化方案 - 自动删除资源组
许多客户会创建多个资源组。 有些资源组专门用于管理生产应用程序，还有一些资源组可能用于开发、测试和过渡环境。 自动部署这些资源是一回事，但按一下鼠标就能取消配置资源组则是另一回事。 使用 Azure 自动化可以简化这种常见的管理任务。 如果使用的 Azure 订阅由于会员优惠制度（例如 MSDN 或 Microsoft Partner Network Cloud Essentials 计划）而实施支出限制，则使用自动化就很有帮助。

此方案基于某个 PowerShell Runbook，旨在删除订阅中指定的一个或多个资源组。 该 Runbook 的默认设置为先测试再继续。 这可以确保在准备完成此过程之前不会意外删除资源组。   

## <a name="getting-the-scenario"></a>获取方案
此方案包括可从 [PowerShell 库](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript)下载的 PowerShell Runbook。 也可以直接在 Azure 门户中从 [Runbook 库](automation-runbook-gallery.md)导入该 Runbook。<br><br>

| Runbook | 说明 |
| --- | --- |
| Remove-ResourceGroup |从订阅中删除一个或多个 Azure 资源组和关联的资源。 |

<br>
为此 Runbook 定义了以下输入参数：

| 参数 | 说明 |
| --- | --- |
| NameFilter（必需） |指定一个名称筛选器来限制要删除的资源组。 可以使用逗号分隔的列表传递多个值。<br>筛选器不区分大小写，会匹配任何包含该字符串的资源组。 |
| PreviewMode（可选） |执行此 Runbook 可查看将删除哪些资源组，但不执行任何操作。<br>默认值为 **true**，可帮助避免意外删除传递给 Runbook 的一个或多个资源组。 |

## <a name="install-and-configure-this-scenario"></a>安装和配置此方案
### <a name="prerequisites"></a>先决条件
此 Runbook 使用 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)进行身份验证。    

### <a name="install-and-publish-the-runbooks"></a>安装和发布 Runbook
下载 Runbook 后，可以使用[导入 Runbook 过程](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation)中的过程导入它。 在成功将该 Runbook 导入自动化帐户后，请发布该 Runbook。

## <a name="using-the-runbook"></a>使用 Runbook
以下步骤将引导执行此 Runbook，帮助你熟悉其工作原理。 在本示例中，我们只测试 Runbook，而不实际删除资源组。  

1. 在 Azure 门户中打开自动化帐户，并单击“Runbook”。
2. 选择 **Remove-ResourceGroup** Runbook，并单击“启动”。
3. 启动 Runbook 时，“启动 Runbook”边栏选项卡会打开，可在其中配置参数。 输入订阅中可用于测试并且意外删除后不会造成任何损害的资源组的名称。<br> ![Remove-ResouceGroup 参数](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)

   > [!NOTE]
   > 确保 **Previewmode** 设置为 **true**，以免删除选定的资源组。  **请注意**，此 Runbook 不会删除运行此 Runbook 的自动化帐户所在的资源组。  
   >
   >
4. 配置所有参数值后，单击“确定”，此 Runbook 会排入队列等待执行。  

若要在 Azure 门户中查看 **Remove-ResourceGroup** Runbook 作业的详细信息，请在 Runbook 中选择“作业”。 作业摘要显示输入参数和输出流，此外，还显示有关作业的一般信息以及发生的任何异常。<br> ![Remove-ResourceGroup Runbook 作业状态](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png)。

“作业摘要”包括来自输出、警告和错误流的消息。 选择“输出”可查看 Runbook 执行的详细结果。<br> ![Remove-ResourceGroup Runbook 输出结果](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>后续步骤
* 若要开始创建自己的 Runbook，请参阅 [Creating or importing a runbook in Azure Automation](automation-creating-importing-runbook.md)（在 Azure 自动化中创建或导入 Runbook）。
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)。
