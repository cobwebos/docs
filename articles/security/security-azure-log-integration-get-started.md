---
title: "Azure 日志集成入门 | Microsoft Docs"
description: "了解如何安装 Azure 日志集成服务并集成来自 Azure 存储、Azure 审核日志和 Azure 安全中心警报的日志。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: aaa69e2e4fed314e8bc363f60e7538b12bb3a56d
ms.openlocfilehash: ca7f05534113752f3607268c15a9fe3e0e2982e0


---
# <a name="get-started-with-azure-log-integration-preview"></a>Azure 日志集成入门（预览版）
Azure 日志集成使你能够将原始日志从你的 Azure 资源集成到你的本地安全信息和事件管理 (SIEM) 系统。 此集成为本地或云中的所有资产提供统一的仪表板，以便聚合、相互关联、分析和警示与应用程序相关的安全事件。

本教程将引导你完成 Azure 日志集成的安装并集成来自 Azure 存储、Azure 审核日志和 Azure 安全中心警报的日志。 本教程预计完成时间为&1; 小时。

## <a name="prerequisites"></a>先决条件
若要完成本教程，你必须准备好以下各项：

* 一台用于安装 Azure 日志集成服务的计算机（位于本地或在云中）。 此计算机必须运行已安装 .Net 4.5.1 的 64 位 Windows 操作系统。 此计算机称为 **Azlog 集成器**。
* Azure 订阅。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/free/)。
* 为 Azure 虚拟机 (VM) 启用的 Azure 诊断。 若要为云服务启用诊断，请参阅[在 Azure 云服务中启用 Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)。 若要为运行 Windows 的 Azure VM 启用诊断，请参阅[在运行 Windows 的虚拟机中使用 PowerShell 启用 Azure 诊断](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 从 Azlog 集成器到 Azure 存储的连接以及用于进行身份验证和授权给 Azure 订阅的连接。
* 对于 Azure VM 日志，必须在 Azlog 集成器上安装 SIEM 代理（例如，Splunk Universal Forwarder、HP ArcSight Windows Event Collector 代理或 IBM QRadar WinCollect）。

## <a name="deployment-considerations"></a>部署注意事项
如果事件量很高，则可以运行 Azlog 集成器的多个实例。 Windows *(WAD)* 的 Azure 诊断存储帐户的负载平衡以及用于提供给实例的订阅数应该基于你的容量。

在一台 8 处理器（内核）的计算机上，Azlog 集成器的单个实例每天可以处理大约 2400 万个事件（每小时处理大约 100 万个事件）。

在一台 ４ 处理器（内核）的计算机上，Azlog 集成器的单个实例每天可以处理大约 150 万个事件（每小时处理大约 6.25 万个事件）。

## <a name="install-azure-log-integration"></a>安装 Azure 日志集成
下载 [Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324)。

Azure 日志集成服务从安装它的计算机中收集遥测数据。  收集的遥测数据包括：

* 在 Azure 日志集成执行过程中发生的异常
* 关于已处理的查询数量和事件数量的指标
* 关于正在使用哪些 Azlog.exe 命令行选项的统计信息

> [!NOTE]
> 通过取消选中此选项，可以关闭遥测数据的收集。
>
>


## <a name="set-your-azure-environment"></a>设置 Azure 环境
1. 以管理员身份打开 PowerShell 控制台并使用 **cd** 命令进入 **C:\Program Files\Microsoft Azure Log Integration**。
2. 运行命令     Set-AzLogAzureEnvironment -Name <Cloud>

       Replace the Cloud with any of the following
       AzureCloud
       AzureChinaCloud
       AzureUSGovernment
       AzureGermanCloud

       Note that at this time, an Azlog integrator only supports integrating logs from one cloud that you choose to integrate.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>从 Azure 诊断存储帐户集成 Azure VM 日志
1. 检查上面列出的先决条件，确保 WAD 存储帐户正在收集日志，然后才能继续进行 Azure 日志集成。 如果 WAD 存储帐户未在收集日志，请不要执行下面的步骤。
2. 打开命令提示符并键入 **cd** **c:\Program Files\Microsoft Azure Log Integration**。
3. 运行命令

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      将 StorageAccountName 替换为配置用于接收来自 VM 的诊断事件的 Azure 存储帐户名称。

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      如果希望将订阅 ID 显示在事件 XML 中，请将订阅 ID 追加到友好名称中：

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>
4. 等待 30~60 分钟（可能需要长达 1 小时的时间），然后查看从存储帐户拉取的事件。 若要查看，请在 Azlog 集成器上打开“**事件查看器 > Windows 日志 > 已转发事件**”。
5. 请确保将计算机上安装的标准 SIEM 连接器配置为从“**已转发事件**”文件夹拾取事件并将事件发送给 SIEM 实例。 审阅 SIEM 特定配置以配置和查看日志集成。

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>如果在“已转发事件”文件夹中未显示数据，该怎么办？
如果在&1; 小时之后，在“**已转发事件**”文件夹中仍未显示数据，请执行以下操作：

1. 检查计算机并确认它可以访问 Azure。 若要测试连接，请尝试从浏览器打开 [Azure 门户](http://portal.azure.com)。
2. 请确保用户帐户 **azlog** 具有对文件夹 **users\azlog** 的写入权限。
3. 请确保在运行命令 **azlog source list** 时，会列出通过命令 **azlog source add** 添加的存储帐户。
4. 转到“**事件查看器 > Windows 日志 > 应用程序**”以查看是否存在从 Azure 日志集成报告的任何错误。

如果仍未看到事件，请执行以下操作：

1. 下载 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)。
2. 连接到通过命令 **azlog source add** 添加的存储帐户。
3. 在 Microsoft Azure 存储资源管理器中，浏览到表 **WADWindowsEventLogsTable**，以查看是否存在任何数据。 如果没有数据，说明 VM 中的诊断未正确配置。

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>集成 Azure 审核日志和安全中心警报
1. 打开命令提示符并键入 **cd** **c:\Program Files\Microsoft Azure Log Integration**。
2. 运行命令

        azlog createazureid

      此命令提示你登录 Azure。 此命令接着会在 Azure AD Tenant 中创建一个 [Azure Active Directory Service Principal](../active-directory/active-directory-application-objects.md)，这些 Azure AD Tenant 会托管 Azure 订阅，而在这些 Azure 订阅中，已登录的用户是管理员、协同管理员或所有者。 如果已登录的用户仅仅是 Azure AD Tenant 中的一名来宾用户，那么此命令将失败。 对 Azure 的身份验证是通过 Azure Active Directory (AD) 完成的。  为 Azlog 集成创建服务主体会创建将被赋予从 Azure 订阅的读取权限的 Azure AD ID。
3. 运行命令

        azlog authorize <SubscriptionID>

      此操作会将对订阅的读取器访问权限分配给在步骤 2 中创建的服务主体。 如果未指定 SubscriptionID，那么此操作会尝试将服务主体读取器角色分配给你有权访问的所有订阅。

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

   > [!NOTE]
   > 在运行 **createazureid** 命令之后立即运行 **authorize** 命令，可能会出现警告。 Azure AD 帐户在创建之后，需要经过一段延迟时间才能使用。 如果在运行 **createazureid** 命令之后，等待大约 10 秒再运行 **authorize** 命令，则不会出现警告。
   >
   >
4. 检查以下文件夹以确认其中是否存在审核日志 JSON 文件：

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. 检查以下文件夹以确认其中是否存在安全中心警报：

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. 将标准 SIEM 文件转发器连接器指向相应的文件夹，以将数据发送给 SIEM 实例。 根据所使用的 SIEM 产品，你可能需要某些字段映射。

如果存在关于 Azure 日志集成的问题，请发送电子邮件至 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>后续步骤
在本教程中，你学习了如何安装 Azure 日志集成以及如何从 Azure 存储集成日志。 若要了解更多信息，请参阅下列文章：

* [Microsoft Azure Log Integration for Azure logs (Preview)](https://www.microsoft.com/download/details.aspx?id=53324)（适用于 Azure 日志的 Microsoft Azure 日志集成（预览版））– 有关 Azure 日志集成的详细信息、系统要求和安装说明的下载中心。
* [Azure 日志集成简介](security-azure-log-integration-overview.md)– 本文档介绍 Azure 日志集成、其主要功能及其工作原理。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) - 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [集成安全中心警报与 Azure 日志集成](../security-center/security-center-integrating-alerts-with-log-integration.md) - 本文档介绍如何将安全中心警报以及由 Azure 诊断和 Azure 审核日志收集的虚拟机安全事件与你的日志分析或 SIEM 解决方案同步。
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)（Azure 诊断和 Azure 审核日志的新功能）– 此博客文章介绍 Azure 审核日志和其他功能，可帮助你深入了解 Azure 资源的操作。



<!--HONumber=Jan17_HO4-->


