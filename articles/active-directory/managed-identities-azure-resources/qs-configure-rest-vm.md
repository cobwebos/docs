---
title: 如何在 Azure VM 上使用 REST 配置系统分配的托管标识和用户分配的托管标识
description: 分步说明如何在 Azure VM 上通过使用 CURL 进行 REST API 调用来配置系统分配的托管标识和用户分配的托管标识。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: priyamo
ms.openlocfilehash: 7b44d58f14da06bdcbd7e9d9ada4976049969c81
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158972"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>使用 REST API 调用在 Azure VM 上配置 Azure 资源的托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管系统标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何在 Azure VM 上通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用来执行以下 Azure 资源的托管标识操作：

- 在 Azure VM 上启用和禁用系统分配的托管标识
- 在 Azure VM 上添加和删除用户分配托管标识

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 如果使用 Windows，请安装[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)或使用 Azure 门户中的 [Azure Cloud Shell](../../cloud-shell/overview.md)。
- 如果使用[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)或 [Linux 分发版 OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)，请[安装 Azure CLI 本地控制台](/cli/azure/install-azure-cli)。
- 如果使用 Azure CLI 本地控制台，请使用 `az login` 和与要用于管理系统或用户分配的托管标识的 Azure 订阅关联的帐户登录。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

本节将介绍如何在 Azure VM 上通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用来启用和禁用系统分配的托管标识。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中启用系统分配托管标识

