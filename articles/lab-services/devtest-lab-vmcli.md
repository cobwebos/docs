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
ms.openlocfilehash: 11ac4e10cbd116ed204a8a11274408f5a5a9b4d9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183136"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>使用 Azure CLI 通过开发测试实验室创建和管理虚拟机
本快速入门介绍如何在实验室中创建、启动、连接、更新和清理开发计算机。 

开始之前：

* 如果尚未创建实验室，[此处](devtest-lab-create-lab.md)提供相关说明。

* [安装 Azure CLI](/cli/azure/install-azure-cli)。 若要开始，请运行 az login，创建与 Azure 的连接。 

## <a name="create-and-verify-the-virtual-machine"></a>创建并验证虚拟机 
执行开发测试 Labs 相关命令之前, 请使用`az account set`命令设置相应的 Azure 上下文:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

用于创建虚拟机的命令为: `az lab vm create`。 实验室、实验室名称和虚拟机名称的资源组都是必需的。 其余参数会根据虚拟机的类型发生变化。

以下命令从 Azure 市场位置创建基于 Windows 的映像。 映像的名称与使用 Azure 门户创建虚拟机时看到的名称相同。 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

以下命令基于实验室中提供的自定义映像创建虚拟机:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**图像类型**参数已从**库**更改为**自定义**。 如果要在 Azure 门户中创建虚拟机, 则映像的名称与你看到的内容相匹配。

以下命令使用 ssh 身份验证从 marketplace 映像创建 VM:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

还可以通过将**图像类型**参数设置为**公式**来创建基于公式的虚拟机。 如果需要为虚拟机选择特定虚拟网络, 请使用**vnet 名称**和**子网**参数。 有关详细信息, 请参阅[az lab vm create](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="verify-that-the-vm-is-available"></a>验证 VM 是否可用。
`az lab vm show`使用命令验证 VM 是否可用, 然后再开始连接。 

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
下面的示例命令将启动 VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

连接到 VM:[SSH](../virtual-machines/linux/mac-create-ssh-keys.md)或[远程桌面](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>更新虚拟机
下面的示例命令将项目应用到 VM:

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
下面的示例命令停止了 VM。

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

删除 VM。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>后续步骤
请参阅以下内容:[Azure 开发测试实验室 Azure CLI 文档](/cli/azure/lab?view=azure-cli-latest)。 
