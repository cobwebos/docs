---
title: 通过 Azure 事件网格接收和响应 Key Vault 通知
description: 了解如何将 Key Vault 与 Azure 事件网格集成。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464107"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>如何：通过 Azure 事件网格接收和响应 Key Vault 通知（预览版）

通过将 Key Vault 与 Azure 事件网格集成（目前提供预览版），用户可以在 Key Vault 中存储的机密的状态发生更改时收到通知。 有关该功能的概述，请参阅[通过 Azure 事件网格监视 Key Vault](event-grid-overview.md)。

本指南将演示如何通过 Azure 事件网格接收 Key Vault 通知，以及如何使用 Azure 自动化对状态更改做出响应。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 订阅中的 Key Vault。 可以按照[使用 Azure CLI 在 Azure Key Vault 中设置和检索机密](quick-create-cli.md)中的步骤来快速创建新的 Key Vault

## <a name="concepts"></a>概念

Azure 事件网格是针对云的事件处理服务。 在本指南中，你将订阅 Key Vault 事件，并将事件路由到 Azure 自动化。 当 Key Vault 中的机密之一即将过期时，则会通知事件网格状态更改，并对终结点发出 HTTP POST。 然后，Webhook 会触发 PowerShell 脚本的 Azure 自动化执行。

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户

通过 [Azure 门户](https://portal.azure.com)创建 Azure 自动化帐户。

1.  转到 portal.azure.com 并登录到你的订阅。

1.  在搜索框中，键入“自动化帐户”。

1.  在搜索栏中的下拉列表的“服务”部分下，选择“自动化帐户”。

1.  单击“添加”。

    ![](media/image2.png)

1.  在“添加自动化帐户”边栏选项卡中填写所需信息，然后选择“创建”。

## <a name="create-a-runbook"></a>创建 Runbook

Azure 自动化帐户准备就绪后，创建 Runbook。

![](media/image3.png)

1.  选择刚创建的自动化帐户。

1.  在“过程自动化”下选择“Runbook”。

1.  单击“创建 Runbook”。

1.  为 Runbook 命名，并选择“PowerShell”作为 Runbook 类型。

1.  单击创建的 Runbook，然后选择“编辑”按钮。

1.  输入以下代码（用于测试目的），然后单击“发布”按钮。 这将输出收到的 POST 请求的结果。

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![](media/image4.png)

## <a name="create-a-webhook"></a>创建 Webhook

现在，创建 Webhook 来触发新创建的 Runbook。

1.  从刚发布的 Runbook 的“资源”部分中选择“Webhook”。

1.  单击“添加 Webhook”。

    ![](media/image5.png)

1.  选择“创建新 Webhook”。

1. 为 Webhook 命名，设置过期日期，并复制 URL  。

    > [!IMPORTANT] 
    > 创建 URL 后，无法查看它。 至于本指南的剩余部分，请确保将副本保存到可访问的安全位置。

1. 单击“参数和运行设置”，然后选择“确定”。 不要输入任何参数。 这将启用“创建”按钮。

1. 选择“确定”，然后选择“创建”。

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>创建事件网格订阅

通过 [Azure 门户](https://portal.azure.com)创建事件网格订阅。

1.  使用以下链接打开 Azure 门户： https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  转到 Key Vault，然后选择“事件”选项卡。如果看不到“事件”选项卡，请确保使用[门户的预览版本](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)。

    ![](media/image7.png)

1.  单击“+ 事件订阅”按钮。

1.  为订阅创建一个描述性名称。

1.  选择“事件网格架构”。

1.  “主题资源”应是要监视其状态更改的 Key Vault。

1.  对于“筛选事件类型”，保留所有选中项（“已选中 9 个”）。

1.  对于“终结点类型”，选择“Webhook”。

1.  选择“选择终结点”。 在新的上下文窗格中，将 Webhook URL 从[创建 Webhook](#create-a-webhook) 步骤粘贴到”订阅者终结点“字段中。

1.  在上下文窗格中选择“确认选择”。

1.  选择“创建”。

    ![](media/image8.png)

## <a name="test-and-verify"></a>测试和验证

验证是否已配置事件网格订阅。  此测试假设你已订阅[创建事件网格订阅](#create-an-event-grid-subscription)中的“已创建机密新版本”通知，并且你具有在 Key Vault 中创建机密新版本所需的权限。

![](media/image9.png)

![](media/image10.png)

1.  在 Azure 门户中转到 Key Vault

1.  创建新机密。 出于测试目的，将过期日期设置为“下一天”。

1.  导航到 Key Vault 中的“事件”选项卡。

1.  选择已创建的事件网格订阅。

1.  在“指标”下，查看是否捕获了事件。 需要两个事件：SecretNewVersion 和 SecretNearExpiry。 这会验证事件网格是否已成功捕获 Key Vault 中机密的状态更改。

    ![](media/image11.png)

1.  转到 Azure 自动化帐户。

1.  选择“Runbook”选项卡，然后选择已创建的 Runbook。

1.  选择“Webhook”选项卡，然后确认“上次触发时间”时间戳在创建新机密后的 60 秒内。  这可确认事件网格对 Webhook 发出了 POST（其中包含 Key Vault 中状态更改的事件详细信息），并触发了 Webhook。

    ![](media/image12.png)

1. 返回到 Runbook，然后选择“概述”选项卡。

1. 查看最近的作业列表。 应会看到已创建作业且状态为“已完成”。  这可确认 Webhook 触发了 Runbook 来开始执行其脚本。

    ![](media/image13.png)

1. 选择最近的作业并查看从事件网格发送到 Webhook 的 POST 请求。 检查 JSON 并确保 Key Vault 和事件类型的参数正确。 如果 JSON 对象中的“事件类型”参数与 Key Vault 中发生的事件匹配（在本示例中为 Microsoft.KeyVault.SecretNearExpiry），则测试成功。

## <a name="troubleshooting"></a>故障排除

### <a name="unable-to-create-event-subscription"></a>无法创建事件订阅

在 Azure 订阅资源提供程序中重新注册事件网格和 Key Vault 提供程序。 请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md)。

## <a name="next-steps"></a>后续步骤

祝贺你！ 如果执行了上述所有步骤，则现已准备好以编程方式响应 Key Vault 中存储的机密的状态更改。

如果已使用基于轮询的系统来查找 Key Vault 中机密的状态更改，则使用此通知功能进行迁移。 还可以将 Runbook 中的测试脚本替换为代码，以编程方式在机密即将过期时进行续订。

了解更多：

- [Azure Key Vault 概述](key-vault-overview.md)
- [Azure 事件网格概述](../event-grid/overview.md)
- [通过 Azure 事件网格监视 Key Vault（预览版）](event-grid-overview.md)
- [Azure Key Vault 的 Azure 事件网格事件架构（预览版）](../event-grid/event-schema-key-vault.md)
- [Azure 自动化概述](../automation/index.yml)
