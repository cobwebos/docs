---
title: "使用 Azure CLI 创建策略分配以识别 Azure 环境中的不合规资源 | Microsoft Docs"
description: "使用 PowerShell 创建 Azure 策略分配以识别不合规的资源。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 92b532691986e72eca68d9bc3033e20ff8ffef3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>使用 Azure CLI 创建策略分配以识别 Azure 环境中的不合规资源

了解 Azure 中的符合性的第一步需要了解当前资源所处的位置。 本快速入门教程逐步讲解如何创建策略分配，以通过策略定义识别不合规的资源（需要 SQL Server 12.0 版）。 完成此过程后，即可成功识别哪些服务器的版本不同，因此在本质上不合规。

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详述如何使用 Azure CLI 创建策略分配，以识别 Azure 环境中的不合规资源。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。
 
## <a name="opt-in-to-azure-policy"></a>选用 Azure 策略

Azure 策略目前以受限预览版提供，因此需要注册才能请求访问。

1. 通过 https://aka.ms/getpolicy 转到“Azure 策略”，在左窗格中选择“注册”。

   ![搜索策略](media/assign-policy-definition/sign-up.png)

2. 通过选择想要使用的“订阅”列表中的订阅选用 Azure 策略。 然后选择“注册”。

   ![选用 Azure 策略](media/assign-policy-definition/preview-opt-in.png)

   可能需要一两天时间来接受注册请求，具体视需求而定。 接受请求后，会收到电子邮件通知，告知可以开始使用该服务。

## <a name="create-a-policy-assignment"></a>创建策略分配

本快速入门创建一个策略分配，并分配“需要 SQL Server 12.0 版”定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

遵循以下步骤创建新的策略分配。

查看所有策略定义，找到“需要 SQL Server 12.0 版”策略定义：

```azurecli
az policy definition list
```

Azure 策略附带可以使用的内置策略定义。 内置策略定义如下：

- 强制实施标记和值
- 应用标记和值
- 需要 SQL Server 12.0 版

接下来，提供以下信息并运行以下命令来分配该策略定义：

- 策略分配的“显示名称”。 在本例中，我们使用“需要 SQL Server 12.0 版分配”。
- **策略** – 创建分配时所依据的策略定义。 在本例中，这是“需要 SQL Server 12.0 版”策略定义
- **范围** - 范围确定可对其强制实施策略分配的资源或资源组。 范围可能为从订阅到资源组。

  使用前面在选用 Azure 策略时注册的订阅（或资源组），在本示例中，我们要使用订阅 ID **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** 和资源组名称 **FabrikamOMS**。 请务必将这些值更改为所用的订阅 ID 和资源组名称。 

命令应如下所示：

```azurecli
az policy assignment create --name Require SQL Server version 12.0 Assignment --policy Require SQL Server version 12.0 --scope /subscriptions/ 
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

本教程集合中的其他指南都在本快速入门的基础上制作。 如何打算继续学习后续教程，请不要清除在本快速入门中创建的资源。 如果不打算继续，可运行以下命令删除所创建的分配：

```azurecli
az policy assignment delete –name Require SQL Server version 12.0 Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

若要详细了解如何分配策略来确保**将来**创建的资源合规，请继续学习以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./create-manage-policy.md)

