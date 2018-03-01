---
title: "Azure 堆栈的 azure 堆栈公钥基础结构证书要求集成系统 |Microsoft 文档"
description: "介绍 Azure 堆栈集成系统的 Azure 堆栈 PKI 证书部署要求。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: f2f71372211dcc9db34beb3fa3fd788920f8bd45
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure 堆栈公钥基础结构证书要求
Azure 堆栈具有使用从外部访问公共 IP 地址分配给 Azure 堆栈服务和可能的租户 Vm 的一小部分的公共基础结构网络。 在 Azure 堆栈部署过程中，使用合适的 DNS 名称，这些 Azure 堆栈公共基础结构终结点的 PKI 证书是必需的。 本文提供以下信息：

- 哪些证书所需部署 Azure 堆栈
- 获取匹配这些规范的证书的过程
- 如何准备、 验证和在部署过程中使用这些证书

> [!NOTE]
> 在部署过程必须将证书复制到与匹配标识提供程序 (Azure AD 或 AD FS) 对要部署的部署文件夹。 如果对所有终结点使用一个证书，你必须将该证书文件复制到每个部署文件夹下表中所述。 文件夹结构中部署虚拟机预先生成和处找不： C:\CloudDeployment\Setup\Certificates。 

## <a name="certificate-requirements"></a>证书要求
下面介绍了部署 Azure 堆栈所需的证书要求： 
- 必须从内部证书颁发机构或公共证书颁发机构颁发证书。 如果使用公用证书颁发机构，则它必须包含在作为 Microsoft 受信任的根颁发机构计划的一部分的基本操作系统映像中。 你可以找到完整的列表： https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- 证书可以是涵盖使用者备用名称 (SAN) 字段中的所有命名空间的单个通配符证书。 或者，你可以使用的终结点，例如 acs 和它们所需的密钥保管库中使用通配符的单个证书。 
- 证书签名算法不能为 SHA1，因为它必须是更强。 
- 证书格式必须为 PFX，因为公钥和私钥密钥 Azure 堆栈安装所必需的。 
- 证书 pfx 文件必须具有值"数字签名"和"KeyEncipherment"与其"密钥用法"字段中。
- 证书 pfx 文件必须具有"增强型密钥用法"字段中的"服务器身份验证 (1.3.6.1.5.5.7.3.1)"和"客户端身份验证 (1.3.6.1.5.5.7.3.2)"的值。
- 对所有的证书 pfx 文件密码必须是部署的相同时
- 请确保使用者名称和的所有证书的使用者备用名称匹配本文以避免失败的部署中所述的规范。

> [!NOTE]
> 支持的中间证书颁发机构证书的链信任 IS 中存在。 

## <a name="mandatory-certificates"></a>必需的证书
本部分中的表描述这两个 Azure ad 所需的 Azure 堆栈公共终结点 PKI 证书和 AD FS Azure 堆栈部署。 证书要求进行分组的区域中，以及使用的命名空间，并且每个命名空间要求的证书进行。 表还描述你的解决方案提供商将在其中复制每个公共终结点不同的证书的文件夹。 

使用合适的 DNS 名称，每个 Azure 堆栈公共基础结构终结点的证书是必需的。 每个终结点的 DNS 名称以格式表示： *&lt;前缀 >。&lt;区域 >。&lt;fqdn >*。 

为你的部署，[region] 和 [externalfqdn] 值必须匹配的区域和 Azure 堆栈系统为所选的外部域名。 例如，如果区域名称*Redmond*的外部域名， *contoso.com*，DNS 名称将具有格式*&lt;前缀 >。 redmond.contoso.com*.*&lt;前缀 >*值 predesignated 由 Microsoft 用于描述由证书保护终结点。 此外， *&lt;前缀 >*的外部的基础结构终结点的值取决于使用特定终结点 Azure Stack 服务。 

