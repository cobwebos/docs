---
title: "Azure 堆栈数据中心集成的 DNS"
description: "了解如何将与你的数据中心 DNS 集成 Azure 堆栈 DNS"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 5bdac2f3e6082f9449800fe2d4b303e2d59ade46
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---dns"></a>Azure 堆栈数据中心集成的 DNS
若要能够访问 Azure 堆栈终结点 (`portal`， `adminportal`， `management`，`adminmanagement`等。) 从外部 Azure 堆栈，你需要与托管你想要使用 Azure 堆栈中的 DNS 区域的 DNS 服务器集成的 Azure 堆栈 DNS 服务。

## <a name="azure-stack-dns-namespace"></a>Azure 堆栈 DNS 命名空间
将要求你将提供在部署 Azure 堆栈时，与 DNS 相关的一些重要信息。


|字段  |说明  |示例|
|---------|---------|---------|
|区域|你的 Azure 堆栈部署地理位置。|`east`|
|外部域名|你想要用于 Azure 堆栈部署区域的名称。|`cloud.fabrikam.com`|
|内部域名|用于 Azure 堆栈中的基础结构服务的内部区域的名称。  它是目录服务集成和专用 （不可从外部 Azure 堆栈部署）。|`azurestack.local`|
|DNS 转发器|用于将转发 DNS 查询、 DNS 区域和外 Azure 堆栈，在公司 intranet 或公共 internet 上托管的记录的 DNS 服务器。|`10.57.175.34`<br>`8.8.8.8`|
|（可选） 命名前缀|你想你 Azure 堆栈基础结构角色实例计算机名称具有命名前缀。  如果不提供，默认值是`azs`。|`azs`|

Azure 堆栈部署和终结点的完全限定的域名 (FQDN) 是区域参数和外部域 Name 参数的组合。 使用上表中的示例中的值，此 Azure 堆栈部署的 FQDN 将以下名称：

`east.cloud.fabrikam.com`

在这种情况下，一些为此部署的终结点的示例将如下所示的以下 Url:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

若要为 Azure 堆栈部署使用此示例 DNS 命名空间，都需要以下条件：

- 区域`fabrikam.com`通过域注册机构，内部公司 DNS 服务器，或两者，具体取决于你的名称解析要求注册。
- 子域`cloud.fabrikam.com`在该区域下存在`fabrikam.com`。
- 承载区域的 DNS 服务器`fabrikam.com`和`cloud.fabrikam.com`可以从 Azure 堆栈部署访问。

若要能够解析 Azure 堆栈终结点和从外部 Azure 堆栈的实例的 DNS 名称，你需要集成为 Azure 堆栈的外部的 DNS 区域托管 DNS 服务器托管你想要使用该父区域的 DNS 服务器。


## <a name="resolution-and-delegation"></a>解析和委托

有两种类型的 DNS 服务器：

- 权威 DNS 服务器托管 DNS 区域。 它只应答这些区域中的 DNS 记录查询。
- 递归 DNS 服务器未承载 DNS 区域。 它调用权威 DNS 服务器来收集所需的数据，以应答所有 DNS 查询。

Azure 堆栈权威同时包含和递归 DNS 服务器。 递归服务器用于解析所有内容的内部专用区域和外部的公共 DNS 区域除外的该 Azure 堆栈部署的名称，以及。 

![Azure 堆栈 DNS 体系结构](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>从 Azure 堆栈的外部 DNS 名称解析

若要解决 Azure 堆栈外部终结点的 DNS 名称 (例如： www.bing.com)，你需要提供 Azure 堆栈可用于为其 Azure 堆栈不是权威的 DNS 请求转发的 DNS 服务器。 对于部署，Azure 堆栈将请求转发到的 DNS 服务器 （中的 DNS 转发器字段中） 的部署工作表中需要。 提供此字段中的至少两个服务器以容错能力。 无需这些值，Azure 堆栈部署将失败。

### <a name="configure-conditional-dns-forwarding"></a>配置条件 DNS 转发

> [!IMPORTANT]
> 这仅适用于 AD FS 部署。

若要启用与现有 DNS 基础结构的名称解析，配置条件转发。

若要添加一个条件转发器，必须使用特权终结点。

对于此过程，在数据中心网络，可与 Azure 堆栈中的特权终结点进行通信中使用的计算机。

1. 打开提升的 Windows PowerShell 会话 （以管理员身份运行），并连接到特权终结点的 IP 地址。 使用凭据进行 CloudAdmin 身份验证。

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. 连接到特权终结点后，运行以下 PowerShell 命令。 替换提供具有域用户名和你想要使用的 DNS 服务器的 IP 地址的示例值。

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>从外部 Azure 堆栈的 Azure 堆栈 DNS 名称解析
权威服务器是那些保存外部的 DNS 区域信息，以及任何用户创建的区域。 将与这些服务器以启用区域委派或条件转发来解析 Azure 堆栈 DNS 名称，从外部 Azure 堆栈启用集成。

## <a name="get-dns-server-external-endpoint-information"></a>获取 DNS 服务器的外部终结点信息

要与你的 DNS 基础结构集成 Azure 堆栈部署，你需要以下信息：

- DNS 服务器 Fqdn
- DNS 服务器 IP 地址

Azure 堆栈 DNS 服务器的 Fqdn 具有以下格式：

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

是使用的采样值的 Fqdn，使 DNS 服务器：

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


此信息还创建名为的文件中的所有 Azure 堆栈部署末尾`AzureStackStampDeploymentInfo.json`。 此文件位于`C:\CloudDeployment\logs`部署虚拟机的文件夹。 如果你不确定哪些值已用于 Azure 堆栈部署，你可以从此处获取的值。

如果部署虚拟机不再可用或不可访问，你可以通过连接到特权终结点并运行中获取值`Get-AzureStackInfo`PowerShell cmdlet。 有关特权的终结点的详细信息，请参阅 （插入到此处文章的链接）。

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>设置条件转发到 Azure 堆栈

与你的 DNS 基础结构集成 Azure 堆栈的最简单且最安全方法是从承载父区域的服务器执行操作的区域的条件转发。 如果你的 Azure 堆栈外部 DNS 命名空间可以直接控制的 DNS 服务器承载父区域，建议使用此方法。

如果你不熟悉如何执行 dns 条件转发，请参阅以下 TechNet 文章：[域名的分配一个条件转发器](https://technet.microsoft.com/library/cc794735)，或特定于你的 DNS 解决方案的文档。

在其中指定你外部 Azure 堆栈 DNS 区域为类似于你公司的域的名称的子域的情况下，不能用于条件转发。 必须配置 DNS 委派。

例如:

- 企业 DNS 域名： `contoso.com`
- Azure 堆栈外部的 DNS 域名： `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>委托到 Azure 堆栈的外部 DNS 区域

对于要从 Azure 堆栈部署外部解析的 DNS 名称，你需要设置 DNS 委派。

每个注册机构都有自身的 DNS 管理工具，可以更改域的名称服务器记录。 在注册机构的 DNS 管理页中，编辑 NS 记录并将替换为 Azure 堆栈中的区域的 NS 记录。

大部分 DNS 注册机构要求你提供最少两个 DNS 服务器以完成委派。

## <a name="next-steps"></a>接下来的步骤

[防火墙集成](azure-stack-firewall.md)
