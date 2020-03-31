---
title: 使用 Azure 托管标识在开发人员测试实验室中创建环境 |微软文档
description: 了解如何在 Azure 开发人员测试实验室中使用 Azure 中的托管标识在实验室中部署环境。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931156"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 托管标识在实验室中部署环境 
作为实验室所有者，可以使用托管标识在实验室中部署环境。 此功能在环境包含或具有对 Azure 资源的引用（如密钥保管库、共享映像库和环境资源组外部的网络）的方案中非常有用。 它支持创建不局限于该环境的资源组的沙盒环境。

> [!NOTE]
> 目前，每个实验室都支持单个用户分配的标识。 

## <a name="prerequisites"></a>先决条件
- [使用 Azure 门户创建、列出、删除或将角色分配给用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

## <a name="use-azure-portal"></a>使用 Azure 门户
在本节中，作为实验室所有者，您可以使用 Azure 门户向实验室添加用户托管标识。 

1. 在实验室页面上，选择 **"配置"和"策略**"。 
1. 在 **"设置"** 部分中选择 **"标识**"。
1. 要添加用户分配的标识，请在工具栏上选择 **"添加**"。 
1. 从预填充的下拉列表中选择**标识**。
1. 选择“确定”。

    ![添加用户托管标识](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. 在列表中可以看到添加的用户托管标识。 

    ![列表中的用户托管标识](./media/use-managed-identities-environments/identity-in-list.png)

保存后，实验室将在部署所有实验室环境时使用此标识。 还可以通过从列表中选择标识来访问 Azure 中的标识资源。 

只要添加到实验室的标识具有环境需要访问的外部资源的权限，实验室所有者在部署环境时不需要执行任何特殊操作。 

要更改分配给实验室的用户托管标识，请先删除附加到实验室的标识，然后向实验室添加另一个标识。 要删除附加到实验室的标识，请选择 **...（省略号），** 然后单击 **"删除**"。 

![列表中的用户托管标识](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>使用 API

1. 创建标识后，请注意此标识的资源 ID。 它应类似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. 执行 PUT Https 方法向实验室`ServiceRunner`添加新资源，类似于以下示例。 服务运行者资源是管理和控制 DevTest 实验室中的托管标识的代理资源。 服务运行者名称可以是任何有效名称，但我们建议您使用托管标识资源的名称。 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    下面是一个示例： 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
将用户分配的标识添加到实验室后，Azure DevTest Labs 服务将在部署 Azure 资源管理器环境时使用它。 例如，如果需要资源管理器模板来访问外部共享图像库图像，请确保添加到实验室的标识具有共享映像库资源所需的最低权限。 
