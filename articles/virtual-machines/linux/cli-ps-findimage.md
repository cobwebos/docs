---
title: 使用 Azure CLI 选择 Linux VM 映像 | Microsoft Docs
description: 了解如何使用 Azure CLI 确定发布服务器、产品/服务、SKU 和市场 VM 映像的版本。
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 53e5100023fb76b4daf468d3f2027f2b5c7f31d7
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292953"
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>如何使用 Azure CLI 在 Azure 市场中查找 Linux VM 映像
本主题介绍如何使用 Azure CLI 2.0 在 Azure 市场中查找 VM 映像。 使用 CLI、资源管理器模板或其他工具以编程方式创建 VM 时，使用此信息指定市场映像。

还可以使用 [Azure 市场](https://azuremarketplace.microsoft.com/)店面、[Azure 门户](https://portal.azure.com)或 [Azure PowerShell](../windows/cli-ps-findimage.md) 浏览可用的映像和产品/服务。 

确保已安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已登录到 Azure 帐户 (`az login`)。

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>列出常用映像

运行 [az vm image list](/cli/azure/vm/image#az_vm_image_list) 命令，无需选择 `--all` 选项即可在 Azure 市场中查看常用 VM 映像的列表。 例如，运行以下命令以表格形式显示缓存的常用映像列表：

```azurecli
az vm image list --output table
```

输出包括映像 URN（Urn 列中的值）。 使用其中一个常用市场映像创建 VM 时，可选择指定 *UrnAlias*（一种简短格式，如 *UbuntuLTS*）。

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

若要在市场中查找特定 VM 映像，请结合使用 `az vm image list` 命令和 `--all` 选项。 此版本命令完成需要一些时间，且会返回冗长输出，因此通常可按 `--publisher` 或其他参数筛选列表。 

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

如果没有使用 `--location` 选项指定一个特定位置，则将返回默认位置的值。 （通过运行 `az configure --defaults location=<location>` 设置不同默认位置。）

例如，以下命令列出了“西欧”位置中的所有 Debian 8 SKU：

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
在某个位置查找映像的另一种方法是，运行序列中的 [az vm image list-publishers](/cli/azure/vm/image#az_vm_image_list_publishers)、[az vm image list-offers](/cli/azure/vm/image#az_vm_image_list_offers) 和 [az vm image list-skus](/cli/azure/vm/image#az_vm_image_list_skus) 命令。 可以使用这些命令确定以下值：

1. 列出映像发布者。
2. 对于给定的发布者，列出其产品。
3. 对于给定的产品，列出其 SKU。

然后，对于所选 SKU，可以选择要部署的版本。

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
使用此信息可以从特定发布者找到产品/服务。 例如，如果 *Canonical* 是美国西部位置的映像发布者，可通过运行 `azure vm image list-offers` 查找其产品/服务。 传递位置和发布者，如以下示例中所示：

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

现在，可通过记下 URN 值准确地选择想要使用的映像。 通过 [az vm create](/cli/azure/vm#az_vm_create) 命令创建 VM 时，可将此值与 `--image` 参数一起传递。 记住，可选择将 URN 中的版本号替换为“latest”。 此版本始终是映像的最新版本。 

如果使用资源管理器模板部署 VM，请在 `imageReference` 属性中单独设置映像参数。 请参阅[模板参考](/azure/templates/microsoft.compute/virtualmachines)。

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>查看计划属性
若要查看映像的购买计划信息，请运行 [az vm image show](/cli/azure/image#az_image_show) 命令。 如果输出中的 `plan` 属性不是 `null`，则映像有条款，在以编程方式部署前需要接受该条款。

例如，Canonical Ubuntu Server 16.04 LTS 映像没有附加条款，因为 `plan` 信息为 `null`：

```azurecli
az vm image show --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --version 16.04.201801260
```

输出：

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/16.04.201801260",
  "location": "westus",
  "name": "16.04.201801260",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

对 Bitnami 映像认证的 RabbitMQ 运行类似的命令显示以下 `plan` 属性：`name`、`product` 和 `publisher`。 （某些映像还具有 `promotion code` 属性。）若要部署此映像，请参阅以下部分，以接受条款并启用编程部署。

```azurecli
az vm image show --location westus --publisher bitnami --offer rabbitmq --sku rabbitmq --version 3.7.1801130730
```
输出：

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1801130730",
  "location": "westus",
  "name": "3.7.1801130730",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>接受条款
若要查看并接受许可条款，请使用 [az vm image accept-terms](/cli/azure/vm/image?#az_vm_image_accept_terms) 命令。 接受条款时，将在订阅中启用编程部署。 对于映像的每个订阅，只需接受条款一次。 例如：

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

输出包括指向许可条款的 `licenseTextLink`，并指示 `accepted` 的值为 `true`：

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2018-02-22T04:06:28.7641907Z",
  "signature": "WVIEA3LAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNKLNLWI",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>使用购买计划参数进行部署
接受映像的条款后，便可以在订阅中部署 VM。 若要使用 `az vm create` 命令部署映像，除了提供映像的 URN 外还需提供购买计划参数。 例如，若要使用 Bitnami 映像认证的 RabbitMQ 部署 VM，请运行以下命令：

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami

```



## <a name="next-steps"></a>后续步骤
若要使用映像信息快速创建虚拟机，请参阅[使用 Azure CLI 创建和管理 Linux VM](tutorial-manage-vm.md)。