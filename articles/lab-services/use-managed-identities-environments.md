---
title: 使用 Azure 托管标识在开发测试实验室中创建环境 |Microsoft Docs
description: 了解如何使用 Azure 中的托管标识在 Azure 开发测试实验室中的实验室部署环境。
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
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931156"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 托管标识在实验室中部署环境 
作为实验室所有者，你可以使用托管标识在实验室中部署环境。 此功能在以下情况下非常有用：环境包含或引用 Azure 资源，如 key vault、共享映像库以及环境资源组外部的网络。 它允许创建不限于该环境的资源组的沙盒环境。

> [!NOTE]
> 目前，每个实验室支持单一用户分配的标识。 

## <a name="prerequisites"></a>先决条件
- [使用 Azure 门户创建、列出、删除用户分配的托管标识或为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

## <a name="use-azure-portal"></a>使用 Azure 门户
在本部分中，你将作为实验室所有者，使用 Azure 门户向实验室添加用户管理的标识。 

1. 在 "实验室" 页上，选择 "**配置和策略**"。 
1. 在 "**设置**" 部分中选择 "**标识**"。
1. 若要添加用户分配的标识，请在工具栏上选择 "**添加**"。 
1. 从预填充的下拉列表中选择一个**标识**。
1. 选择“确定”。

    ![添加用户托管标识](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. 你将在列表中看到添加的用户管理的标识。 

    ![列表中用户管理的标识](./media/use-managed-identities-environments/identity-in-list.png)

保存后，实验室将使用此标识，同时部署所有实验室环境。 还可以通过从列表中选择标识来访问 Azure 中的标识资源。 

部署环境时，实验室所有者无需执行任何特殊操作，只要添加到实验室的标识有权访问环境需要访问的外部资源。 

若要更改分配给实验室的用户托管标识，请首先删除附加到实验室的标识，然后将另一个标识添加到实验室。 若要删除附加到实验室的标识，请选择 " **..."（省略号）** ，然后单击 "**删除**"。 

![列表中用户管理的标识](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>使用 API

1. 创建标识后，请记下此标识的资源 ID。 它应类似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`。
1. 执行 PUT Https 方法，以将新的 `ServiceRunner` 资源添加到实验室，类似于下面的示例。 服务运行程序资源是一个代理资源，用于管理和控制开发测试实验室中的托管标识。 服务运行程序名称可以是任何有效的名称，但我们建议你使用托管标识资源的名称。 
 
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
 
将用户分配的标识添加到实验室后，Azure 开发测试实验室服务在部署 Azure 资源管理器环境时将使用它。 例如，如果你需要资源管理器模板来访问外部共享的映像库图像，请确保添加到实验室的标识具有共享映像库资源的最小所需权限。 
