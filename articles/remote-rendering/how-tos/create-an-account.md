---
title: 创建“Azure 远程渲染”帐户
description: 介绍为 Azure 远程呈现创建帐户的步骤
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681293"
---
# <a name="create-an-azure-remote-rendering-account"></a>创建“Azure 远程渲染”帐户

本章指导完成为**Azure 远程呈现**服务创建帐户的步骤。 有效的帐户是完成任何快速入门或教程所必需的。

## <a name="create-an-account"></a>创建帐户

为 Azure 远程呈现服务创建帐户需要执行以下步骤：

1. 中转到[混合现实预览页面](https://aka.ms/MixedRealityPrivatePreview)
1. 单击 "创建资源" 按钮
1. 在搜索字段中，键入 "远程呈现" 并按 "enter"。
1. 在结果列表中，单击 "远程呈现" 磁贴
1. 在下一个屏幕中，单击 "创建" 按钮。 此时将打开一个窗体，以创建新的远程呈现帐户：
    1. 将 "资源名称" 设置为帐户的名称
    1. 如果需要，更新 "订阅"
    1. 将 "资源组" 设置为所选的资源组
1. 创建帐户后，请导航到该帐户，并执行以下操作：
    1. 在 "*概述*" 选项卡中，记下 "帐户 ID"
    1. 在 "*设置" > 访问密钥*"选项卡中，记下" 主密钥 "-这是帐户的机密帐户密钥

### <a name="retrieve-the-account-information"></a>检索帐户信息

示例和教程要求提供帐户 ID 和密钥。 例如，在用于 PowerShell 示例脚本的**arrconfig**文件中：

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

请参阅填写*区域*选项的[可用区域列表](../reference/regions.md)。

可以在门户**`arrAccountId`** 中**`arrAccountKey`** 找到和的值，如以下步骤中所述：

* 转到 [Azure 门户](https://www.portal.azure.com)
* 找到 **"远程呈现帐户"** -它应位于 **"最近使用的资源"** 列表中。 你还可以在顶部的搜索栏中搜索它。 在这种情况下，请确保在默认订阅筛选器中选择要使用的订阅（搜索栏旁边的筛选器图标）：

![订阅筛选器](./media/azure-subscription-filter.png)

单击你的帐户将进入此屏幕，其中立即显示**帐户 ID** ：

![Azure 帐户 ID](./media/azure-account-id.png)

对于密钥，请在左侧面板中选择 "**访问密钥**"。 下一页显示主密钥和辅助密钥：

![Azure 访问密钥](./media/azure-account-primary-key.png)

的**`arrAccountKey`** 值可以是主密钥或辅助密钥。

## <a name="link-storage-accounts"></a>链接存储帐户

此段介绍如何将存储帐户链接到远程呈现帐户。 链接存储帐户时，不需要在每次要与帐户中的数据进行交互时（例如，在加载模型时）生成 SAS URI。 相反，你可以直接使用存储帐户名称，如 "[加载模型" 部分](../concepts/models.md#loading-models)中所述。

必须为应使用此备用访问方法的每个存储帐户执行此段落中的步骤。 如果尚未创建存储帐户，可以在[转换模型以呈现快速入门](../quickstarts/convert-model.md#storage-account-creation)中演练相应的步骤。

现在，假设你有一个存储帐户。 导航到门户中的存储帐户，并转到该存储帐户的 "**访问控制（IAM）** " 选项卡：

![存储帐户 IAM](./media/azure-storage-account.png)

 确保你拥有对此存储帐户的所有者权限，以确保你可以添加角色分配。 如果没有访问权限，则将禁用 "**添加角色分配**" 选项。

 需要添加三个不同的角色，如以下步骤中所述。 如果未提供所有三个级别的访问权限，则在尝试访问存储帐户时，将会出现权限问题。

 单击 "添加角色分配" 磁贴中的 "**添加**" 按钮，添加第一个角色：

![存储帐户 IAM](./media/azure-add-role-assignment.png)

* 要分配的第一个角色为 "**所有者**"，如上面的屏幕截图中所示。 
* 从 "**将访问权限分配到**" 下拉列表中选择 "**远程呈现帐户**"。
* 在上一个下拉列表中选择你的订阅和远程呈现帐户。

对于 "**角色**" 下拉列表中的相应选项，重复两次添加新角色：
* **存储帐户参与者**
* **存储 Blob 数据参与者**

在第一步中选择另一个下拉列表。

如果已添加全部三个角色，则 Azure 远程呈现帐户可以使用系统分配的托管服务标识访问你的存储帐户。

## <a name="next-steps"></a>后续步骤

* [身份验证](authentication.md)
* [使用 Azure 前端 Api 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
