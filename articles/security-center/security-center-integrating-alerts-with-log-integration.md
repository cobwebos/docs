---
title: "集成 Azure 安全中心警报和 Azure 日志集成（预览版）| Microsoft Docs"
description: "本文有助于开始集成安全中心警报和 Azure 日志集成。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: f946ef41f0fc5ab39351d85c37ed24715b92b882
ms.openlocfilehash: cd58f24c356e6d1ec0ebe77b1c336ee8370a4a9a


---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration-preview"></a>集成 Azure 安全中心警报和 Azure 日志集成（预览版）
许多安全操作和事件响应团队依靠安全信息和事件管理 (SIEM) 解决方案作为会审和调查安全警报的起始点。 使用 Azure 日志集成，客户可以近实时地同步通过 Azure 诊断和 Azure 审核日志收集的安全中心警报和虚拟机安全事件，以及其日志分析或 SIEM 解决方案。

Azure 日志集成适用于 HP ArcSight、Splunk、IBM QRadar 和其他类型。

## <a name="what-logs-can-i-integrate"></a>可以集成哪些日志？
Azure 针对每个服务生成大量日志记录。 这些日志分类如下：

* **控制/管理日志**，使 Azure 资源管理器的 CREATE、UPDATE 和 DELETE 操作可见。
* **数据平面日志**，可让使用 Azure 资源时引发的事件可见。 其中一个示例是 Windows 事件日志，即虚拟机中的安全和应用程序日志。

Azure 日志集成当前支持以下集成：

* Azure VM 日志
* Azure 审核日志
* Azure 安全中心警报

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

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>集成 Azure 审核日志和安全中心警报
1. 打开命令提示符并键入 **cd** **c:\Program Files\Microsoft Azure Log Integration**。
2. 运行 **azlog createazureid** 命令，以在承载 Azure 订阅的 Azure Active Directory (AD) 租户中创建 [Azure Active Directory 服务主体](../active-directory/active-directory-application-objects.md)。

    系统会提示进行 Azure 登录。

   > [!NOTE]
   > 必须是订阅所有者或订阅的协同管理员。
   >
   >

    通过 Azure AD 完成 Azure 身份验证。  为 Azure 日志集成创建服务主体会创建 Azure AD 标识，后者已获得相关访问权限，可以从 Azure 订阅进行读取。
3. 运行 **azlog authorize <SubscriptionID>** 命令将订阅的读取者访问权限分配给步骤 2 中创建的服务主体。 如未指定 **SubscriptionID**，系统会为服务主体分配针对用户有权访问的所有订阅的读取者角色。

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
6. 将标准 SIEM 文件转发器连接器指向相应的文件夹，以将数据发送给 SIEM 实例。 关于 SIEM 配置，请参阅 [SIEM 配置](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm)。

如果有关于 Azure 日志集成方面的问题，请发送电子邮件至 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 审核日志和属性定义的详细信息，请参阅：

* [使用资源管理器执行审核操作](../resource-group-audit.md)
* [列出订阅中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx) - 检索审核日志事件。

若要了解有关安全中心的详细信息，请参阅以下文章：

* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)了解如何管理和响应安全警报。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) — 获取最新的 Azure 安全新闻和信息。



<!--HONumber=Dec16_HO2-->


