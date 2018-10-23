---
title: Azure Data Lake Storage Gen1 中的网络安全性 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen1 中的 IP 防火墙和虚拟网络集成工作原理
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168040"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>用于 Azure Data Lake Storage Gen1 的虚拟网络集成 - 预览

介绍用于 Azure Data Lake Storage Gen1 的虚拟网络集成（预览版） VNet 集成可以防止对 Azure Data Lake Storage Gen1 帐户进行未经授权的访问，方法是将这些帐户锁定到特定的虚拟网络和子网。 现在可以配置 ADLS Gen1 帐户，让其只接受来自指定虚拟网络和子网的流量，阻止从任何其他位置进行的访问。 这样可以让 ADLS 帐户免受外部威胁。

适用于 ADLS Gen1 的 VNet 集成在虚拟网络和 Azure Active Directory 服务之间使用虚拟网络服务终结点安全性在访问令牌中生成其他安全声明。 然后，系统会使用这些声明对 ADLS Gen1 帐户进行虚拟网络身份验证，然后允许访问。

> [!NOTE]
> 此技术为预览版，建议不要将其用于生产环境。
>
> 使用这些功能不需额外支付相关费用。 帐户按 ADLS Gen1（[定价](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)）和所使用的所有 Azure 服务（[定价](https://azure.microsoft.com/pricing/#product-picker)）的标准费率收费。

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>适用于 ADLS Gen1 的 VNET 集成的方案

使用 ADLS Gen1 VNet 集成，可以限制从指定虚拟网络和子网对 ADLS Gen1 帐户进行访问。  Azure 中的其他 VNet/VM 一旦锁定到指定的 VNet 子网，将无法访问你的帐户。  从功能上说，ADLS Gen1 VNet 集成可以启用与[虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)相同的方案。  存在一些关键差异，详见以下部分。 

![ADLS Gen1 VNet 集成方案图](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> 也可通过 VNet 规则和现有的 IP 防火墙规则来允许从本地网络进行访问。 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>使用 ADLS Gen1 VNet 集成优化路由

VNet 服务终结点的主要优势是通过虚拟网络来[优化路由](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits)。  若要对 ADLS Gen1 帐户执行相同的路由优化，请使用以下[用户定义路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)从 VNet 路由到 ADLS Gen1 帐户：

- **ADLS 公共 IP 地址** – 使用目标 ADLS Gen1 帐户的公共 IP 地址。  可以通过[解析帐户的 DNS 名称](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity)来确定 ADLS Gen1 帐户的 IP 地址。  为每个地址创建一个单独的条目。

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>客户 VNet 中的数据泄露

除了对 ADLS 帐户进行保护以确保从虚拟网络进行安全的访问以外，可能还需确保不将数据泄露给未经授权的帐户。

建议在 VNet 中使用防火墙解决方案，根据目标帐户 URL 筛选出站流量，只允许访问经授权的 ADLS Gen1 帐户。

部分可用选项如下：
- [Azure 防火墙](https://docs.microsoft.com/azure/firewall/overview)：可以针对 VNet [部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)，确保出站 ADLS 流量的安全性，将其锁定到已知授权的帐户 URL。
- [网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)防火墙：如果管理员只允许使用特定的商业防火墙供应商，则可使用 Azure 市场中提供的 NVA 防火墙解决方案执行相同的功能。

> [!NOTE]
> 在数据路径中使用防火墙会在数据路径中引入一个额外的跃点，可能会影响端到端数据交换的网络性能，其中包括对可用吞吐量和连接延迟的影响。 

## <a name="limitations"></a>限制
1.  在预览版中增加 HDInsight 群集功能以后，此类群集必须是新创建的群集。  在 ADLS Gen1 VNet 集成功能推出之前创建的群集必须重新创建，否则不支持这项新功能。 
2.  创建新的 HDInsight 群集时，如果选择的 ADLS Gen1 帐户已启用 VNet 集成功能，则会导致此过程失败。 必须先禁用 VNet 规则，也可以通过 ADLS 帐户的“防火墙和虚拟网络”边栏选项卡“允许从所有网络和服务进行访问”。  有关详细信息，请参阅[例外](##Exceptions)部分。
3.  ADLS Gen1 VNet 集成预览版不适用于 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。  
4.  支持 VNet 的 ADLS Gen1 帐户中的文件/文件夹数据不能从门户进行访问。  这包括从 VNET 中的某个 VM 进行访问，以及使用数据资源管理器之类的活动。  帐户管理活动仍可使用。  支持 VNET 的 ADLS 帐户中的文件/文件夹数据可以通过所有非门户资源进行访问，这些资源包括：SDK 访问权限、PowerShell 脚本、其他 Azure 服务（不是源自门户时），等等。 

## <a name="configuration"></a>配置

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>步骤 1：将 VNET 配置为使用 AAD 服务终结点
1.  转到 Azure 门户并登录到帐户。 
2.  在订阅中[创建新的虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)，或者导航到现有的虚拟网络。  目前，VNET 与 ADLS Gen 1 帐户必须位于同一区域。 
3.  在“虚拟网络”边栏选项卡中，选择“服务终结点”。 
4.  单击“添加”，添加新的服务终结点。
![添加 VNet 服务终结点](media/data-lake-store-network-security/config-vnet-1.png)
5.  选择 **Microsoft.AzureActiveDirectory** 作为终结点的服务。
![选择 Microsoft.AzureActiveDirectory 服务终结点](media/data-lake-store-network-security/config-vnet-2.png)
6.  选择要为其启用连接性的子网，然后单击“添加”。
![选择子网](media/data-lake-store-network-security/config-vnet-3.png)
7.  添加服务终结点可能需要长达 15 分钟的时间。 添加以后，它就会显示在列表中。 验证它是否显示以及所有详细信息是否如所配置的那样。 
![成功添加服务终结点](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>步骤 2：为 ADLS Gen1 帐户设置允许的 VNET/子网
1.  配置 VNET 以后，请在订阅中[创建新的 Azure Data Lake Storage Gen1 帐户](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)，或者导航到现有的 ADLS Gen1 帐户。 目前，ADLS Gen 1 帐户必须与 VNET 位于同一区域。 
2.  选择“防火墙和虚拟网络”。

  > [!NOTE]
  > 如果在设置中看不到“防火墙和虚拟网络”，则请注销门户。 关闭浏览器。 清除浏览器缓存。 重启计算机并重试。

  ![向 ADLS 帐户添加 VNet 规则](media/data-lake-store-network-security/config-adls-1.png)
3.  选择“所选网络”。 
4.  单击“添加现有虚拟网络”。
  ![添加现有虚拟网络](media/data-lake-store-network-security/config-adls-2.png)
5.  选择要启用连接性的 VNet 和子网，然后单击“添加”。
  ![选择 VNet 和子网](media/data-lake-store-network-security/config-adls-3.png)
6.  确保 VNet 和子网在列表中正确显示，然后单击“保存”。
  ![保存新规则](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > 在保存以后，可能需要长达 5 分钟的时间设置才会生效。

7.  [可选] 除了 VNet 和子网以外，如果还希望允许从特定的 IP 地址进行连接，则可在同一页的“防火墙”部分完成相关设置。 

## <a name="exceptions"></a>例外
在“防火墙和虚拟网络”边栏选项卡的“例外”区域有两个复选框，勾选以后即可从 Azure 上的一组服务和虚拟机进行连接。
![防火墙和虚拟网络异常](media/data-lake-store-network-security/firewall-exceptions.png)
- **允许所有 Azure 服务访问此 Data Lake Storage Gen1 帐户**：允许所有 Azure 服务（例如 Azure 数据工厂、事件中心、所有 Azure VM 等） 与 ADLS 帐户通信。

- **允许 Azure Data Lake Analytics 访问此 Data Lake Storage Gen1 帐户**：允许 Azure Data Lake Analytics 服务连接到此 ADLS 帐户。 

建议始终禁用这些例外，只在需要从 VNet 外部的这些其他的服务进行连接时才启用它们。
