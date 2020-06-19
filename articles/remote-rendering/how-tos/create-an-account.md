---
title: 创建“Azure 远程渲染”帐户
description: 介绍创建 Azure 远程渲染帐户的步骤
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 58757dba9a8956d97c19269c2ac913d801f73746
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844499"
---
# <a name="create-an-azure-remote-rendering-account"></a>创建“Azure 远程渲染”帐户

本章将指导你完成 Azure 远程渲染服务帐户的创建。 要完成任何快速入门或教程，必须使用一个有效帐户。

## <a name="create-an-account"></a>创建帐户

创建 Azure 远程渲染服务帐户需要执行以下步骤：

1. 访问[混合现实预览页面](https://aka.ms/MixedRealityPrivatePreview)
1. 单击“创建资源”按钮
1. 在搜索字段（“搜索市场”）中，键入“远程渲染”，然后单击 enter。
1. 在结果列表中，单击“远程渲染”磁贴
1. 在下一个屏幕中，单击“创建”按钮。 此时将打开一个窗体，以新建远程渲染帐户：
    1. 将“资源名称”设置为帐户的名称
    1. 如有必要，更新“订阅”
    1. 将“资源组”设置为所选的资源组
1. 创建帐户后，导航到该帐户，然后执行以下操作：
    1. 在“概述”选项卡中，记下“帐户 ID”
    1. 在“设置”>“访问密钥”选项卡中，记下“主密钥”，它是帐户的机密帐户密钥

### <a name="retrieve-the-account-information"></a>检索帐户信息

示例和教程要求提供帐户 ID 和密钥。 例如，在 PowerShell 示例脚本所使用的 arrconfig.json 文件中：

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

若要填写“区域”选项，请参阅[可用区域列表](../reference/regions.md)。

可以在门户中找到 `arrAccountId` 和 `arrAccountKey` 的值，如以下步骤所述 ：

* 转到 [Azure 门户](https://www.portal.azure.com)
* 查找你的“远程渲染帐户”，它应位于“最近的资源”列表中 。 你还可以在顶部的搜索栏中搜索它。 在这种情况下，请确保在默认订阅筛选器（搜索栏旁边的筛选器图标）中选中你要使用的订阅：

![订阅筛选器](./media/azure-subscription-filter.png)

单击帐户将显示此屏幕，其中立即显示帐户 ID：

![Azure 帐户 ID](./media/azure-account-id.png)

对于密钥，请在左侧面板中选择“访问密钥”。 下一页显示主密钥和辅助密钥：

![Azure 访问密钥](./media/azure-account-primary-key.png)

`arrAccountKey` 的值可以是主密钥，也可以是辅助密钥。

## <a name="link-storage-accounts"></a>链接存储帐户

本段落介绍如何将存储帐户链接到远程渲染帐户。 链接存储帐户时，无需在每次要与帐户中的数据进行交互时（例如加载模型时）生成 SAS URI。 你可以直接改用存储帐户名称，如[加载模型部分](../concepts/models.md#loading-models)中所述。

必须为应使用此替代访问方法的每个存储帐户执行本段落中的步骤。 如果尚未创建存储帐户，可以完成[“转换模型以进行渲染”快速入门](../quickstarts/convert-model.md#storage-account-creation)中的相应步骤。

现在，假设你有一个存储帐户。 导航到门户中的存储帐户，然后转到该存储帐户的访问控制 (IAM) 选项卡：

![存储帐户 IAM](./media/azure-storage-account.png)

 确保你拥有对此存储帐户的所有者权限，以确保可以添加角色分配。 如果你没有访问权限，则将禁用“添加角色分配”选项。

 你需要添加三个不同的角色，如后续步骤中所述。 如果你未提供所有三个级别的访问权限，则你尝试访问存储帐户时将会出现权限问题。

 单击“添加角色分配”磁贴中的“添加”按钮，添加第一个角色：

![存储帐户 IAM](./media/azure-add-role-assignment.png)

* 要分配的第一个角色是“所有者”，如上面的屏幕截图中所示。
* 在“访问权限分配对象”下拉列表中，选择“远程渲染帐户” 。
* 在上一个下拉列表中选择你的订阅和远程渲染帐户。

> [!WARNING]
> 如果未列出远程渲染帐户，请参阅此[疑难解答部分](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account)。

对于“角色”下拉列表中的相应选择，请重复添加新角色两次：
* **存储帐户参与者**
* **存储 Blob 数据参与者**

与第一步一样，选择其他下拉列表。

如果你已添加全部三个角色，则 Azure 远程渲染帐户可以使用系统分配的托管服务标识访问你的存储帐户。

## <a name="next-steps"></a>后续步骤

* [身份验证](authentication.md)
* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
