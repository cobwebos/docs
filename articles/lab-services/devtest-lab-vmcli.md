---
title: 使用 Azure CLI 在开发测试实验室中创建和管理虚拟机 | Microsoft Docs
description: 了解如何通过 Azure 开发测试实验室，使用 Azure CLI 创建和管理虚拟机
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795930"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>使用 Azure CLI 通过开发测试实验室创建和管理虚拟机
本快速入门将指导您完成创建、 启动、 连接、 更新和清理在实验室中的开发计算机。 

开始之前：

* 如果尚未创建实验室，[此处](devtest-lab-create-lab.md)提供相关说明。

* [安装 Azure CLI](/cli/azure/install-azure-cli)。 若要开始，请运行 az login，创建与 Azure 的连接。 

## <a name="create-and-verify-the-virtual-machine"></a>创建并验证虚拟机 
在执行开发测试实验室相关的命令之前，通过设置适当的 Azure 上下文`az account set`命令：

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

若要创建的虚拟机的命令是： `az lab vm create`。 实验室、 实验室名称和虚拟机名称的资源组都是必需的。 自变量的其余部分更改，具体取决于虚拟机的类型。

以下命令创建基于 Windows 的映像从 Azure Market Place。 正如您将看到创建虚拟机使用 Azure 门户时，映像的名称都是相同的。 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

以下命令创建基于在实验室中可用的自定义映像的虚拟机：

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**图像类型**参数已更改，不再**库**到**自定义**。 映像的名称匹配，如果要创建虚拟机在 Azure 门户中看到的内容。

以下命令创建一个 VM 从 marketplace 映像使用 ssh 身份验证：

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

此外可以创建虚拟机通过设置基于公式**图像类型**参数**公式**。 如果你需要选择用于你的虚拟机的特定虚拟网络，使用**vnet 名称**并**子网**参数。 有关详细信息，请参阅[az lab vm 创建](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="verify-that-the-vm-is-available"></a>验证 VM 是否可用。
使用`az lab vm show`命令来验证之前启动并连接到该 VM 是否可用。 

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
下面的示例命令启动 VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

连接到 VM:[SSH](../virtual-machines/linux/mac-create-ssh-keys.md)或[远程桌面](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>更新虚拟机
以下示例命令适用于 VM 的项目：

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
以下示例命令停止 VM。

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

删除 VM。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>后续步骤
请参阅以下内容：[有关 Azure 开发测试实验室的 azure CLI 文档](/cli/azure/lab?view=azure-cli-latest)。 