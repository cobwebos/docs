---
title: 在 Azure 开发人员测试实验室中的实验室 VM 上启用托管标识
description: 本文介绍实验室所有者如何在实验室虚拟机上启用用户分配的托管标识。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692662"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure 开发人员测试实验室中的实验室虚拟机上启用用户分配的托管标识
作为实验室所有者，您可以在 Azure DevTest Labs 中的实验室虚拟机 （VM） 上启用用户分配的托管标识。

托管标识可用于对支持 Azure 活动目录 （AD） 身份验证（包括密钥保管库）的任何服务进行身份验证，而无需传递代码中的任何凭据。 有关托管标识的详细信息，请参阅 Azure[资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md)

使用此功能，实验室用户可以在实验室的上下文中共享 Azure 资源，如 Azure SQL 数据库。 对资源的身份验证由标识本身处理。 配置后，将使用此标识启用每个现有/新创建的实验室 VM。 实验室用户可以在登录到其计算机后访问资源。

> [!NOTE]
> 您可以添加多个分配给用户分配的托管标识，以在实验室 VM 上启用。

## <a name="use-azure-portal"></a>使用 Azure 门户
要为实验室 VM 添加分配给的托管标识的用户，请按照以下步骤操作：

1. [在订阅中创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. 导航到实验室的 **"配置和策略"** 页面。
2. 选择左侧菜单上的**标识（预览）。**
3. 选择 **"虚拟机**"选项卡。
4. 选择 **"添加"** 可从预填充的下拉列表中选择现有标识。 

    ![添加标识按钮](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. 从下落列表中选择现有**用户托管标识**，然后选择 **"确定**"。 

    ![添加标识](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>使用 API

1.  创建标识后，请注意标识的资源 ID。 它应类似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. 运行 PUT HTTPS 方法以向实验室添加新**的 ServiceRunner**资源，如以下示例所示。 

    服务运行者资源是管理和控制 DevTest 实验室中的托管标识的代理资源。 服务运行者名称可以是任何有效名称，但我们建议您使用托管标识资源的名称。

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>后续步骤
要了解有关托管标识的更多内容，请参阅[Azure 资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md)