若要创建启用了系统分配的托管标识的 Azure VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 运行 [az group create](/cli/azure/group/#az-group-create)，创建用于容纳和部署 VM 及其相关资源的[资源组](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，可以跳过这一步：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. 为 VM 创建[网络接口](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的 VM。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用，创建 VM。 下面的示例创建名为 *myVM* 且已启用系统分配的托管标识（请求正文中用值 `"identity":{"type":"SystemAssigned"}` 进行标识）的 VM。 请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **请求标头**
   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 
   
   **请求正文**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用系统分配标识

若要在最初未预配系统分配的托管标识的 VM 上启用该托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的 VM。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用以下 CURL 命令对 Azure 资源管理器 REST 终结点进行调用，为名为“myVM”的 VM 启用系统分配的托管标识（在请求正文中用值 `{"identity":{"type":"SystemAssigned"}` 进行标识）。  请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。
   
   > [!IMPORTANT]
   > 若要确保不删除用户分配给 VM 的任何现有托管标识，需要使用以下 CURL 命令列出用户分配的托管标识：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如果具有用户分配给 VM 的任何托管标识（响应中用值 `identity` 进行标识），请跳过步骤 3，该步骤介绍了如何在 VM 上启用系统分配的托管标识的同时保留用户分配的托管标识。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **请求标头**
   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 
   
   **请求正文**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. 要在具有现有用户分配的托管标识的 VM 上启用系统分配的托管标识，需要将 `SystemAssigned` 添加到 `type` 值。  
   
   例如，如果 VM 具有用户分配给它的托管标识 `ID1` 和 `ID2` 并且你希望将系统分配的托管标识添加到该 VM，请使用以下 CURL 调用。 将 `<ACCESS TOKEN>` 和 `<SUBSCRIPTION ID>` 替换为适合环境的值。

   API 版本 `2018-06-01` 以字典格式将用户分配的托管标识存储在 `userAssignedIdentities` 值中，而 API 版本 `2017-12-01` 则以数组格式将托管标识存储在 `identityIds` 值中。
   
   **API 版本 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 
   **请求正文**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配的托管标识

若要在 VM 上禁用系统分配的托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的 VM。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用以禁用系统分配的托管标识，更新 VM。  下面的示例从名为 myVM 的 VM 禁用系统分配的托管标识（请求正文中用值 `{"identity":{"type":"None"}}` 进行标识）。  请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。

   > [!IMPORTANT]
   > 若要确保不删除用户分配给 VM 的任何现有托管标识，需要使用以下 CURL 命令列出用户分配的托管标识：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如果具有用户分配给 VM 的任何托管标识（响应中用值 `identity` 进行标识），请跳过步骤 3，该步骤介绍了如何在 VM 上禁用系统分配的托管标识的同时保留用户分配的托管标识。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   如果使用的是 API 版本 2018-06-01，若要从具有用户分配的托管标识的虚拟机中删除系统分配的托管标识，请从 `{"identity":{"type:" "}}` 值中删除 `SystemAssigned`，同时保留 `UserAssigned` 值和 `userAssignedIdentities` 字典值。 如果使用的是 **API 版本 2017-12-01** 或早期版本，请保留 `identityIds` 数组。

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

本节将介绍如何在 Azure VM 上通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用来添加和删除用户分配的托管标识。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中分配用户分配托管标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的 VM。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 为 VM 创建[网络接口](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的 VM。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 按照此处的说明创建用户分配的托管标识：[创建用户分配的托管标识](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

5. 通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用，创建 VM。 下面的示例在资源组“myResourceGroup”中创建名为“myVM”的 VM，该 VM 具有用户分配的托管标识 `ID1`（请求正文中用值 `"identity":{"type":"UserAssigned"}` 进行标识）。 请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。
 
   **API 版本 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API 版本 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>向现有 Azure VM 分配用户分配托管标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的 VM。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  按照此处的说明创建用户分配的托管标识：[创建用户分配的托管标识](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

3. 若要确保不删除用户或系统分配给 VM 的现有托管标识，需要使用以下 CURL 命令列出分配给 VM 的标识。 如果具有分配给虚拟机规模集的托管标识，则这些标识会在 `identity` 值下列出。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **请求标头**
   |请求标头  |说明  |
   |---------|---------|
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。

    如果具有用户或系统分配给 VM 的任何托管标识（响应中用值 `identity` 进行标识），请跳过步骤 5，该步骤展示了如何在 VM 上保留系统分配的托管标识，同时添加用户分配的托管标识。

4. 如果没有用户分配给 VM 的任何托管标识，请使用以下 CURL 命令对 Azure 资源管理器 REST 终结点进行调用，以将第一个用户分配的托管标识分配给 VM。

   下面的示例将用户分配的托管标识 `ID1` 分配给资源组 *myResourceGroup* 中名为 *myVM* 的 VM。  请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。

   **API 版本 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        |
 
   **请求正文**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. 如果具有用户或系统分配给 VM 的现有托管标识，则：
   
   **API 版本 2018-06-01**

   将用户分配的托管标识添加到 `userAssignedIdentities` 字典值。
    
   例如，如果你具有当前分配给虚拟机的系统分配的托管标识和用户分配的托管标识 `ID1` 并希望将用户分配的托管标识 `ID2` 添加到该虚拟机，则：

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   在 `identityIds` 数组值中保留要保持的用户分配的托管标识，同时添加新的用户分配的托管标识。

   例如，如果你具有当前分配给虚拟机的系统分配的托管标识和用户分配的托管标识 `ID1` 并希望将用户分配的托管标识 `ID2` 添加到该虚拟机，则： 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中删除用户分配的托管标识

若要从 VM 中删除用户分配的标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的 VM。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 若要确保不删除任何现有用户分配的托管标识（希望保留在 VM 上）或不删除系统分配的托管标识，需要使用以下 CURL 命令列出这些托管标识： 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **请求标头**
   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。
 
   如果具有分配给 VM 的托管标识，则这些标识会在 `identity` 值下列出。

   例如，如果你有分配给 VM 的用户分配的托管标识 `ID1` 和 `ID2`，并且仅希望保持分配 `ID1` 并保留系统分配的标识：
   
   **API 版本 2018-06-01**

   将 `null` 添加到要删除的用户分配的托管标识：

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   在 `identityIds` 数组中仅保留要保持的用户分配的托管标识：

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **请求标头**

   |请求标头  |说明  |
   |---------|---------|
   |*Content-Type*     | 必需。 设置为 `application/json`。        |
   |*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        | 

   **请求正文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

如果 VM 同时具有系统分配的托管标识和用户分配的托管标识，则可通过使用以下命令切换为仅使用系统分配的托管标识，删除所有用户分配的托管标识：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**请求标头**

|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。 | 

**请求正文**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
如果 VM 只具有用户分配的托管标识并希望删除所有这些托管标识，请使用以下命令：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**请求标头**

|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。| 

**请求正文**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>后续步骤

有关如何使用 REST 创建、列出或删除用户分配的托管标识，请参阅：

- [使用 REST API 调用创建、列出或删除用户分配的托管标识](how-to-manage-ua-identity-rest.md)
