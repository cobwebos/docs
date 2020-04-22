---
title: Azure 快速入门 - 创建 Azure 自动化帐户 | Microsoft Docs
description: 了解如何创建 Azure 自动化帐户和运行 Runbook
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536975"
---
# <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户

可以使用 Azure 门户通过 Azure 创建 Azure 自动化帐户。Azure 门户是一个基于浏览器的用户界面，在其中可以访问许多资源。 一个自动化帐户可以跨所有区域和订阅管理给定租户的资源。 

本快速入门将引导你创建一个自动化帐户，并在该帐户中运行 Runbook。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

[登录 Azure](https://portal.azure.com)。

## <a name="create-automation-account"></a>创建自动化帐户

1. 为你的 Azure 帐户选择一个名称。 自动化帐户名称在每个区域和资源组中是唯一的。 已删除的自动化帐户的名称可能无法立即可用。

    > [!NOTE]
    > 在用户界面中输入帐户名称后，无法对其进行更改。 

2. 单击 Azure 门户左上角的“创建资源”按钮。 

3. 选择“IT 和管理工具”，然后选择“自动化”。  

4. 输入帐户信息，包括所选的帐户名称。 对于“创建 Azure 运行方式帐户”，请选择“是”，以便自动启用可简化向 Azure 进行的身份验证的项目。   填写信息后，单击“创建”以启动自动化帐户部署。 

    ![在页面中输入有关自动化帐户的信息](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > 有关可将自动化帐户部署到的位置的更新列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)。

5. 部署完成后，单击“所有服务”。 

6. 选择“自动化帐户”，然后选择你创建的自动化帐户。 

    ![自动化帐户概览](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>运行 Runbook

运行一个教程 Runbook。

1. 单击“流程自动化”下的“Runbook”。   此时会显示 Runbook 列表。 默认情况下，帐户中已启用多个教程 Runbook。

    ![自动化帐户 Runbook 列表](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. 选择 **AzureAutomationTutorialScript** Runbook。 此操作打开 Runbook 概览页。

    ![Runbook 概览](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. 单击“启动”，然后在“启动 Runbook”页上，单击“确定”以启动 Runbook。  

    ![Runbook 作业页](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. 在作业状态变为 `Running` 后，单击“输出”或“所有日志”以查看 Runbook 作业输出。   对于此教程 Runbook，输出是 Azure 资源的列表。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了自动化帐户、启动 Runbook 作业并查看作业状态。 若要详细了解 Azure 自动化，请继续阅读快速入门中有关如何创建第一个 Runbook 的内容。

> [!div class="nextstepaction"]
> [自动化快速入门 - 创建 Runbook](./automation-quickstart-create-runbook.md)

