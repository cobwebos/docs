---
title: 使用 Azure 门户配置客户托管密钥
description: 本文介绍如何在 Azure 数据资源管理器中配置客户托管密钥加密。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: c014ed2c25711677617d3bf8ff5d2f0f968a3b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301039"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>使用 Azure 门户配置客户管理的密钥

> [!div class="op_single_selector"]
> * [门户](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 模板](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>在 Azure 门户中使用客户管理的密钥进行加密

本文介绍如何使用 Azure 门户启用客户管理密钥加密。 默认情况下，Azure 数据资源管理器加密使用 Microsoft 托管密钥。 将 Azure 数据资源管理器群集配置为使用客户托管密钥，并指定要与群集关联的密钥。

1. 在[Azure 门户](https://portal.azure.com/)中，转到[Azure 数据资源管理器群集](create-cluster-database-portal.md#create-a-cluster)资源。 
1. 在门户的左侧窗格中选择 > **"设置加密**"。 **Settings**
1. 在 **"加密**"窗格中，为**客户管理的密钥**设置选择 **"打开**"。
1. 单击 **"选择键**"。

    ![配置客户管理的密钥](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. 在"**从 Azure 密钥保管库中选择密钥**"窗口中，从下拉列表中选择现有的**密钥保管库**。 如果选择 **"创建新**"[以创建新的密钥保管库](/azure/key-vault/quick-create-portal#create-a-vault)，则将路由到 **"创建密钥保管库"** 屏幕。

1. 选择**键**。
1. 选择**版本**。
1. 单击“选择”。

    ![从 Azure 密钥保管库中选择密钥](media/customer-managed-keys-portal/cmk-key-vault.png)

1. 在"**加密**"窗格中，现在包含密钥，选择 **"保存**"。 当 CMK 创建成功时，您将在**通知**中看到一条成功消息。

    ![保存客户管理的密钥](media/customer-managed-keys-portal/cmk-encryption-setting.png)

通过为 Azure 数据资源管理器群集启用客户管理的密钥，如果群集不存在，则将创建系统分配的标识。 此外，您将向所选密钥保管库上的 Azure 数据资源管理器群集提供所需的视图权限，并获取密钥保管库属性。 

> [!NOTE]
> 选择 **"关闭**"以在创建客户管理的密钥后将其删除。

## <a name="next-steps"></a>后续步骤

* [在 Azure 中保护 Azure 数据资源管理器群集](security.md)
* 通过启用静态加密[保护 Azure 数据资源管理器中的群集 - Azure 门户](manage-cluster-security.md)。
* [使用 Azure 资源管理器模板配置客户管理的密钥](customer-managed-keys-resource-manager.md)
* [使用 C# 配置客户管理的密钥](customer-managed-keys-csharp.md)



