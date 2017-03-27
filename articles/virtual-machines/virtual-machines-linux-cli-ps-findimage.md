---
title: "使用 Azure CLI 选择 Linux VM 映像 | Microsoft Docs"
description: "了解在使用 Resource Manager 部署模型创建 Linux 虚拟机时如何确定映像的确定发布者、产品和 SKU。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: e9be064425ae6b9048098333cc664310e7128314
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-find-linux-vm-images-with-the-azure-cli"></a>如何使用 Azure CLI 查找 Linux VM 映像
本主题介绍如何查找每个部署目标位置的发布者、产品、SKU 和版本。 


## <a name="use-azure-cli-20"></a>使用 Azure CLI 2.0

[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) 后，可使用 `az vm image list` 命令查看常用 VM 映像的缓存列表。 例如，下面的命令`az vm image list -o table` 示例显示：

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>查找所有当前映像

若要获取所有映像的当前列表，请使用具有 `--all` 选项的 `az vm image list` 命令。 与 Azure CLI 1.0 命令不同，`az vm image list --all` 命令在默认情况下返回 **westus** 中的所有映像（除非指定特定 `--location` 参数），因此 `--all` 命令需要一些时间才能完成。 如果要以交互方式进行调查，请使用 `az vm image list --all > allImages.json`，这会返回 Azure 上当前可用的所有映像的列表，并将它存储为文件以供本地使用。 

可以指定几个选项中的一个，以将搜索范围限制为特定位置、产品/服务、发布者或 sku（如果已记住一个或多个）。 如果未指定位置，则返回 **westus** 的值。

### <a name="find-specific-images"></a>查找特定映像

将 `az vm image list` 与筛选器一起使用可查找特定信息。 例如，下面显示可用于 **Debian** 的**产品/服务**（请记住，不使用 `--all` 开关时，只搜索公共映像的本地缓存）：

```azurecli
az vm image list --offer Debian -o table --all
```

输出类似下面这样： 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

可以对 **--publisher** 和 **--sku** 选项执行类似筛选。 甚至可以对筛选器执行部分匹配，如搜索 **--offer Deb** 以查找所有 Debian 映像，或搜索 **--publisher Micr** 以查找 Microsoft 发布的所有映像。

如果知道部署位置，则可以将常规映像搜索结果与 `az vm image list-skus`、`az vm image list-offers` 和 `az vm image list-publishers` 命令一起使用，以查找所需的精确内容以及可以进行部署的位置。 例如，如果从前面的示例知道 `credativ` 具有 Debian 产品/服务，则可以使用 `--location` 和其他选项查找所需的精确内容。 下面的示例查找在 **westeurope** 中查找 Debian 8 映像：

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

输出为：

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>使用 Azure CLI 1.0 

> [!NOTE]
> 本文介绍如何使用支持 Azure Resource Manager 部署模型的 Azure CLI 1.0 或 Azure PowerShell 安装来导航和选择虚拟机映像。 作为先决条件，更改为 Resource Manager 模式。 使用 Azure CLI 时，键入 `azure config mode arm` 即可进入该模式。 
> 

查找映像的最快速方式是调用 `azure vm image list` 命令，并传递位置、发布者名称（不区分大小写！）和产品/服务（如果你知道该产品/服务）。 例如，如果你知道“Canonical”是“UbuntuServer”产品的发布者，则以下列表只是简短的示例（许多列表相当长）。

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

**Urn** 列是传递给 `azure vm quick-create` 的表单。

不过，你通常还不知道什么可用。 在这种情况下，可以通过在提示符处使用 `azure vm image list-publishers` 命令并指定数据中心位置来导航映像。 例如，以下列表是美国西部位置的所有映像发布者（将位置设为小写并删除标准位置中的空格，以传递位置参数）。

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

这些列表可能相当长，因此前面的示例列表只是一个代码片段。 假设我注意到 Canonical 事实上是美国西部位置的映像发布者。 现在可以调用 `azure vm image list-offers` 来查找其发布的产品，并在提示文字后面输入位置和发布者，如以下示例所示：

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

现在，我们知道在美国西部区域中，Canonical 在 Azure 上发布 **UbuntuServer** 产品。 但是，有哪些 SKU 呢？ 若要获取这些值，请调用 `azure vm image list-skus`，并在提示文字后面输入你找到的位置、发布者和产品/服务。

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

利用这些信息，现在可以通过在顶部调用原始调用，准确地找到你需要的映像。

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>后续步骤
现在，你可以确切地选择想要使用的映像。 若要使用刚刚找到的 URN 信息快速创建虚拟机，或要使用包含该 URN 信息的模板，请参阅[使用 Azure CLI 创建 Linux VM](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

