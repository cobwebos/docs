---
title: "Azure 快速入门 - 创建 Azure 自动化帐户 | Microsoft Docs"
description: "了解如何创建 Azure 自动化帐户和运行 Runbook"
services: automation
author: csand-msft
ms.author: csand
ms.date: 12/13/2017
ms.topic: quickstart
ms.service: automation
ms.openlocfilehash: 17f711a0678c977fd2331f7596e482a0f0db6df2
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2017
---
# <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户

Azure 自动化帐户可以通过 Azure 创建。 此方法提供一个基于浏览器的用户界面，用于创建和配置自动化帐户和相关的资源。 本入门介绍了如何创建自动化帐户并在帐户中运行 Runbook。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录 Azure

## <a name="create-automation-account"></a>创建自动化帐户

1. 单击 Azure 左上角的“新建”按钮。

1. 选择“监视 + 管理”，然后选择“自动化”。

1. 输入帐户信息。 对于“创建 Azure 运行方式帐户”，请选择“是”，以便自动启用可简化向 Azure 进行的身份验证的项目。 完成后，请单击“创建”，启动自动化帐户部署。

    ![在页面中输入有关自动化帐户的信息](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

1. 自动化帐户固定到 Azure 仪表板。 完成部署后，自动化帐户概览会自动打开。

    ![自动化帐户概览](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>运行 Runbook

运行一个教程 Runbook。

1. 单击“流程自动化”下的“Runbook”。 此时会显示 Runbook 列表。 默认情况下，帐户中启用多个教程 Runbook。

    ![自动化帐户 Runbook 列表](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. 选择 **AzureAutomationTutorialScript** Runbook。 此操作打开 Runbook 概览页。

    ![Runbook 概览](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. 单击“启动”，然后在“启动 Runbook”页中单击“确定”以启动 Runbook。

    ![Runbook 作业页](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. 在“作业状态”变为“正在运行”后，单击“输出”或“所有日志”查看 Runbook 作业输出。 对于此教程 Runbook，输出是 Azure 资源的列表。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、自动化帐户以及所有相关资源，可将其删除。 为此，请选择自动化帐户的资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了自动化帐户、启动 Runbook 作业并查看作业状态。 若要详细了解 Azure 自动化，请继续阅读快速入门中有关如何创建第一个 Runbook 的内容。

> [!div class="nextstepaction"]
> [自动化快速入门 - 创建 Runbook](./automation-quickstart-create-runbook.md)
