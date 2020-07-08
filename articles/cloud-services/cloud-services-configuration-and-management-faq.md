---
title: 配置和管理问题常见问题解答
titleSuffix: Azure Cloud Services
description: 本文列出了一些关于 Microsoft Azure 云服务配置和管理的常见问题解答。
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: c418ed87bd74471ce8c2e8186bd6244eaf6f21de
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921580"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 云服务配置和管理问题：常见问题 (FAQ)

本文包括一些关于 [Microsoft Azure 云服务](https://azure.microsoft.com/services/cloud-services)配置和管理的常见问题解答。 还可以参阅[云服务 VM 大小页面](cloud-services-sizes-specs.md)，了解大小信息。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificates**

- [云服务 TLS/SSL 证书的证书链为何不完整？](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [“用于扩展的 Windows Azure 工具加密证书”有何用途？](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [如何在未“通过 RDP 连接”到实例的情况下生成证书签名请求 (CSR)？](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [我的云服务管理证书即将到期。如何续订？](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [如何自动安装主要 TLS/SSL 证书 (.pfx) 和中间证书 (.p7b)？](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [“用于 MachineKey 的 Microsoft Azure 服务管理”证书的用途是什么？](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**监视和日志记录**

- [Azure 门户中即将推出的可帮助管理和监视应用程序的云服务功能是什么？](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [IIS 为何停止写入日志目录？](#why-does-iis-stop-writing-to-the-log-directory)
- [如何为云服务启用 WAD 日志记录？](#how-do-i-enable-wad-logging-for-cloud-services)

**网络配置**

- [如何为 Azure 负载均衡器设置空闲超时？](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [如何将静态 IP 地址关联到云服务？](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Azure 基本 IPS/IDS 和 DDOS 有哪些功能？](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [如何在云服务 VM 上启用 HTTP/2？](#how-to-enable-http2-on-cloud-services-vm)

**权限**

- [没有权限的 Microsoft 内部工程师能否通过远程桌面连接到云服务实例？](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [无法使用 RDP 文件通过远程桌面连接到云服务 VM。收到以下错误：发生身份验证错误（代码：0x80004005）](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**缩放**

- [无法扩展到 X 个实例以上](#i-cannot-scale-beyond-x-instances)
- [如何基于内存指标配置自动缩放？](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**常规**

- [如何将“nosniff”添加到网站？](#how-do-i-add-nosniff-to-my-website)
- [如何为 Web 角色自定义 IIS？](#how-do-i-customize-iis-for-a-web-role)
- [什么是云服务配额限制？](#what-is-the-quota-limit-for-my-cloud-service)
- [云服务 VM 上的驱动器为何显示可用磁盘空间不足？](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [如何以自动化方法为云服务添加反恶意软件扩展？](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [如何为云服务启用服务器名称指示 (SNI)？](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [如何将标记添加到 Azure 云服务？](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure 门户不显示云服务的 SDK 版本。如何获取版本信息？](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [我想将云服务关闭几个月。如何降低云服务的计费成本，而不丢失 IP 地址？](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>证书

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>云服务 TLS/SSL 证书的证书链为何不完整？
    
我们建议客户安装完整的证书链（叶证书、中间证书和根证书），而不要只安装叶证书。 如果只是安装叶证书，则要依赖 Windows 通过遍历 CTL 来构建证书链。 当 Windows 尝试验证证书时，如果 Azure 或 Windows 更新中发生间歇性网络问题或 DNS 问题，可能会将该证书视为无效。 如果安装完整的证书链，则可避免此问题。 博客 [How to install a chained SSL certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/)（如何安装链接的 SSL 证书）中介绍了操作方法。

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>“用于扩展的 Windows Azure 工具加密证书”有何用途？

每次将扩展添加到云服务中时，就会自动创建这些证书。 在大多数情况下，这是 WAD 扩展或 RDP 扩展，但也可能是其他扩展，例如反恶意软件或日志收集器扩展。 这些证书仅用于加密和解密扩展的专用配置。 系统永远不会检查过期日期，因此，证书是否已过期并不重要。 

可以忽略这些证书。 如果想要清理证书，可以尝试删除所有证书。 如果你尝试删除正在使用的证书，Azure 将引发错误。

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>如何在不通过 RDP 连接到实例的情况下生成证书签名请求 (CSR)？

请参阅以下指导文档：

[获取证书以用于 Windows Azure 网站 (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR 只是一个文本文件。 无需从最终使用此证书的计算机中创建它。 尽管本文档是针对应用服务编写的，但 CSR 创建过程是通用的，同样适用于云服务。

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>我的云服务管理证书即将过期。 如何续订？

可使用以下 PowerShell 命令续订管理证书：

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

**AzurePublishSettingsFile** 将在 Azure 门户上的“订阅” > “管理证书”中创建新的管理证书。   新证书的名称类似于“[订阅名称]-[当前日期]-credentials”。

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>如何自动安装主要 TLS/SSL 证书 (.pfx) 和中间证书 (.p7b)？

可以使用启动脚本 (batch/cmd/PowerShell) 自动完成此任务，并将该启动脚本注册到服务定义文件中。 将启动脚本和证书（.p7b 文件）添加到启动脚本所在的同一目录中的项目文件夹。

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>“用于 MachineKey 的 Microsoft Azure 服务管理”证书的用途是什么？

此证书用于加密 Azure Web 角色的计算机密钥。 若要了解详细信息，请查看[此公告](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731)。

有关详细信息，请参阅以下文章：
- [如何配置和运行云服务的启动任务](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [常见的云服务启动任务](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>监视和日志记录

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Azure 门户中即将推出的可帮助管理和监视应用程序的云服务功能是什么？

即将推出为远程桌面协议 (RDP) 生成新证书的功能。 或者，可运行以下脚本：

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
选择 blob 或本地作为 csdef 和 cscfg 上传位置的功能即将推出。 使用 [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)，可以设置每个位置值。

能够监视实例级别的指标。 其他监视功能在[如何监视云服务](cloud-services-how-to-monitor.md)中提供。

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>IIS 为何阻止写入日志目录？
与写入日志目录相关的本地存储配额已用完。 若要解决此问题，可采取以下三种措施之一：
* 为 IIS 启用诊断，并定期将诊断信息移到 Blob 存储。
* 从日志目录中手动删除日志文件。
* 提高本地资源的配额限制。

有关详细信息，请参阅以下文档：
* [在 Azure 存储中存储和查看诊断数据](/azure/storage/common/storage-introduction)
* [IIS 日志停止写入到云服务中](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>如何为云服务启用 WAD 日志记录？
可以通过以下选项启用 Windows Azure 诊断 (WAD) 日志记录：
1. [从 Visual Studio 启用](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [通过 .NET 代码启用](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [通过 Powershell 启用](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

若要获取云服务的当前 WAD 设置，可以使用 [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd，也可以通过门户从“云服务 - > 扩展”边栏选项卡查看它。


## <a name="network-configuration"></a>网络配置

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>如何为 Azure 负载均衡器设置空闲超时？
可以在服务定义 (csdef) 文件中指定超时，如下所示：

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

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>如何将静态 IP 地址关联到云服务？
若要设置静态 IP 地址，需要创建保留 IP。 该保留 IP 可以关联到新的云服务或现有部署。 请参阅以下文档了解详细信息：
* [如何创建保留 IP 地址](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [保留现有云服务的 IP 地址](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [将保留 IP 关联到新的云服务](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [将保留 IP 关联到正在运行的部署](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [使用服务配置文件将保留 IP 关联到云服务](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure 基本 IPS/IDS 和 DDOS 提供哪些特性和功能？
Azure 在数据中心物理服务器上使用 IPS/IDS 来抵御威胁。 此外，客户可以部署第三方安全解决方案，例如 Web 应用程序防火墙、网络防火墙、反恶意软件、入侵检测、防护系统 (IDS/IPS)，等等。 有关详细信息，请参阅[保护数据和资产并遵守全局安全标准](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)。

Microsoft 会持续监视服务器、网络和应用程序以检测威胁。 Azure 的全方位威胁管理方法使用入侵检测、分布式拒绝服务 (DDoS) 攻击防护、渗透测试、行为分析、异常检测和机器学习来不断增强其防御能力并降低风险。 适用于 Azure 的 Microsoft 反恶意软件可以保护 Azure 云服务和虚拟机。 此外，你可以选择部署第三方安全解决方案，例如 Web 应用程序防火墙、网络防火墙、反恶意软件、入侵检测和防护系统 (IDS/IPS)，等等。

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>如何在云服务 VM 上启用 HTTP/2？

Windows 10 和 Windows Server 2016 随附了对客户端和服务器端上的 HTTP/2 的支持。 如果客户端（浏览器）通过 TLS（通过 TLS 扩展协商 HTTP/2）连接到 IIS 服务器，则不需要在服务器端进行任何更改。 这是因为，默认情况下会通过 TLS 来发送指定使用 HTTP/2 的 h2-14 标头。 如果在另一方面，客户端要发送升级标头以升级到 HTTP/2，则需要在服务器端进行以下更改，确保能够正常进行升级，并最终建立 HTTP/2 连接。 

1. 运行 regedit.exe。
2. 浏览到注册表项：HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters。
3. 创建名为 **DuoEnabled** 的新 DWORD 值。
4. 将其值设置为 1。
5. 重启服务器。
6. 转到“默认网站”，在“绑定”下，使用刚刚创建的自签名证书创建新的 TLS 绑定。   

有关详细信息，请参阅：

- [IIS 上的 HTTP/2](https://blogs.iis.net/davidso/http2)
- [视频：Windows 10 中的 HTTP/2：浏览器、应用和 Web 服务器](https://channel9.msdn.com/Events/Build/2015/3-88)
         

可通过启动任务自动完成这些步骤，这样，每次创建新的 PaaS 实例后，都可以在系统注册表中执行上述更改。 有关详细信息，请参阅[如何配置和运行云服务的启动任务](cloud-services-startup-tasks.md)。

 
完成此过程后，可使用以下方法之一验证是否已启用 HTTP/2：

- 在 IIS 日志中启用协议版本，并查看 IIS 日志。 日志中会显示 HTTP/2。 
- 在 Internet Explorer 或 Microsoft Edge 中启用 F12 开发人员工具，并切换到“网络”选项卡来验证协议。 

有关详细信息，请参阅 [IIS 上的 HTTP/2](https://blogs.iis.net/davidso/http2)。

## <a name="permissions"></a>权限

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>如何为云服务实现基于角色的访问？
云服务不支持基于角色的访问控制 (RBAC) 模型，因为它不是基于 Azure 资源管理器的服务。

请参阅[了解 Azure 中的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="remote-desktop"></a>远程桌面

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>没有权限的 Microsoft 内部工程师能否通过远程桌面连接到云服务实例？
Microsoft 遵循严格的流程，未经所有者或其被委派者书面许可（电子邮件或其他书面通信），不允许内部工程师通过远程桌面连接到云服务。

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>无法使用 RDP 文件通过远程桌面连接到云服务 VM。 收到以下错误：发生身份验证错误（代码：0x80004005）

如果在已加入 Azure Active Directory 的计算机上使用 RDP 文件，则可能会发生此错误。 若要解决此问题，请执行以下步骤：

1. 右键单击下载的 RDP 文件，然后选择“编辑”。 
2. 在用户名的前面添加“&#92;”前缀。 例如，使用 **.\username** 而不要使用 **username**。

## <a name="scaling"></a>扩展

### <a name="i-cannot-scale-beyond-x-instances"></a>无法扩展到 X 个实例以上
Azure 订阅对可以使用的内核数存在限制。 如果已使用所有可用的内核，缩放无法执行。 例如，如果限制为 100 个核心，则意味着云服务可以有 100 个 A1 大小的虚拟机实例或 50 个 A2 大小的虚拟机实例。

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>如何基于内存指标配置自动缩放？

当前不支持基于内存指标为云服务配置自动缩放。 

若要解决此问题，可以使用 Application Insights。 自动缩放支持将 Application Insights 作为指标源，可以基于“内存”等来宾指标缩放角色实例计数。  必须在云服务项目包文件 (*.cspkg) 中配置 Application Insights 并对该服务启用 Azure 诊断扩展，才能实现此功能。

有关如何在云服务上通过 Application Insights 利用自定义指标来配置自动缩放的更多详细信息，请参阅[在 Azure 中根据自定义指标自动缩放入门](../azure-monitor/platform/autoscale-custom-metric.md)

有关如何针对云服务将 Azure 诊断与 Application Insights 集成的详细信息，请参阅[将云服务、虚拟机或 Service Fabric 诊断数据发送到 Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

有关如何为云服务启用 Application Insights 的详细信息，请参阅[适用于 Azure 云服务的 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

有关如何为云服务启用 Azure 诊断日志记录的详细信息，请参阅[为 Azure 云服务和虚拟机设置诊断](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>泛型

### <a name="how-do-i-add-nosniff-to-my-website"></a>如何将“nosniff”添加到网站？
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

### <a name="how-do-i-customize-iis-for-a-web-role"></a>如何自定义 Web 角色的 IIS
使用[常见启动任务](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)一文中的 IIS 启动脚本。

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>什么是云服务配额限制？
请参阅[服务特定的限制](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>云服务 VM 上的驱动器为何显示可用磁盘空间不足？
这是预期的行为，不会导致应用程序出现任何问题。 为 Azure PaaS VM 中的 %approot% 驱动器启用了日记，因此，占用的空间量在实际上是文件平时占用的空间量的两倍。 但是，有几个因素会在本质上消除此状态造成的问题。

% Approot% 驱动器的大小计算为 \<size of .cspkg + max journal size + a margin of free space> 或 1.5 GB，取两者中较大者。 VM 大小对于计算结果没有任何影响。 （VM 大小只会影响临时 C: 驱动器的大小。） 

不支持写入 %approot% 驱动器。 如果要写入 Azure VM，必须在临时 LocalStorage 资源中执行此操作（或使用其他选项，例如 Blob 存储、Azure 文件，等等）。 因此，%approot% 文件夹的可用空间量没有意义。 如果不确定应用程序是否写入 %approot% 驱动器，始终可以让服务运行数日，然后比较“之前”和“之后”的大小。 

Azure 不会将任何数据写入 %approot% 驱动器。 从 .cspkg 创建 VHD 并将其装载到 Azure VM 之后，就只有你的应用程序可能会写入此驱动器。 

日记设置不可配置，因此无法将其禁用。

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>如何以自动化方法为云服务添加反恶意软件扩展？

可在启动任务中使用 PowerShell 脚本启用反恶意软件扩展。 请遵循以下文章中的步骤实现此目的： 
 
- [创建 PowerShell 启动任务](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

有关反恶意软件部署方案以及如何在门户中启用此类方案的详细信息，请参阅[反恶意软件部署方案](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)。

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>如何为云服务启用服务器名称指示 (SNI)？

可使用以下方法之一在云服务中启用 SNI：

**方法 1：使用 PowerShell**

可在启动任务中使用 PowerShell cmdlet **New-WebBinding** 为云服务角色实例配置 SNI 绑定，如下所示：

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

如[此文](https://technet.microsoft.com/library/ee790567.aspx)所述，$sslFlags 可为以下值之一：

|值|含义|
------|------
|0|没有 SNI|
|1|已启用 SNI|
|2|使用中心证书存储的非 SNI 绑定|
|3|使用中心证书存储的 SNI 绑定|
 
**方法 2：使用代码**

也可以根据此[博客文章](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)中所述，通过角色启动中的代码配置 SNI 绑定：

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
使用上述任一方法时，必须先使用启动任务或通过代码在角色实例上安装特定主机名的相应证书 (*.pfx)，这样才能使 SNI 绑定生效。

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>如何将标记添加到 Azure 云服务？ 

云服务是一个经典资源。 只有通过 Azure 资源管理器创建的资源才支持标记。 无法将标记应用到云服务等经典资源。 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure 门户不显示我的云服务的 SDK 版本。 如何查看版本？

我们正努力在 Azure 门户中实现此功能。 在此同时，你可以使用以下 PowerShell 命令获取 SDK 版本：

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>我想要关闭云服务几个月。 如何在不丢失 IP 地址的情况下降低云服务的计费成本？

已部署的云服务使用的计算和存储资源会产生费用。 因此，即使关闭 Azure VM，也仍要支付存储费用。 

下面是可以在不丢失服务 IP 地址的情况下节省费用的办法：

1. 在删除部署之前[保留 IP 地址](../virtual-network/virtual-networks-reserved-public-ip.md)。  这样，就只需支付此 IP 地址的费用。 有关 IP 地址计费的详细信息，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。
2. 删除部署。 不要删除 xxx.cloudapp.net，以供将来使用。
3. 如果想要使用订阅中的相同保留 IP 来重新部署云服务，请参阅[云服务和虚拟机的保留 IP 地址](https://azure.microsoft.com/blog/reserved-ip-addresses/)。

