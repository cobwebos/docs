---
title: "使用 Azure CLI 选择 Linux VM 映像 | Microsoft Docs"
description: "了解如何使用 Azure CLI 确定发布服务器、产品/服务、SKU 和 Marketplace VM 映像的版本。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e0c27a7ee9e9a7ab1a3b004e070fa556b56a36a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>如何使用 Azure CLI 在 Azure Marketplace 中查找 Linux VM 映像
本主题介绍如何使用 Azure CLI 2.0 在 Azure Marketplace 中查找 VM 映像。 创建 Linux VM 时使用此信息来指定 Marketplace 映像。

确保已安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已登录到 Azure 帐户 (`az login`)。

## <a name="terminology"></a>术语

CLI 和其他 Azure 工具中根据层次结构标识 Marketplace 映像：

* 发布者 - 创建映像的组织。 示例：Canonical
* 产品/服务 - 发布者创建的一组相关映像。 示例：Ubuntu 服务器
* SKU - 产品/服务的实例，例如分发的主要版本。 示例：16.04-LTS
* 版本 - 映像 SKU 的版本号。 指定映像时，可将版本号替换为“latest”，这会选择最新的分发版本。

若要指定 Marketplace 映像，通常使用 URN 映像。 URN 将这些值合并，值之间用冒号 (:) 字符隔开：*发布者*:*产品/服务*:*Sku*:*版本*。 


## <a name="list-popular-images"></a>列出常用映像

运行 [az vm image list](/cli/azure/vm/image#list) 命令，无需选择 `--all` 选项即可在 Azure Marketplace 中查看常用 VM 映像的列表。 例如，运行以下命令以表格形式显示缓存的常用映像列表：

```azurecli
az vm image list --output table
```

输出包括用以指定映像的 URN（Urn 列中的值）。 使用其中一个常用 Marketplace 映像创建 VM 时，可选择指定 URN 别名，如 UbuntuLTS。

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>查找特定映像

若要在 Marketplace 中查找特定 VM 映像，请结合使用 `az vm image list` 命令和 `--all` 选项。 此版本命令完成需要一些时间，且会返回冗长输出，因此通常可按 `--publisher` 或其他参数筛选列表。 

例如，以下命令显示所有 Debian 产品/服务（请记住，不使用 `--all` 开关时，只搜索常见映像的本地缓存）：

```azurecli
az vm image list --offer Debian --all --output table 

```

部分输出： 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

通过 `--location`、`--publisher` 和 `--sku` 选项应用类似的筛选器。 甚至可以在筛选器上执行部分匹配，如搜索 `--offer Deb` 以查找所有 Debian 映像。

如果没有使用 `--location` 选项指定一个特定位置，则将默认返回 `westus` 的值。 （通过运行 `az configure --defaults location=<location>` 设置不同默认位置。）

例如，以下命令列出了 `westeurope` 中的所有 Debian 8 SKU：

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

部分输出：

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>浏览映像 
在某个位置查找映像的另一种方法是，运行序列中的 [az vm image list-publishers](/cli/azure/vm/image#list-publishers)、[az vm image list-offers](/cli/azure/vm/image#list-offers) 和 [az vm image list-skus](/cli/azure/vm/image#list-skus) 命令。 可以使用这些命令确定以下值：

1. 列出映像发布者。
2. 对于给定的发布者，列出其产品。
3. 对于给定的产品，列出其 SKU。


例如，以下命令列出了美国西部位置的映像发布者：

```azurecli
az vm image list-publishers --location westus --output table
```

部分输出：

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
使用此信息可以从特定发布者找到产品/服务。 例如，如果 Canonical 是美国西部位置的映像发布者，可通过运行 `azure vm image list-offers` 查找其产品/服务。 传递位置和发布者，如以下示例中所示：

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

输出：

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
你将看到在美国西部区域中，Canonical 在 Azure 上发布 UbuntuServer 产品。 但是，有哪些 SKU 呢？ 要获取这些值，请运行 `azure vm image list-skus`，并对找到的位置、发布者和产品/服务进行设置：

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

输出：

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

最后，使用 `az vm image list` 命令查找所需的特定版本的 SKU，例如，16.04-LTS：

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

输出：

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```
## <a name="next-steps"></a>后续步骤
现在，可通过记下 URN 值准确地选择想要使用的映像。 通过 [az vm create](/cli/azure/vm#create) 命令创建 VM 时，可将此值与 `--image` 参数一起传递。 记住，可选择将 URN 中的版本号替换为“latest”。 此版本始终是分发的最新版本。 若要使用 URN 信息快速创建虚拟机，请参阅[使用 Azure CLI 创建和管理 Linux VM](tutorial-manage-vm.md)。
