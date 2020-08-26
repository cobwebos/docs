---
title: 快速入门：在组织外共享 - Azure Data Share
description: 快速入门 - 使用 Azure Data Share 和资源管理器模板与客户和合作伙伴共享数据
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/17/2020
ms.openlocfilehash: 62c800e8da3ab4f99b0933e286debcb05c5c3e22
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88575633"
---
# <a name="tutorial-share-data-using-azure-data-share-and-resource-manager-templates"></a>教程：使用 Azure Data Share 和资源管理器模板共享数据

了解如何从 Azure 存储帐户使用 Azure 资源管理器模板设置新的 Azure Data Share，然后开始与 Azure 组织外部的客户和合作伙伴共享数据。 有关支持的数据存储的列表，请参阅 [Azure Data Share 中支持的数据存储](./supported-data-stores.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/)。

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

该模板中定义了以下资源：

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)：
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts](/rest/api/datashare/accounts/create)
* [Microsoft.DataShare/accounts/shares](/rest/api/datashare/shares/create)
* [Microsoft.DataShare/accounts/shares/dataSets](/rest/api/datashare/datasets/create)
* [Microsoft.DataShare/accounts/shares/invitations](/rest/api/datashare/invitations/create)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

本模板执行以下任务：

* 创建一个存储帐户和一个用作数据共享数据源的容器。
* 创建一个 Data Share 帐户和一个数据共享。
* 创建角色分配，将存储 Blob 数据读取器角色授予源数据共享资源。 请参阅 [Azure Data Share 的角色和要求](./concepts-roles-permissions.md)。
* 将数据集添加到数据共享。
* 将收件人添加到数据共享。
* 为数据共享启用快照计划。

此模板为学习目的而创建。 实际上，通常现有的存储帐户中有一些数据。 你需要在运行模板或脚本创建数据集之前创建角色分配。 有时，在你部署模板时，可能会收到以下错误消息：

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

这是因为部署正在尝试在 RBAC 分配完成之前创建数据集。 尽管出现错误消息，但部署仍可能成功。  你仍然可以浏览[查看部署的资源](#review-deployed-resources)。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开模板。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. 选择或输入以下值：

    * 订阅：选择用于创建数据共享和其他资源的 Azure 订阅。
    * 资源组：选择“新建”以创建新的资源组，或选择现有资源组。
    * **位置**：选择资源组的位置。
    * 项目名称：输入项目名称。  项目名称用于生成资源名称。  请参阅上一个模板中的变量定义。
    * 位置：选择资源的位置。  可以为资源组使用相同的位置。
    * 邀请电子邮件：输入数据共享收件人的 Azure 登录电子邮件地址。  电子邮件别名不起作用。

    其余设置均采用默认值。
1. 选择“我同意上述条款和条件”，然后选择“购买” 。

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 打开已创建的 Data Share 帐户。
1. 在左侧菜单中，选择“发送共享”。  你将看到列出了一个存储帐户。
1. 选择“存储帐户”。  在“详细信息”下，你将看到模板中配置的同步设置。

    ![Azure Data Share 存储帐户同步设置](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. 从顶部选择“邀请”。 你将看到在部署模板时指定的电子邮件地址。 “状态”应为“挂起” 。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组，可以将其删除，这将删除资源组中的资源。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure Data Share 并邀请收件人。 若要了解有关数据使用者如何接受和接收数据共享，请继续学习[接受和接收数据](subscribe-to-data-share.md)教程。
