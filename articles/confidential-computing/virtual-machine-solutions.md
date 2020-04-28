---
title: 虚拟机上的 Azure 机密计算解决方案
description: 了解虚拟机上的 Azure 机密计算解决方案。
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: e574ac33e5f7da814c4bd813fc1c083c7cb4c2c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187879"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure 虚拟机上的解决方案

本文介绍如何部署运行 intel[软件防护扩展](https://software.intel.com/sgx)（intel SGX）支持的 intel 处理器的 Azure 机密计算虚拟机（vm）。 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure 机密计算 VM 大小

Azure 机密计算虚拟机旨在保护数据和代码的机密性和完整性，同时在云中进行处理 

[DCsv2 系列](../virtual-machines/dcv2-series.md)Vm 是最新且最新的机密计算大小系列。 这些 Vm 支持更大范围的部署功能，与我们的 DC 系列 Vm 相比，Enclave 页缓存（EPC）和更大的大小选择。 [DC 系列](../virtual-machines/sizes-previous-gen.md#preview-dc-series)vm 当前处于预览状态，将被弃用，并且不会在正式发行版本中包括在内。

按照[快速入门教程](quick-create-marketplace.md)中的说明，开始通过 Microsoft 商用 Marketplace 部署 DCSV2 系列 VM。

### <a name="current-available-sizes-and-regions"></a>当前可用的大小和区域

若要获取可用区域和可用性区域中所有公开可用的机密计算 VM 大小的列表，请在[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)中运行以下命令：

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

从2020年4月起，这些 Sku 在以下区域和可用性区域中提供：

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

有关上述大小的更详细视图，请运行以下命令：

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>部署注意事项

按照快速入门教程，在10分钟内部署 DCsv2 系列虚拟机。 

- **Azure 订阅**-若要部署机密计算 VM 实例，请考虑使用即用即付订阅或其他购买选项。 如果使用的是[azure 免费帐户](https://azure.microsoft.com/free/)，则不会有合适数量的 azure 计算核心的配额。

- **定价和区域可用性**-找到[虚拟机定价页](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)上的 DCsv2 系列 vm 的定价。 有关各 Azure 区域推出的产品，请查看 [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)（按区域提供的产品）。


- **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅还可能会限制在某些 VM 大小系列（包括 DCsv2 系列）中可部署的核心数。 若要请求增加配额，请免费[打开联机客户支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。 请注意，默认限制可能因订阅类别而异。

  > [!NOTE]
  > 如果有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  
- **调整大小**–由于其专用硬件，只能调整相同大小系列中的机密计算实例的大小。 例如，只能将 DCsv2 系列的 VM 从一个 DCsv2 系列大小调整为另一个。 不支持从非机密计算大小调整为机密计算大小。  

- **映像**-若要为机密计算实例提供 Intel 软件防护扩展（intel SGX）支持，所有部署都需要在第2代映像上运行。 Azure 机密计算支持在 Ubuntu 18.04 第2代、Ubuntu 16.04 第2代和 Windows Server 2016 Gen 2 上运行的工作负荷。 阅读有关[Azure 上第2代 vm 的支持](../virtual-machines/linux/generation-2.md)，了解有关受支持和不支持的方案的详细信息。 

- **存储**– Azure 机密计算虚拟机数据磁盘和临时操作系统磁盘位于 NVMe 磁盘上。 实例仅支持高级 SSD 和标准 SSD 磁盘，而不支持超级 SSD 或标准 HDD。 虚拟机大小**DC8_v2**不支持高级存储。 

- **磁盘加密**–机密计算实例目前不支持磁盘加密。 

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和灾难恢复注意事项

使用 Azure 中的虚拟机时，你负责实现高可用性和灾难恢复解决方案，以避免任何停机。 

Azure 机密计算此时不支持通过可用性区域区域冗余。 为了获得机密计算的最高可用性和冗余，请使用[可用性集](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。 由于硬件限制，适用于机密计算实例的可用性集最多只能有10个更新域。 

## <a name="deploying-via-an-azure-resource-manager-template"></a>通过 Azure 资源管理器模板进行部署 

Azure 资源管理器是 Azure 的部署和管理服务。 它提供一个管理层用于在 Azure 订阅中创建、更新和删除资源。 部署后，可以使用访问控制、锁和标记等管理功能来保护和组织资源。

若要了解 Azure 资源管理器模板，请参阅[模板部署概述](../azure-resource-manager/templates/overview.md)。

要将 DCsv2 系列 VM 部署到 ARM 模板，你将利用[虚拟机资源](../virtual-machines/windows/template-description.md)。 你需要确保指定**vmSize**和**imageReference**的正确属性。

### <a name="vm-size"></a>VM 大小

在虚拟机资源的 ARM 模板中指定以下大小之一。 此字符串将作为**vmSize**放在**属性**中。

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS 映像

在 "**属性**" 下，还必须在**storageProfile**下引用图像。 对于**imageReference**，只使用以下映像*之一*。

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>后续步骤 

本文介绍了创建机密计算虚拟机时所需的条件和配置。 现在，你可以转到 Azure Marketplace 部署 DCsv2 系列 VM。

> [!div class="nextstepaction"]
> [在 Azure Marketplace 中部署 DCsv2 系列虚拟机](quick-create-marketplace.md)