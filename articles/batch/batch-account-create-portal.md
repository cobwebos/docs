---
title: "在 Azure 门户中创建批处理帐户 | Microsoft Docs"
description: "了解如何在 Azure 门户中创建 Azure Batch 帐户，以便在云中运行大规模并行工作负荷"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebda2f11f93b04a5592d18f8e15c8fc3b560aac3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>使用 Azure 门户创建 Batch 帐户

> [!div class="op_single_selector"]
> * [Azure 门户](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

了解如何在 [Azure 门户][azure_portal]中创建 Azure Batch 帐户，以及如何选择适合计算方案的帐户属性。 了解在何处查找重要的帐户属性，例如访问密钥和帐户 URL。

有关批处理帐户和方案的背景，请参阅[功能概述](batch-api-basics.md)。



## <a name="create-a-batch-account"></a>创建批处理帐户

> [!NOTE]
> 创建 Batch 帐户时，通常应选择默认的“Batch 服务”模式。使用此模式时，池在 Azure 托管的订阅中以幕后方式分配。 在备用的“用户订阅”模式（对于大多数方案不再推荐使用）下，会在创建池时直接在订阅中创建 Batch VM 和其他资源。 若要在用户订阅模式下创建 Batch 帐户，还需将订阅注册到 Azure Batch 中，并将该帐户与 Azure Key Vault 相关联。

1. 登录到 [Azure 门户][azure_portal]。
2. 单击“新建”，搜索 Marketplace 中是否有“Batch 服务”。

    ![Marketplace 中的批处理][marketplace_portal]
3. 选择“Batch 服务”，单击“创建”，然后输入“新建 Batch 帐户”设置。 查看以下详细信息。

    ![创建批处理帐户][account_portal]

    a. **帐户名称**：所选名称必须在创建帐户的 Azure 区域中唯一（参见下面的“位置”）。 帐户名只能包含小写字符或数字，且长度必须为 3-24 个字符。

    b. **订阅**：要在其中创建批处理帐户的订阅。 如果只有一个订阅，则默认选择此项。

    c. **池分配模式**：如果显示此设置，请接受默认值“Batch 服务”。

    c. **资源组**：为新批处理帐户选择现有的资源组，或选择创建一个新组。

    d.单击“下一步”。 **位置**：要在其中创建批处理帐户的 Azure 区域。 只有订阅和资源组支持的区域显示为选项。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 **存储帐户**（可选）：与批处理帐户关联的通用 Azure 存储帐户。 建议大多数批处理帐户采用此设置。 如需详细信息，请参阅本文后面的[关联的 Azure 存储帐户](#linked-azure-storage-account)。

4. 单击“创建”  以创建帐户。



## <a name="view-batch-account-properties"></a>查看 Batch 帐户属性
创建帐户后，单击该帐户即可访问其设置和属性。 可以使用左侧菜单访问所有帐户设置和属性。

![Azure 门户中的 Batch 帐户页][account_blade]

* **Batch 帐户 URL**：通过 [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) 开发应用程序时，需要帐户 URL 才能访问 Batch 资源。 Batch 帐户 URL 采用以下格式：

    `https://<account_name>.<region>.batch.azure.com`

![门户中的 Batch 帐户 URL][account_url]

* **访问密钥**：从应用程序访问批处理帐户时，若要进行身份验证，可使用帐户访问密钥。 （Batch 还支持 Azure Active Directory 身份验证。）

    若要查看或重新生成访问密钥，请选择“密钥”。

    ![Azure 门户中的 Batch 帐户密钥][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>链接的 Azure 存储帐户

可以将通用 Azure 存储帐户关联到 Batch 帐户，这适用于很多情况。 与[批处理文件约定 .NET](batch-task-output.md) 库一样，批处理的[应用程序包](batch-application-packages.md)功能使用 Azure Blob 存储。 这些可选功能可用于部署批处理任务运行的应用程序，以及保存它们生成的数据。

我们建议创建批处理帐户专用的新存储帐户。 Azure Batch 目前仅支持通用存储帐户类型。 此帐户类型在[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)的步骤 5：[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)中介绍。

![创建通用存储帐户][storage_account]

> [!NOTE]
> 重新生成关联的存储帐户的访问密钥时请小心。 只重新生成一个存储帐户密钥，并单击“链接的存储帐户”页上的“同步密钥”。 等待五分钟，让密钥传播到池中的计算节点，重新生成并同步其他密钥（如有必要）。 如果同时重新生成这两个密钥，计算节点将无法同步任何一个密钥，并且无法访问存储帐户。
>
>

![重新生成存储帐户密钥][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>用户订阅模式的其他配置

如果选择在用户订阅模式下创建 Batch 帐户，请在创建帐户前执行以下附加步骤。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>允许 Azure Batch 访问订阅（一次性操作）
在用户订阅模式下创建第一个 Batch 帐户时，需将订阅注册到 Batch 中。 （如果已执行过此操作，请跳至下一部分。）

1. 登录到 [Azure 门户][azure_portal]。

2. 单击“更多服务” > “订阅”，并单击要用于批处理帐户的订阅。

3. 在“订阅”页中，单击“访问控制(IAM)” > “添加”。

    ![订阅访问控制][subscription_access]

4. 在“添加权限”页上，选择“参与者”角色，然后搜索 Batch API。 搜索每一条字符串，直到找到此 API：
    1. MicrosoftAzureBatch。
    2. Microsoft Azure Batch。 较新的 Azure AD 租户可能使用此名称。
    3. ddbf3205-c6bd-46ae-8127-60eb93363864 是此 Batch API 的 ID。 

5. 找到此 Batch API 后，将其选中并单击“保存”。

    ![添加批处理权限][add_permission]

### <a name="create-a-key-vault"></a>创建密钥保管库
在“用户订阅”模式下，需要的 Azure 密钥保管库与要创建的批处理帐户属于同一资源组。 请确保资源组所在的区域是[提供](https://azure.microsoft.com/regions/services/)批处理的区域，也是订阅所支持的区域。

1. 在 [Azure 门户][azure_portal]中，单击“新建” > “安全性 + 标识” > “密钥保管库”。

2. 在“创建密钥保管库”页中，输入密钥保管库的名称，并在区域中创建需要用于 Batch 帐户的资源组。 让其余设置保留默认值，并单击“创建”。




## <a name="batch-service-quotas-and-limits"></a>Batch 服务配额和限制
与 Azure 订阅和其他 Azure 服务一样，Batch 帐户也适用特定[配额和限制](batch-quota-limit.md)。 Batch 帐户的当前配额显示在“配额”中。

![Azure 门户中的 Batch 帐户配额][quotas]



此外，其中许多配额可以通过在 Azure 门户中提交免费产品支持请求来增加。 有关请求增加配额的详细信息，请参阅 [Azure Batch 服务的配额和限制](batch-quota-limit.md) 。

## <a name="other-batch-account-management-options"></a>其他 Batch 帐户管理选项
除了使用 Azure 门户外，还可使用以下各项创建和管理 Batch 帐户：

* [批处理 PowerShell cmdlet](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>后续步骤
* 请参阅[批处理功能概述](batch-api-basics.md)，详细了解处理服务的概念和功能。 本文讨论主要 Batch 资源（例如池、计算节点、作业和任务），并提供适用于大规模计算工作负荷的服务功能概述。
* 了解使用[批处理 .NET 客户端库](batch-dotnet-get-started.md)或 [Python](batch-python-tutorial.md) 开发支持批处理的应用程序的基本概念。 这些简介文章介绍了使用批处理服务在多个计算节点上执行工作负荷的可行应用程序，并说明了如何使用 Azure 存储进行工作负荷文件暂存和检索。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "重新生成存储帐户密钥"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

