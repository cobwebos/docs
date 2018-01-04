---
title: "使用 Azure CLI 在开发测试实验室中创建和管理虚拟机 | Microsoft Docs"
description: "了解如何通过 Azure 开发测试实验室，使用 Azure CLI 2.0 创建和管理虚拟机"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: v-craic
ms.openlocfilehash: e73ddeba56c779d9fb1be77a50cbae5111de03c4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>使用 Azure CLI 通过开发测试实验室创建和管理虚拟机
本快速入门将指导用户在实验室中进行创建、启动、连接、更新和清理开发计算机等操作。 

开始之前：

* 如果尚未创建实验室，[此处](devtest-lab-create-lab.md)提供相关说明。

* [安装 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 若要开始，请运行 az login，创建与 Azure 的连接。 

## <a name="create-and-verify-the-virtual-machine"></a>创建并验证虚拟机 
使用 ssh 身份验证从 Marketplace 映像创建 VM。
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> 将实验室的资源组名称置于 --resource-group 参数中。
>

如果想要使用公式创建 VM，请使用 [az lab vm create](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create) 中的 --formula 参数。


验证 VM 是否可用。
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>启动并连接到虚拟机
启动 VM。
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> 将实验室的资源组名称置于 --resource-group 参数中。
>

连接到 VM：[SSH](../virtual-machines/linux/mac-create-ssh-keys.md) 或[远程桌面](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>更新虚拟机
将项目应用到 VM。
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

在实验室中列出可用项目。
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>停止和删除虚拟机    
停止 VM。
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

删除 VM。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]