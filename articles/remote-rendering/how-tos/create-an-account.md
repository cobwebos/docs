---
title: 创建 Azure 远程呈现帐户
description: 描述为 Azure 远程呈现创建帐户的步骤
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681293"
---
# <a name="create-an-azure-remote-rendering-account"></a>创建 Azure 远程呈现帐户

本章将指导您完成为**Azure 远程呈现**服务创建帐户的步骤。 完成任何快速入门或教程必须使用有效的帐户。

## <a name="create-an-account"></a>创建帐户

为 Azure 远程呈现服务创建帐户需要以下步骤：

1. 转到[混合现实预览页面](https://aka.ms/MixedRealityPrivatePreview)
1. 单击"创建资源"按钮
1. 在搜索字段（"搜索市场"）中，键入"远程渲染"并点击"输入"。
1. 在结果列表中，单击"远程渲染"磁贴
1. 在下一个屏幕中，单击"创建"按钮。 将打开一个窗体以创建新的远程呈现帐户：
    1. 将"资源名称"设置为帐户的名称
    1. 如果需要，更新"订阅"
    1. 将"资源组"设置为您选择的资源组
1. 创建帐户后，导航到该帐户，并：
    1. 在 *"概述"* 选项卡中，请注意"帐户 ID"
    1. 在 *"设置>访问密钥*"选项卡中，请注意"主密钥" - 这是帐户的秘密帐户密钥

### <a name="retrieve-the-account-information"></a>检索帐户信息

示例和教程要求您提供帐户 ID 和密钥。 例如，在用于 PowerShell 示例脚本的**arrconfig.json**文件中：

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

请参阅[可用区域列表](../reference/regions.md)以填写*区域*选项。

的值**`arrAccountId`****`arrAccountKey`** 可以在门户中找到，如以下步骤所述：

* 转到[Azure 门户](https://www.portal.azure.com)
* 查找您的 **"远程呈现帐户"** - 它应位于 **"最新资源"** 列表中。 您还可以在顶部的搜索栏中搜索它。 在这种情况下，请确保在"默认订阅"筛选器（搜索栏旁边的筛选器图标）中选择了要使用的订阅：

![订阅筛选器](./media/azure-subscription-filter.png)

单击您的帐户会将您带到此屏幕，该屏幕会立即显示**帐户 ID：**

![Azure 帐户 ID](./media/azure-account-id.png)

对于键，选择左侧面板中的 **"访问键**"。 下一页显示主键和辅助键：

![Azure 访问密钥](./media/azure-account-primary-key.png)

的值**`arrAccountKey`** 可以是主键或辅助键。

## <a name="link-storage-accounts"></a>链接存储帐户

本段说明如何将存储帐户链接到远程呈现帐户。 链接存储帐户时，不必在每次要与帐户中的数据交互时（例如加载模型时）生成 SAS URI。 相反，您可以直接使用存储帐户名称，如[加载模型部分](../concepts/models.md#loading-models)中所述。

必须针对应使用此替代访问方法的每个存储帐户执行此段落中的步骤。 如果尚未创建存储帐户，则可以在[转换模型以呈现快速启动](../quickstarts/convert-model.md#storage-account-creation)时遍历相应的步骤。

现在假定您有一个存储帐户。 导航到门户中的存储帐户，然后转到该存储帐户的访问**控制 （IAM）** 选项卡：

![存储帐户 IAM](./media/azure-storage-account.png)

 确保对此存储帐户具有所有者权限，以确保可以添加角色分配。 如果您没有访问权限，将禁用 **"添加角色分配**"选项。

 您需要添加三个不同的角色，如下步骤中所述。 如果不提供所有三个级别的访问，则在尝试访问存储帐户时将存在权限问题。

 单击"添加角色分配"磁贴中的 **"添加**"按钮以添加第一个角色：

![存储帐户 IAM](./media/azure-add-role-assignment.png)

* 第一个要分配的角色是**所有者**，如上图所示。 
* 从 +**分配访问到**下拉列表中选择**远程呈现帐户**。
* 在上一个下拉列表中选择订阅和远程呈现帐户。

重复添加新角色两次，以便从**角色**下拉列表的下拉进行相应的选择：
* **存储帐户参与者**
* **存储 Blob 数据参与者**

其他下拉列表选择在第一步中。

如果添加了所有三个角色，则 Azure 远程呈现帐户可以使用系统分配的托管服务标识访问存储帐户。

## <a name="next-steps"></a>后续步骤

* [身份验证](authentication.md)
* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [电源外壳脚本示例](../samples/powershell-example-scripts.md)
