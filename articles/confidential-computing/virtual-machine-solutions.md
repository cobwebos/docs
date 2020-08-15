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
ms.openlocfilehash: f9b73e0919d660947edd0417f7379b3f6e6140c0
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245846"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure 虚拟机上的解决方案

本文介绍了如何部署运行基于 [Intel Software Guard Extension](https://software.intel.com/sgx) (Intel SGX) 的 Intel 处理器的 Azure 机密计算虚拟机 (VM)。 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure 机密计算 VM 大小

Azure 机密计算虚拟机旨在保护云中处理的数据和代码的机密性与完整性 

[DCsv2 系列](../virtual-machines/dcv2-series.md) VM 是最新的机密计算大小系列。 这些 VM 支持更大范围的部署功能，其 Enclave 页面缓存 (EPC) 是 DC 系列 VM 的 2 倍，并且这些 VM 比 DC 系列 VM 有更多的大小可供用户选择。 [DC 系列](../virtual-machines/sizes-previous-gen.md#preview-dc-series) VM 目前以预览版提供，将来会被弃用，并且不会包含在正式版中。

遵循[快速入门教程](quick-create-marketplace.md)，开始通过 Microsoft 商业市场部署 DCsv2 系列 VM。

### <a name="current-available-sizes-and-regions"></a>当前可用大小和区域

若要获取可用区域和可用性区域中所有正式版机密计算 VM 大小的列表，请在 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) 中运行以下命令：

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

若要更详细地了解上述大小，请运行以下命令：

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>专用主机要求
在 DCSv2 系列 VM 系列中部署 **Standard_DC8_v2** 虚拟机大小将占用整个主机，而不会与其他租户或订阅共享。 此 VM SKU 系列提供了你可能需要的隔离，以满足通常通过专用主机服务实现的符合性和安全法规要求。 选择 **Standard_DC8_v2** SKU 时，物理主机服务器会将所有可用的硬件资源（包括 EPC 内存）分配给虚拟机。 请注意，此功能在基础结构设计中存在，并且将支持 **Standard_DC8_v2** 的所有功能。 此部署与其他 Azure VM 系列提供的 [Azure 专用主机](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts) 服务不同。


## <a name="deployment-considerations"></a>部署注意事项

遵循快速入门教程，在不到 10 分钟的时间内部署 DCsv2 系列虚拟机。 

- **Azure 订阅** – 若要部署机密计算 VM 实例，请考虑使用即用即付订阅或其他购买选项。 如果使用 [Azure 免费帐户](https://azure.microsoft.com/free/)，则不会有相应 Azure 计算核心数量的配额。

- **定价和区域可用性** - 在[虚拟机定价页](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)上查找 DCsv2 系列 VM 的定价。 有关各 Azure 区域推出的产品，请查看 [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)（按区域提供的产品）。


- **核心配额** - 可能需要在 Azure 订阅中在默认值的基础上增加核心配额。 订阅可能也会限制可在特定 VM 大小系列（包括 DCsv2 系列）中部署的核心数目。 若要请求提高配额，可免费[提出在线客户支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。 请注意，默认限制可能会因订阅类别而异。

  > [!NOTE]
  > 如果有大规模容量需求，请联系 Azure 支持。 Azure 配额为信用额度，而不是容量保障。 不管配额是什么，都只根据所用的核心数计费。
  
- **调整大小** – 由于机密计算实例包含专用的硬件，你只能在同一大小系列内调整其大小。 例如，对于 DCsv2 系列 VM，只能将其从一种 DCsv2 系列大小调整为另一种 DCsv2 系列大小。 不支持从非机密计算大小调整为机密计算大小。  

- **映像** – 若要在机密计算实例上提供 Intel Software Guard Extension (Intel SGX) 支持，所有部署需要在第 2 代映像上运行。 Azure 机密计算支持 Ubuntu 18.04 Gen 2、Ubuntu 16.04 Gen 2、Windows Server 2019 Gen 2 和 Windows Server 2016 Gen 2 上运行的工作负荷。 请阅读 [Azure 对第 2 代 VM 的支持](../virtual-machines/linux/generation-2.md)以详细了解支持的方案和不支持的方案。 

- **存储** – Azure 机密计算虚拟机数据磁盘和临时 OS 磁盘位于 NVMe 磁盘上。 实例仅支持高级 SSD 和标准 SSD 磁盘，而不支持超级 SSD 或标准 HDD。 虚拟机大小 **DC8_v2** 不支持高级存储。 

- **磁盘加密** – 机密计算实例目前不支持磁盘加密。 

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和灾难恢复注意事项

在 Azure 中使用虚拟机时，你需要负责实施高可用性和灾难恢复解决方案，以避免出现任何停机。 

Azure 机密计算目前不支持通过可用性区域实现区域冗余。 若要实现机密计算的最高可用性和冗余，请使用[可用性集](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。 由于硬件限制，机密计算实例的可用性集最多只能包含 10 个更新域。 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>用 Azure 资源管理器 (ARM) 模板进行部署

Azure 资源管理器是 Azure 的部署和管理服务。 它提供一个管理层用于在 Azure 订阅中创建、更新和删除资源。 部署后，可以使用访问控制、锁和标记等管理功能来保护和组织资源。

若要了解 ARM 模板，请参阅 [模板部署概述](../azure-resource-manager/templates/overview.md)。

若要在 ARM 模板中部署 DCsv2 系列 VM，你将利用 [虚拟机资源](../virtual-machines/windows/template-description.md)。 确保为 **vmSize** 和 **imageReference** 指定正确的属性。

### <a name="vm-size"></a>VM 大小

在虚拟机资源的 ARM 模板中指定以下大小之一。 此字符串用作 **properties** 中的 **vmSize**。

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS 映像

在 **properties** 下，还必须在 **storageProfile** 下引用一个映像。 请仅将以下某一映像用于 **imageReference**。

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
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

在本文中，你已了解在创建机密计算虚拟机时所要满足的条件和所需的配置。 现在，你可以转到 Microsoft Azure 市场来部署 DCsv2 系列 VM。

> [!div class="nextstepaction"]
> [在 Azure 市场中部署 DCsv2 系列虚拟机](quick-create-marketplace.md)