|部署文件夹|所需的证书使用者和使用者可选名称 (SAN)|（每个区域） 的作用域|子域命名空间|
|-----|-----|-----|-----|
|公共门户|portal.*&lt;region>.&lt;fqdn>*|门户|*&lt;region>.&lt;fqdn>*|
|管理门户|adminportal.*&lt;region>.&lt;fqdn>*|门户|*&lt;region>.&lt;fqdn>*|
|Azure Resource Manager Public|management.*&lt;region>.&lt;fqdn>*|Azure 资源管理器|*&lt;region>.&lt;fqdn>*|
|Azure 资源管理器管理|adminmanagement.*&lt;region>.&lt;fqdn>*|Azure 资源管理器|*&lt;region>.&lt;fqdn>*|
|ACS<sup>1</sup>|使用者可选名称与一个多子域的通配符证书：<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.table.*&lt;region>.&lt;fqdn>*|存储|blob.*&lt;region>.&lt;fqdn>*<br>table.*&lt;region>.&lt;fqdn>*<br>queue.*&lt;region>.&lt;fqdn>*|
|KeyVault|&#42;.vault.*&lt;region>.&lt;fqdn>*<br>（通配型 SSL 证书）|Key Vault|vault.*&lt;region>.&lt;fqdn>*|
|KeyVaultInternal|&#42;.adminvault.*&lt;region>.&lt;fqdn>*<br>（通配型 SSL 证书）|内部 Keyvault|adminvault.*&lt;region>.&lt;fqdn>*|
|
<sup>1</sup> ACS 证书需要单个证书上的三个通配符 San。 所有公共证书颁发机构可能不支持 San 单个证书上的多个通配符。 

如果你部署 Azure 堆栈使用 Azure AD 部署模式，你只需请求在上表中列出的证书。 但是，如果你部署 Azure 堆栈使用 AD FS 部署模式，你还必须请求下表中所述的证书：

|部署文件夹|所需的证书使用者和使用者可选名称 (SAN)|（每个区域） 的作用域|子域命名空间|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>（SSL 证书）|ADFS|*&lt;region>.&lt;fqdn>*|
|图形|graph.*&lt;region>.&lt;fqdn>*<br>（SSL 证书）|图形|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> 本部分中列出的所有证书必须都具有相同的密码。 

## <a name="optional-paas-certificates"></a>可选的 PaaS 证书
如果你计划要在 Azure 堆栈后部署其他 Azure 堆栈 PaaS 服务 （SQL、 MySQL 和 App Service） 已部署并配置，你将需要请求要涵盖 PaaS 服务的终结点的其他证书。 

> [!IMPORTANT]
> App Service、 SQL 和 MySQL 资源提供程序使用的证书需要具有与全局 Azure 堆栈终结点使用相同的根颁发机构。 

下表描述的终结点和 SQL 和 MySQL 适配器和 App Service 所需证书。 你不需要将这些证书复制到 Azure 堆栈部署文件夹。 相反，当你安装的其他资源提供程序时提供这些证书。 

|（每个区域） 的作用域|证书|所需的证书使用者和使用者备用名称 (San)|子域命名空间|
|-----|-----|-----|-----|
|SQL、 MySQL|SQL 和 MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>（通配型 SSL 证书）|dbadapter.*&lt;region>.&lt;fqdn>*|
|应用服务|Web 流量默认 SSL 证书|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>(多域通配型 SSL 证书<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|应用服务|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|应用服务|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|应用服务|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup>需要使用多个通配符使用者可选名称的一个证书。 所有公共证书颁发机构可能不支持 San 单个证书上的多个通配符 

<sup>2</sup> A &#42;。appservice。*&lt;区域 >。&lt;fqdn >*通配符证书不能代替这些三个证书 (api.appservice。*&lt;区域 >。&lt;fqdn >*，ftp.appservice。*&lt;区域 >。&lt;fqdn >*，和 sso.appservice。*&lt;区域 >。&lt;fqdn >*。 Appservice 显式这些终结点需要使用单独的证书。 

## <a name="learn-more"></a>了解详细信息
了解如何[生成 Azure 堆栈部署的 PKI 证书](azure-stack-get-pki-certs.md)。 

## <a name="next-steps"></a>后续步骤
[标识集成](azure-stack-integrate-identity.md)

