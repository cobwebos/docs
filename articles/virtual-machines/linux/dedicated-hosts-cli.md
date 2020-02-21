---
title: 使用 CLI 部署 Azure 专用主机
description: 使用 Azure CLI 将 Vm 部署到专用主机。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ab9d7128748e99b75b7e1a7187a7958e18300759
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483476"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>使用 Azure CLI 将 Vm 部署到专用主机
 

本文介绍如何创建 Azure[专用主机](dedicated-hosts.md)来托管虚拟机（vm）。 

请确保已安装 Azure CLI 版本2.0.70 或更高版本，并使用 `az login`登录到 Azure 帐户。 


## <a name="limitations"></a>限制

- 虚拟机规模集目前在专用主机上不受支持。
- 初始版本支持以下 VM 系列： DSv3、ESv3、FSv2、LSv2 和 MSv2。 
 

## <a name="create-resource-group"></a>创建资源组 
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 用 az group create 创建资源组。 以下示例在*美国东部*位置创建名为*myDHResourceGroup*的资源组。

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>创建主机组 

**主机组**是代表专用主机集合的资源。 在区域和可用性区域中创建主机组，并向其添加主机。 规划高可用性时，有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用： 
- 跨多个可用性区域。 在这种情况下，你需要在要使用的每个区域中都有一个主机组。
- 跨多个容错域，这些容错域映射到物理机架。 
 
在任一情况下，都需要提供主机组的容错域计数。 如果你不希望跨组中的容错域，请使用容错域计数1。 

您还可以决定使用可用性区域和容错域。 

在此示例中，我们将使用[az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create)创建使用可用性区域和容错域的主机组。 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>其他示例

你还可以使用[az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create)在可用性区域1（而不是容错域）中创建主机组。

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
以下方法使用[az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create)来通过仅使用容错域创建主机组（在不支持可用性区域的区域中使用）。 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>创建主机 

现在，让我们在主机组中创建一个专用主机。 除了主机名称外，还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件生成。  支持以下 SKU 值： DSv3_Type1 和 ESv3_Type1。

有关主机 Sku 和定价的详细信息，请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。

使用[az vm host create](/cli/azure/vm/host#az-vm-host-create)创建主机。 如果为主机组设置了容错域计数，系统会要求你为主机指定容错域。  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>创建虚拟机 
使用[az vm create](/cli/azure/vm#az-vm-create)创建专用主机中的虚拟机。 如果在创建主机组时指定了可用性区域，则需要在创建虚拟机时使用同一区域。

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> 如果在没有足够资源的主机上创建虚拟机，则虚拟机将创建为失败状态。 


## <a name="check-the-status-of-the-host"></a>检查主机的状态

你可以使用[az vm host get instance view 查看](/cli/azure/vm/host#az-vm-host-get-instance-view)主机运行状况状态以及仍可部署到主机的虚拟机数。

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 输出与此类似：
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>作为模板导出 
如果现在想要创建具有相同参数的其他开发环境或与其匹配的生产环境，则可以导出模板。 Resource Manager 使用定义了所有环境参数的 JSON 模板。 通过引用此 JSON 模板构建出整个环境。 可以手动构建 JSON 模板，也可以导出现有的环境以创建 JSON 模板。 使用[az group export](/cli/azure/group#az-group-export)导出资源组。

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

此命令在当前工作目录中创建 `myDHResourceGroup.json` 文件。 从此模板创建环境时，系统会提示输入所有资源名称。 可以通过将 `--include-parameter-default-value` 参数添加到 `az group export` 命令在模板文件中填充这些名称。 编辑 JSON 模板以指定资源名称，或创建指定资源名称的 json 文件。
 
若要从模板创建环境，请使用[az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)。

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>清除 

即使部署了虚拟机，也需要为你的专用主机付费。 应删除当前未使用的任何主机以节省成本。  

只有在没有任何更长的虚拟机使用它时，才能删除该主机。 使用[az vm delete](/cli/azure/vm#az-vm-delete)删除 vm。

```bash
az vm delete -n myVM -g myDHResourceGroup
```

删除 Vm 后，你可以使用[az vm host delete](/cli/azure/vm/host#az-vm-host-delete)删除该主机。

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
删除所有主机后，可以使用[az vm 主机组 delete](/cli/azure/vm/host/group#az-vm-host-group-delete)删除该主机组。  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
也可以在单个命令中删除整个资源组。 这会删除在组中创建的所有资源，包括所有 Vm、主机和主机组。
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[专用主机](dedicated-hosts.md)概述。

- 你还可以使用[Azure 门户](dedicated-hosts-portal.md)创建专用主机。

- [这里](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板，它使用区域和容错域实现了区域中的最大复原能力。