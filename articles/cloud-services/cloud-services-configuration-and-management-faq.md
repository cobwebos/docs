---
title: "Microsoft Azure 云服务配置和管理常见问题解答 | Microsoft Docs"
description: "本文列出了一些关于 Microsoft Azure 云服务配置和管理的常见问题解答。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7d7676be26a8b68ab9fda18388e2b8cd5ed5f60b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017


---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 云服务配置和管理问题：常见问题解答 (FAQ)

本文包括一些关于 [Microsoft Azure 云服务](https://azure.microsoft.com/services/cloud-services)配置和管理的常见问题解答。 还可以参阅[云服务 VM 大小页面](cloud-services-sizes-specs.md)，了解大小信息。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>如何将“nosniff”添加到网站？
若要防止客户端探查 MIME 类型，请在 *web.config* 文件中添加设置。

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

也可以将此项作为 IIS 中的设置添加。 使用[常见启动任务](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)一文中的以下命令。

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>如何为 Web 角色自定义 IIS？
使用[常见启动任务](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)一文中的 IIS 启动脚本。

## <a name="i-cannot-scale-beyond-x-instances"></a>无法扩展到 X 个实例以上
Azure 订阅对可使用的核心数量有限制。 如果已使用所有可用的核心，将无法进行扩展。 例如，如果限制为 100 个核心，这意味着云服务可以有大小为 100 A1 的虚拟机实例或大小为 50 A2 的虚拟机实例。

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>如何为云服务实现基于角色的访问？
云服务不支持基于角色的访问控制 (RBAC) 模型，因为它不是基于 Azure 资源管理器的服务。

请参阅 [Azure RBAC 与经典订阅管理员](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators)。

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>如何为 Azure 负载均衡器设置空闲超时？
可以在服务定义 (csdef) 文件中按如下所示指定超时：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
请参阅[新增功能：Azure 负载均衡器可配置空闲超时](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/)，了解详细信息。

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>没有权限的 Microsoft 内部工程师是否可以通过 RDP 连接到云服务实例？
Microsoft 遵循严格的流程，不允许没有来自所有者或其被委派者的写入权限（电子邮件或其他书面通信）的内部工程师通过 RDP 连接到云服务。

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>为什么云服务 SSL 证书的证书链不完整？
建议客户安装完整的证书链（叶证书、中间证书和根证书），而不是仅安装叶证书。 如果仅安装叶证书，将依赖 Windows 通过遍历 CTL 生成证书链。 如果在 Windows 尝试验证证书时，Azure 或 Windows 更新中出现间歇性网络或 DNS 问题，证书可能会被视为无效。 通过安装完整的证书链可避免此问题。 博客[如何安装链接的 SSL 证书](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/)演示了如何执行此操作。

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>如何将静态 IP 地址关联到云服务？
若要设置静态 IP 地址，需要创建保留 IP。 该保留 IP 可以关联到新的云服务或现有部署。 请参阅以下文档了解详细信息：
* [如何创建保留 IP 地址](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [保留现有云服务的 IP 地址](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [将保留 IP 关联到新的云服务](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [将保留 IP 关联到正在运行的部署](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [使用服务配置文件将保留 IP 关联到云服务](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>什么是云服务配额限制？
请参阅[服务特定的限制](../azure-subscription-service-limits.md#subscription-limits)。

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>云服务 VM 上的驱动器为何显示可用磁盘空间不足？
这是预期行为，应不会导致任何应用程序问题。 Azure PaaS VM 中的 %uproot% 驱动器的日记功能启用后，基本上会占用文件通常所用空间的两倍。 但是请注意以下几点，从根本上看，此问题无足轻重。

%approot% 驱动器大小的计算方法是 <.cspkg 的大小 + 日记最大大小 + 备用可用空间> 或 1.5 GB 两者中较大的一个。 VM 的大小与此计算方法无关。 （VM 大小仅影响临时 C: 驱动器的大小） 

不支持写入 %approot% 驱动器。 如果要写入 Azure VM，必须在临时 LocalStorage 资源（或其他选项，例如 Blob 存储、Azure 文件等）中执行此操作。 因此 %approot% 文件夹的可用空间没有意义。 如果不确定应用程序是否要写入 %approot% 驱动器，可以让服务持续运行几天，然后比较“之前”和“之后”的大小。 

Azure 不会将任何内容写入 %approot% 驱动器。 从 .cspkg 创建 VHD 并将其装载到 Azure VM 后，可能写入此驱动器的唯一内容是你的应用程序。 

日记设置不可配置，因此无法将其关闭。

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure 基本 IPS/IDS 和 DDOS 有哪些功能？
Azure 的数据中心物理服务器中包含 IPS/IDS，可用于防御威胁。 此外，客户可以部署第三方安全解决方案，例如 Web 应用程序防火墙、网络防火墙、反恶意软件、入侵检测、防护系统 (IDS/IPS) 等。 有关详细信息，请参阅[保护数据和资产及遵守全局安全标准](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)。

Microsoft 持续监测服务器、网络和应用程序，以检测威胁。 Azure 的多元威胁管理方法使用入侵检测、分布式拒绝服务 (DDoS) 攻击防护、渗透测试、行为分析、异常检测和机器学习来不断加强其防护并降低风险。 适用于 Azure 的 Microsoft 反恶意软件可以保护 Azure 云服务和虚拟机。 你还可以选择部署第三方安全解决方案，例如 Web 应用程序防火墙、网络防火墙、反恶意软件、入侵检测和防护系统 (IDS/IPS) 等。

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>IIS 为何停止写入日志目录？
你用于写入日志目录的本地存储配额已用完。 若要修正此问题，可以执行以下三种操作之一：
* 为 IIS 启用诊断功能并将诊断定期移动到 blob 存储。
* 手动删除日志记录目录中的日志文件。
* 增加本地资源的配额限制。

有关详细信息，请参阅以下文档：
* [在 Azure 存储中存储和查看诊断数据](cloud-services-dotnet-diagnostics-storage.md)
* [IIS 日志停止写入云服务](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>“用于扩展的 Windows Azure 工具加密证书”有何用途？
每次将扩展添加到云服务中时，就会自动创建这些证书。 通常是 WAD 扩展或 RDP 扩展，但也可能是其他扩展，例如反恶意软件或日志收集器扩展。 这些证书仅用于加密和解密扩展的专用配置。 从不检查过期日期，因此证书是否过期无关紧要。 

可以忽略这些证书。 如果想要清理这些证书，可以尝试将他们全部删除。 如果尝试删除正在使用的证书，Azure 会引发错误。

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>如何在未“通过 RDP 连接”到实例的情况下生成证书签名请求 (CSR)？
请参阅以下指南文档：

>[获取证书以用于 Windows Azure 网站 (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

请注意，CSR 只是一个文本文件。 无需从最终使用此证书的计算机中创建它。 此文档针对应用服务编写，但是 CSR 创建是通用的，它也适用于云服务。

