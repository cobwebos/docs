---
title: Azure Data Lake Storage Gen1 中的网络安全性 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen1 中的虚拟网络集成如何工作
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
ms.openlocfilehash: ed2d1256508e588000970879dae7ac653797fbf9
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883307"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>用于 Azure Data Lake Storage Gen1 的虚拟网络集成

本文介绍了用于 Azure Data Lake Storage Gen1 的虚拟网络集成。 使用虚拟网络集成，可以将帐户配置为仅接受特定虚拟网络和子网的流量。 

此功能有助于确保 Data Lake Storage 帐户免受外部威胁。

适用于 Data Lake Storage Gen1 的虚拟网络集成在虚拟网络和 Azure Active Directory (Azure AD) 之间使用虚拟网络服务终结点安全性在访问令牌中生成其他安全声明。 然后，系统会使用这些声明对 Data Lake Storage Gen1 帐户进行虚拟网络身份验证，然后允许访问。

> [!NOTE]
> 使用这些功能不需额外支付相关费用。 帐户按 Data Lake Storage Gen1 的标准费率收费。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)。 至于你使用的所有其他 Azure 服务，请参阅[定价](https://azure.microsoft.com/pricing/#product-picker)。

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>用于 Data Lake Storage Gen1 的虚拟网络集成的方案

使用 Data Lake Storage Gen1 虚拟网络集成，可以限制对 Data Lake Storage Gen1 帐户的访问，仅允许从特定虚拟网络和子网进行访问。 将帐户锁定到指定的虚拟网络子网以后，Azure 中的其他虚拟网络/VM 将不允许访问该帐户。 从功能上说，Data Lake Storage Gen1 虚拟网络集成可以启用与[虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)相同的方案。 以下部分详述了一些关键差异。 

![Data Lake Storage Gen1 虚拟网络集成的方案图](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> 也可通过虚拟网络规则和现有的 IP 防火墙规则来允许从本地网络进行访问。 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>使用 Data Lake Storage Gen1 虚拟网络集成优化路由

虚拟网络服务终结点的主要优势是通过虚拟网络来[优化路由](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits)。 可以对 Data Lake Storage Gen1 帐户进行相同的路由优化。 请使用以下[用户定义路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)，从虚拟网络路由到 Data Lake Storage Gen1 帐户。

**Data Lake Storage 公共 IP 地址** - 使用目标 Data Lake Storage Gen1 帐户的公共 IP 地址。 若要确定 Data Lake Storage Gen1 帐户的 IP 地址，请[解析其 DNS 名称](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity)。 为每个地址创建一个单独的条目。

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>客户虚拟网络中的数据泄露

除了对 Data Lake Storage 帐户进行保护以确保从虚拟网络进行安全的访问以外，可能还需确保不将数据泄露给未经授权的帐户。

请在虚拟网络中使用防火墙解决方案，根据目标帐户 URL 筛选出站流量。 只允许访问经授权的 Data Lake Storage Gen1 帐户。

部分可用选项如下：
- [Azure 防火墙](https://docs.microsoft.com/azure/firewall/overview)：针对虚拟网络[部署和配置 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。 确保出站 Data Lake Storage 流量的安全性，将其锁定到已知授权的帐户 URL。
- [网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)防火墙：管理员可能只允许使用特定的商业防火墙供应商。 请使用 Azure 市场中提供的网络虚拟设备防火墙解决方案执行相同的功能。

> [!NOTE]
> 在数据路径中使用防火墙会在数据路径中引入一个额外的跃点， 可能会影响端到端数据交换的网络性能， 还可能会影响吞吐量可用性和连接延迟。 

## <a name="limitations"></a>限制

- 在 Data Lake Storage Gen1 虚拟网络集成功能推出之前创建的 HDInsight 群集必须重新创建，否则不支持这项新功能。
 
- 如果创建新的 HDInsight 群集且选择的 Data Lake Storage Gen1 帐户已启用虚拟网络集成功能，则此过程会失败。 首先，请禁用虚拟网络规则。 也可以在 Data Lake Storage 帐户的“防火墙和虚拟网络”边栏选项卡上选择“允许从所有网络和服务进行访问”。 然后，在最终重新启用虚拟网络规则或取消选择**允许来自所有网络和服务的访问**之前，创建 HDInsight 群集。 有关详细信息，请参阅[例外](##Exceptions)部分。

- Data Lake Storage Gen1 虚拟网络集成不适用于 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
  
- 支持虚拟网络的 Data Lake Storage Gen1 帐户中的文件和文件夹数据不能从门户进行访问。 此限制包括从虚拟网络中的某个 VM 进行的访问，以及使用数据资源管理器之类的活动。 帐户管理活动仍可使用。 支持虚拟网络的 Data Lake Storage 帐户中的文件和文件夹数据可以通过所有非门户资源进行访问。 这些资源包括 SDK 访问权限、PowerShell 脚本以及其他 Azure 服务（不是源自门户时）。 

## <a name="configuration"></a>配置

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>步骤 1：配置虚拟网络，以便使用 Azure AD 服务终结点

1.  转到 Azure 门户并登录帐户。
 
2.  在订阅中[创建新的虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。 也可转到现有的虚拟网络。 虚拟网络必须位于 Data Lake Storage Gen 1 帐户所在的区域。
 
3.  在“虚拟网络”边栏选项卡中，选择“服务终结点”。
 
4.  选择“添加”，添加新的服务终结点。

    ![添加虚拟网络服务终结点](media/data-lake-store-network-security/config-vnet-1.png)

5.  选择 **Microsoft.AzureActiveDirectory** 作为终结点的服务。

     ![选择 Microsoft.AzureActiveDirectory 服务终结点](media/data-lake-store-network-security/config-vnet-2.png)

6.  选择要为其启用连接性的子网。 选择 **添加** 。

    ![选择子网](media/data-lake-store-network-security/config-vnet-3.png)

7.  添加服务终结点可能需要长达 15 分钟的时间。 添加以后，它会显示在列表中。 验证它是否显示以及所有详细信息是否如所配置的那样。
 
    ![成功添加服务终结点](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>步骤 2：为 Data Lake Storage Gen1 帐户设置允许的虚拟网络或子网

1.  配置虚拟网络以后，请在订阅中[创建新的 Azure Data Lake Storage Gen1 帐户](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)。 也可转到现有的 Data Lake Storage Gen1 帐户。 Data Lake Storage Gen 1 帐户必须位于虚拟网络所在的区域。
 
2.  选择“防火墙和虚拟网络”。

    > [!NOTE]
    > 如果在设置中看不到“防火墙和虚拟网络”，则请注销门户。 关闭浏览器并清除浏览器缓存。 重启计算机并重试。

       ![向 Data Lake Storage 帐户添加虚拟网络规则](media/data-lake-store-network-security/config-adls-1.png)

3.  选择“所选网络”。
 
4.  选择“添加现有虚拟网络”。

    ![添加现有虚拟网络](media/data-lake-store-network-security/config-adls-2.png)

5.  选择要启用连接性的虚拟网络和子网。 选择 **添加** 。

    ![选择虚拟网络和子网](media/data-lake-store-network-security/config-adls-3.png)

6.  确保虚拟网络和子网在列表中正确显示。 选择“保存”。

    ![保存新规则](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > 在保存以后，可能需要长达 5 分钟的时间设置才会生效。

7.  [可选] 在“防火墙和虚拟网络”页的“防火墙”部分，可以允许从特定 IP 地址进行连接。 

## <a name="exceptions"></a>例外
可以允许从所选虚拟网络外部的 Azure 服务和 VM 进行连接。 在“防火墙和虚拟网络”边栏选项卡的“例外”区域中，从两个选项中进行选择：
 
- **允许所有 Azure 服务访问此 Data Lake Storage Gen1 帐户**。 此选项允许 Azure 服务（例如 Azure 数据工厂、Azure 事件中心、所有 Azure VM）与 Data Lake Storage 帐户通信。

- **允许 Azure Data Lake Analytics 访问此 Data Lake Storage Gen1 帐户**。 此选项允许 Data Lake Analytics 连接到此 Data Lake Storage 帐户。 

  ![防火墙和虚拟网络例外](media/data-lake-store-network-security/firewall-exceptions.png)

建议始终禁用这些例外， 只在需要从虚拟网络外部的这些其他的服务进行连接时才启用它们。
