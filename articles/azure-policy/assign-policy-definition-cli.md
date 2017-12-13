---
title: "使用 Azure CLI 创建策略分配以识别 Azure 环境中的不合规资源 | Microsoft Docs"
description: "使用 PowerShell 创建 Azure 策略分配以识别不合规的资源。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 88ceb47d46b66e716c6c263098d5b9458e4aff22
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>使用 Azure CLI 创建策略分配以识别 Azure 环境中的不合规资源

了解 Azure 中符合性的第一步需要了解自己当前资源的状况。 本快速入门教程指导你完成创建策略分配的过程，以识别未使用托管磁盘的虚拟机。

此过程结束时，应已成功识别哪些虚拟机未使用托管磁盘，也就是不符合要求。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-policy-assignment"></a>创建策略分配

在此快速入门中，我们将创建一个策略分配，分配“审核缺少托管磁盘的虚拟机”定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

按照下列步骤操作以创建新的策略分配。

查看所有的策略定义，并查找“缺少托管磁盘的审核虚拟机”策略定义：

```azurecli
az policy definition list
```

Azure 策略附带可供使用的内置策略定义。 内置策略定义如下：

- 强制实施标记和值
- 应用标记和值
- 需要 SQL Server 12.0 版

接下来，提供以下信息并运行以下命令来分配该策略定义：

- 策略分配的显示名称。 在此示例中，我们使用“审核缺少托管磁盘的虚拟机”。
- **策略** - 指的是策略定义，即为用于创建分配的依据。 在此示例中，即策略定义 - 审核缺少托管磁盘的虚拟机
- 范围 - 范围用于确定在其中实施策略分配的资源或资源组。 它可以从订阅延伸至资源组。

  使用之前注册的订阅（或资源组）。 在此示例中，我们使用的订阅 ID 为 bc75htn-a0fhsi-349b-56gh-4fghti-f84852，资源组名称为 FabrikamOMS。 请务必将这些值更改为所用的订阅 ID 和资源组名称。

命令应如下所示：

```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

策略分配是在特定范围内发生的已分配策略。 另外，此范围也是从管理组到资源组。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

查看不符合此新分配的资源：

1. 导航回到“Azure 策略”页。
2. 在左窗格中选择“符合性”，搜索创建的“策略分配”。

   ![策略符合性](media/assign-policy-definition/policy-compliance.png)

   如果存在任何不符合此新分配的现有资源，它们将显示在“不合规资源”选项卡下，如上图所示。

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他指南建立在本快速入门的基础之上。 如何打算继续浏览后续教程，请勿清除本快速入门中创建的资源。 如果不打算继续，则通过运行以下命令删除创建的分配：

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

若要详细了解如何分配策略来确保**将来**创建的资源合规，请继续学习以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./create-manage-policy.md)
