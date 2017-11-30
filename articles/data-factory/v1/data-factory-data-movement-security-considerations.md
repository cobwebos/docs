---
title: "Azure 数据工厂中数据移动的安全注意事项 | Microsoft Docs"
description: "了解如何为 Azure 数据工厂中的数据移动提供保护。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 5857a0286dce92493c4d538f79ef9f47012bc0a2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure 数据工厂 - 数据移动的安全注意事项

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[数据工厂版本 2 的数据移动安全注意事项](../data-movement-security-considerations.md)。

## <a name="introduction"></a>介绍
本文介绍了 Azure 数据工厂中数据移动服务用于保护数据的基本安全基础结构。 Azure 数据工厂管理资源建立在 Azure 安全基础结构上，并使用 Azure 提供的所有可能的安全措施。

在数据工厂解决方案中，可以创建一个或多个数据[管道](data-factory-create-pipelines.md)。 “管道”是共同执行一项任务的活动的逻辑分组。 这些管道位于创建数据工厂的区域。 

尽管数据工厂只能在美国西部、美国东部和北欧区域使用，但数据移动服务可在[全球多个区域](data-factory-data-movement-activities.md#global)使用。 数据工厂服务确保数据不会离开某个地理区域，除非在数据移动服务尚未部署到该区域的情况下，明确指示服务使用备用区域。 

除使用证书加密的云数据存储的链接服务凭据外，Azure 数据工厂本身不存储任何其他数据。 它允许创建数据驱动的工作流，协调[受支持数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)之间的数据移动，以及使用[计算服务](data-factory-compute-linked-services.md)在其他区域或本地环境中处理数据。 它还允许使用编程方式及 UI 机制来 [监视和管理工作流](data-factory-monitor-manage-pipelines.md) 。

使用 Azure 数据工厂的数据移动已获得以下认证：
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
如果对 Azure 合规性以及 Azure 如何保护其专属基础结构感兴趣，请访问 [Microsoft 信任中心](https://www.microsoft.com/TrustCenter/default.aspx)。 

在本文中，我们将查看以下两个数据移动方案中的安全注意事项： 

- 云方案 - 在这种方案中，源和目标都可通过 Internet 公开访问。 其中包括托管的云存储服务（如 Azure 存储、Azure SQL 数据仓库、Azure SQL 数据库、Azure Data Lake Store、Amazon S3 和 Amazon Redshift）、SaaS 服务（如 Salesforce）以及 Web 协议（如 FTP 和 OData）。 可以在[这里](data-factory-data-movement-activities.md#supported-data-stores-and-formats)找到受支持数据源的完整列表。
- 混合方案 - 在这种方案中，源或目标位于防火墙之后或本地公司网络中，数据存储位于专用网络/虚拟网络（通常是源）中，且不可公开访问。 虚拟机上托管的数据库服务器也属于这种情况。

## <a name="cloud-scenarios"></a>云方案
###<a name="securing-data-store-credentials"></a>保护数据存储凭据
Azure 数据工厂使用由 Microsoft 管理的证书对数据存储凭据加密，从而为这些凭据提供保护。 这些证书每两年轮换一次（包括证书的续订和凭据的迁移）。 这些加密凭据安全地存储在由 Azure 数据工厂管理服务管理的 Azure 存储中。 有关 Azure 存储安全的详细信息，请参阅 [Azure 存储安全概述](../../security/security-storage-overview.md)。

### <a name="data-encryption-in-transit"></a>传输中的数据加密
如果云数据存储支持 HTTPS 或 TLS，则数据工厂中数据移动服务与云数据存储之间的所有数据传输均通过安全通道 HTTPS 或 TLS 进行。

> [!NOTE]
> 在与数据库相互传输数据时，与 Azure SQL 数据库和 Azure SQL 数据仓库的所有连接始终需要经过加密 (SSL/TLS)。 在使用 JSON 编辑器创作管道时，请在“连接字符串”中添加“加密”属性并将其设置为“true”。 使用[复制向导](data-factory-azure-copy-wizard.md)时，向导会默认设置此属性。 对于 Azure 存储，可以在连接字符串中使用“HTTPS”。

### <a name="data-encryption-at-rest"></a>静态数据加密
某些数据存储支持静态数据加密。 我们建议为这些数据存储启用数据加密机制。 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL 数据仓库
Azure SQL 数据仓库中的透明数据加密 (TDE) 可对静态数据执行实时加密和解密，从而帮助防止恶意活动的威胁。 此行为对客户端透明。 有关详细信息，请参阅[保护 SQL 数据仓库中的数据库](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)。

#### <a name="azure-sql-database"></a>Azure SQL 数据库
Azure SQL 数据库还支持透明数据加密 (TDE)，它无需更改应用程序，即可对数据执行实时加密和解密，从而帮助防止恶意活动的威胁。 此行为对客户端透明。 有关详细信息，请参阅[使用 Azure SQL 数据库进行透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)。 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store 还为存储在帐户中的数据提供加密。 启用后，Data Lake Store 会在保存数据前进行自动加密，在检索前进行自动解密，从而使其对访问数据的客户端透明。 有关详细信息，请参阅 [Azure Data Lake Store 中的安全性](../../data-lake-store/data-lake-store-security-overview.md)。 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob 存储和 Azure 表存储
Azure Blob 存储和 Azure 表存储支持存储服务加密 (SSE)，它会在将数据保存到存储中前进行自动加密，在检索前进行自动解密。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../../storage/common/storage-service-encryption.md)。

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 支持静态数据的客户端和服务器加密。 有关详细信息，请参阅[使用加密保护数据](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)。 目前，数据工厂不支持虚拟私有云 (VPC) 中的 Amazon S3。

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift 支持静态数据的群集加密。 有关详细信息，请参阅 [Amazon Redshift 数据库加密](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)。 目前，数据工厂不支持 VPC 中的 Amazon Redshift。 

#### <a name="salesforce"></a>Salesforce
Salesforce 支持防火墙平台加密，它允许加密所有文件、附件、自定义字段。 有关详细信息，请参阅 [Understanding the Web Server OAuth Authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)（了解 Web 服务器 OAuth 身份验证流）。  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>混合方案（使用数据管理网关）
混合方案需要在本地网络或虚拟网络 (Azure) 内或虚拟私有云 (Amazon) 中安装数据管理网关。 网关必须能访问本地数据存储。 有关网关的详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)。 

![数据管理网关通道](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

使用命令通道，可在数据工厂中的数据移动服务与数据管理网关之间进行通信。 通信包含与活动相关的信息。 数据信道用于在本地数据存储和云数据存储之间传输数据。    

### <a name="on-premises-data-store-credentials"></a>本地数据存储凭据
本地数据存储的凭据存储在本地（而不是在云中）。 可通过三种不同方式对其进行设置。 

- 从 Azure 门户/复制向导通过 HTTPS 使用纯文本（较不安全）。 凭据以纯文本形式传递到本地网关。
- 使用复制向导中的 JavaScript 加密库。
- 使用一键式凭据管理器应用。 一键式应用程序在有权访问网关的本地计算机上执行，并为数据存储设置凭据。 此选项与下一选项是最安全的选项。 默认情况下，凭据管理器应用在计算机上使用端口 8050，借助网关确保安全通信。  
- 使用 [New-AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) PowerShell cmdlet 加密凭据。 此 cmdlet 使用的证书是配置网关加密凭据所用的证书。 可以使用此 cmdlet 返回的加密凭据，并将其添加到 JSON 文件中 connectionString 的 EncryptedCredential 元素内（该文件将与 [New-AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) cmdlet 一起使用），或者将其添加到门户网站的数据工厂编辑器的 JSON 代码片段中。 此选项与一键式应用程序是最安全的选项。 

#### <a name="javascript-cryptography-library-based-encryption"></a>基于 JavaScript 加密库的加密
可以使用[复制向导](data-factory-copy-wizard.md)中的 [JavaScript 加密库](https://www.microsoft.com/download/details.aspx?id=52439)对数据存储凭据加密。 选择此选项时，复制向导将检索网关的公共密钥，并将其用于加密数据存储凭据。 凭据由网关计算机解密，并受 Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) 保护。

**支持的浏览器：**IE8、IE9、IE10、IE11、Microsoft Edge 以及最新的 Firefox、Chrome、Opera、Safari 浏览器。 

#### <a name="click-once-credentials-manager-app"></a>一键式凭据管理器应用
创作管道时，可以从 Azure 门户/复制向导启动一键式凭据管理器应用。 此应用程序确保凭据不会通过网络以纯文本形式传输。 默认情况下，该应用程序在计算机上使用端口 8050 ，借助网关确保安全通信。 如有必要，可以更改此端口。  
  
![网关的 HTTPS 端口](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

目前，数据管理网关使用单个证书。 此证书是在网关安装期间创建的（适用于在 2016 年 11 月之后创建的数据管理网关 2.4.xxxx.x 版本或更高上版本）。 可以使用自己的 SSL/TLS 证书替换此证书。 一键式凭据管理器应用程序使用此证书安全地连接到网关计算机，以便设置数据存储凭据。 它通过在具有网关的计算机上使用 Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) 在本地安全地存储数据存储凭据。 

> [!NOTE]
> 2016 年 11 月之前安装的旧网关或 2.3.xxxx.x 版本的网关继续使用云中存储的加密凭据。 即使将网关升级到最新版本，凭据也不会迁移到本地计算机上    
  
| 网关版本（创建期间） | 存储的凭据 | 凭据加密/安全 | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | 在云上 | 使用证书加密（不同于凭据管理器应用使用的证书） | 
| > = 2.4.xxxx.x | 本地 | 通过 DPAPI 保护 | 
  

### <a name="encryption-in-transit"></a>传输中加密
所有数据传输都是通过 HTTPS 和 TLS over TCP 安全通道进行的，可防止与 Azure 服务通信期间发生中间人攻击。
 
还可以使用 [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) 或 [快速路由](../../expressroute/expressroute-introduction.md) 进一步保护本地网络和 Azure 之间的通信信道。

虚拟网络是网络在云中的逻辑表示形式。 可以通过设置 IPSec VPN（站点到站点）或快速路由（私有对等互连）将本地网络连接到 Azure 虚拟网络 (VNet)     

下表基于混合数据移动的源和目标位置的不同组合，总结了相关的网络和网关配置建议。

| 源 | 目标 | 网络配置 | 网关设置 |
| ------ | ----------- | --------------------- | ------------- | 
| 本地 | 虚拟网络中部署的虚拟机和云服务 | IPSec VPN（点到站点或站点到站点） | 网关可以安装在本地或 VNet 中的 Azure 虚拟机 (VM) 上 | 
| 本地 | 虚拟网络中部署的虚拟机和云服务 | ExpressRoute（私有对等互连） | 网关可以安装在本地或 VNet 中的 Azure VM 上 | 
| 本地 | 具有公共终结点的基于 Azure 的服务 | ExpressRoute（公共对等互连） | 网关必须安装在本地 | 

下图显示了如何使用数据管理网关通过快速路由和 IPSec VPN（具有虚拟网络）在本地数据库和 Azure 服务之间移动数据：

快速路由：
 
![将快速路由与网关配合使用](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

IPSec VPN：

![将 IPSec VPN 与网关配合使用](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>网关的防火墙配置和白名单 IP 地址

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>本地/专用网络的防火墙要求  
在企业中，企业防火墙在组织的中央路由器上运行。 并且，Windows 防火墙在安装网关的本地计算机上作为守护程序运行。 

下表提供了企业防火墙的出站端口和域要求。

| 域名 | 出站端口 | 说明 |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | 用于将网关连接到数据工厂中的数据移动服务 |
| `*.core.windows.net` | 443 | 使用[暂存复制](data-factory-copy-activity-performance.md#staged-copy)功能时，由网关用于连接到 Azure 存储帐户。 | 
| `*.frontend.clouddatahub.net` | 443 | 用于将网关连接到 Azure 数据工厂服务。 | 
| `*.database.windows.net` | 1433   | （可选）目标为 Azure SQL 数据库/Azure SQL 数据仓库时需要。 在不打开端口 1433 的情况下，使用暂存复制功能将数据复制到 Azure SQL 数据库/Azure SQL 数据仓库。 | 
| `*.azuredatalakestore.net` | 443 | （可选）目标为 Azure Data Lake Store 时需要 | 

> [!NOTE] 
> 可能需要按相应数据源的要求在企业防火墙级别管理端口/白名单域。 此表仅以 Azure SQL 数据库、Azure SQL 数据仓库和 Azure Data Lake Store 为例。   

下表提供了 Windows 防火墙的入站端口要求。

| 入站端口 | 说明 | 
| ------------- | ----------- | 
| 8050 (TCP) | 由凭据管理器应用程序用于为网关上的本地数据存储安全地设置凭据。 | 

![网关端口要求](media\data-factory-data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-whitelisting-in-data-store"></a>数据存储中的 IP 配置/白名单
云中的某些数据存储还需要访问它们的计算机的 IP 地址白名单。 确保已在防火墙中将网关计算机的 IP 地址列入白名单或对其进行了适当配置。

以下云数据存储需要网关计算机的 IP 地址白名单。 默认情况下，某些这类数据存储可能不需要 IP 地址白名单。 

- [Azure SQL 数据库](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL 数据仓库](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>常见问题

问题：可否在不同的数据工厂之间共享网关？
答案：我们尚不支持此功能。 我们正致力于解决该问题。

问题：确保网关正常工作的端口要求是什么？
答案：网关建立基于 HTTP 的连接，以打开 Internet。 必须打开出站端口 443 和 80，网关才能建立此连接。 仅在计算机级别（不是企业防火墙级别）为凭据管理器应用程序打开入站端口 8050。 如果使用 Azure SQL 数据库或 Azure SQL 数据仓库作为源/目标，则还需要打开端口 1433。 有关详细信息，请参阅[防火墙配置和白名单 IP 地址](#firewall-configurations-and-whitelisting-ip-address-of gateway)部分。 

问题：网关的证书要求是什么？
答案：当前网关需要凭据管理器应用程序用于安全设置数据存储凭据的证书。 该证书是由网关安装程序创建并配置的自签名证书。 可以改用自己的 TLS/SSL 证书。 有关详细信息，请参阅[一键式凭据管理器应用程序](#click-once-credentials-manager-app)部分。 

## <a name="next-steps"></a>后续步骤
有关复制活动性能的信息，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

 
