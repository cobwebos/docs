---
title: Azure 快速入门 - 创建 Azure 自动化 Runbook | Microsoft Docs
description: 本文可帮助你开始创建 Azure 自动化 Runbook。
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 948ca820347c7cdcd560ade46e850f66b25bc88e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90987292"
---
# <a name="create-an-azure-automation-runbook"></a>创建 Azure 自动化 Runbook

Azure 自动化 Runbook 可以通过 Azure 创建。 此方法提供一个基于浏览器的用户界面，用于创建自动化 Runbook。 本快速入门介绍如何创建、编辑、测试和发布自动化 PowerShell Runbook。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure。

## <a name="create-the-runbook"></a>创建 Runbook

首先，请创建 Runbook。 本快速入门中创建的示例 Runbook 默认输出 `Hello World`。

1. 打开自动化帐户。

1. 单击“流程自动化”下的“Runbook”。  此时会显示 Runbook 列表。

1. 单击列表顶部的“创建 Runbook”。

1. 在“名称”字段中输入 `Hello-World` 作为 Runbook 名称，并在“Runbook 类型”字段中选择“PowerShell”。   

   ![在页面中输入有关自动化 Runbook 的信息](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. 单击“创建”。 此时会创建 Runbook 并打开“编辑 PowerShell Runbook”页。

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="“编辑 PowerShell Runbook”页面的屏幕截图。":::

1. 将以下代码键入或者复制并粘贴到编辑窗格中。 此代码创建名为 `Name`、默认值为 `World` 的可选输入参数，并输出使用以下输入值的字符串：

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. 单击“保存”以保存 Runbook 的草稿副本。

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="“编辑 PowerShell Runbook”页面的屏幕截图。":::

## <a name="test-the-runbook"></a>测试 Runbook

创建 Runbook 之后，必须对其进行测试以验证其是否正常工作。

1. 单击“测试窗格”  打开测试窗格。

1. 输入一个值作为“名称”，然后单击“启动”。 此时会启动测试作业并显示作业状态和输出。

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="“编辑 PowerShell Runbook”页面的屏幕截图。":::

1. 单击右上角的“X”关闭“测试”窗格。 在显示的弹出窗口中选择“确定”。

1. 在“编辑 PowerShell Runbook”页中，单击“发布”将此 Runbook 作为正式版 Runbook 在帐户中发布。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="“编辑 PowerShell Runbook”页面的屏幕截图。":::

## <a name="run-the-runbook"></a>运行 Runbook

Runbook 发布以后，会显示概览页。

1. 在 Runbook 概览页中，单击“启动”打开该 Runbook 的“启动 Runbook”配置页。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="“编辑 PowerShell Runbook”页面的屏幕截图。":::

1. 将“名称”保留为空，以便使用默认值，然后单击“确定”。  此时会提交 Runbook 作业，并显示“作业”页。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="“编辑 PowerShell Runbook”页面的屏幕截图。":::

1. 当作业状态为 `Running` 或 `Completed` 时，单击“输出”打开“输出”窗格并查看 Runbook 输出。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="“编辑 PowerShell Runbook”页面的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

不再需要 Runbook 时，即可将其删除。 为此，请在 Runbook 列表中选择 Runbook，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建、编辑、测试和发布 Runbook，以及如何启动 Runbook 作业。 若要详细了解自动化 Runbook，请继续阅读可以在自动化中创建和使用的不同 Runbook 类型的相关文章。

> [!div class="nextstepaction"]
> [Azure 自动化 Runbook 类型](./automation-runbook-types.md)
