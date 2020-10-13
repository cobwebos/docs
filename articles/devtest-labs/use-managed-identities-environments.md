---
title: 使用 Azure 托管标识在开发测试实验室中创建环境 |Microsoft Docs
description: 了解如何使用 Azure 中的托管标识在 Azure 开发测试实验室中的实验室部署环境。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5bac4210afee6db1c7617dac1cd6d2ff9149439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718964"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 托管标识在实验室中部署环境 

作为实验室所有者，你可以使用托管标识在实验室中部署环境。 此功能在以下情况下非常有用：环境包含或引用 Azure 资源，如 key vault、共享映像库以及环境资源组外部的网络。 它允许创建不限于该环境的资源组的沙盒环境。

> [!NOTE]
> 目前，每个实验室支持单一用户分配的标识。 

## <a name="prerequisites"></a>必备条件

- [使用 Azure 门户创建、列出、删除用户分配的托管标识或为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 
    
    确保你的托管标识是与实验室在同一区域和订阅中创建的。 托管标识不需要位于同一资源组中。

## <a name="use-azure-portal"></a>使用 Azure 门户

在本部分中，你将作为实验室所有者，使用 Azure 门户向实验室添加用户管理的标识。 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索 **开发测试实验室**。
1. 从实验室列表中，选择所需的实验室。
1. 选择 "**配置和策略**  ->  **标识" (预览 ") **。 
1. 若要添加用户分配的标识，请选择 " **用户分配** " 选项卡。
1. 按 " **添加** "。
1. 从下拉选择已创建和/或有权访问的现有用户。
 
    ![添加用户托管标识](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. 按页面顶部的 " **保存** "。

    保存后，实验室将使用此标识，同时部署所有实验室环境。 还可以通过从列表中选择标识来访问 Azure 中的标识资源。 

部署环境时，实验室所有者无需执行任何特殊操作，只要添加到实验室的标识有权访问环境需要访问的外部资源。 

若要更改分配给实验室的用户托管标识，请首先删除附加到实验室的标识，然后将另一个标识添加到实验室。 若要删除附加到实验室的标识，请选择 " **..." (省略号) **，然后单击 " **删除**"。 

## <a name="use-api"></a>使用 API

1. 创建标识后，请记下此标识的资源 ID。 它应类似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. 执行 PUT Https 方法，以将新 `ServiceRunner` 资源添加到实验室，类似于下面的示例。 服务运行程序资源是一个代理资源，用于管理和控制开发测试实验室中的托管标识。 服务运行程序名称可以是任何有效的名称，但我们建议你使用托管标识资源的名称。 
 
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
