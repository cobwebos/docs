---
title: "Azure 数据目录发行说明 | Microsoft Docs"
description: "Azure 数据目录的发行说明。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/21/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 60abdc490cefc7362ca6ed0dec7dbfd53984201f


---
# <a name="azure-data-catalog-release-notes"></a>Azure 数据目录发行说明
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Azure 数据目录 2015 年 11 月 20 日发行说明
### <a name="opening-data-sources-in-power-bi-desktop"></a>在 Power BI Desktop 中打开数据源
使用“Azure 数据目录”门户中的“在 Power BI Desktop 中打开”选项时，用户可能会在 Power BI Desktop 应用程序中遇到下面两个问题之一：

* 显示带有“无法打开文档”标题的对话框
* 可以打开 Power BI Desktop 应用程序，但文件显示为空

对于每种情况，可通过从 [PowerBI.com](https://powerbi.com) 下载并安装最新版 Power BI Desktop 解决问题。

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Azure 数据目录 2015 年 11 月 13 日发行说明
### <a name="registering-and-connecting-to-teradata"></a>注册并连接到 Teradata
连接到 Teradata 数据源时，用户必须已安装与所用软件的位数（32 位或 64 位）相匹配的正确 Teradata ODBC 驱动程序。

截至此 ADC 发布日期，最新[适用于 Windows 的 Teradata ODBC 驱动程序（版本 15.10）](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) 兼容 Office 2013，但不兼容 Office 2016。

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Azure 数据目录 2015 年 7 月 13 日发行说明
### <a name="registering-and-connecting-to-oracle-database"></a>注册并连接到 Oracle 数据库
连接到 Oracle 数据库数据源时，用户必须已安装与所用软件的位数（32 位或 64 位）相匹配的正确 Oracle 驱动程序。

* 在运行 32 位 Windows 的计算机上注册 Oracle 数据源时，将使用 32 位 Oracle 驱动程序
* 在运行 64 位 Windows 的计算机上注册 Oracle 数据源时，将使用 64 位 Oracle 驱动程序
* 在运行 32 位版 Microsoft Office（包括 64 位 Windows）的计算机上使用 Excel 连接到 Oracle 数据源时，将使用 32 位 Oracle 驱动程序
* 在运行 64 位版 Microsoft Office 的计算机上使用 Excel 连接到 Oracle 数据源时，将使用 64 位 Oracle 驱动程序

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>注册并连接到 SQL Server Reporting Services
对 SQL Server Reporting Services (SSRS) 数据源的支持当前仅限于本机模式服务器。 将在以后的版本中添加对 SharePoint 模式下 SSRS 的支持。

### <a name="opening-data-assets-in-excel"></a>在 Excel 中打开数据资产
从“Azure 数据目录”门户在 Microsoft Excel 中打开数据资产时，用户可能会收到“Microsoft Excel 安全公告”对话框提示。 这是标准的预期行为，用户可以选择“启用”继续操作。

有关详细信息，请参阅[启用或禁用对于来自可疑网站的链接及文件的安全警报](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)。

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>代理和策略配置以及数据源注册
用户可能会遇到这种情况：可以登录 Azure 数据目录门户，但在尝试登录数据源注册工具时出现错误消息，导致无法登录。

对于此问题行为有两种可能原因：

**原因 1：Active Directory 联合身份验证服务配置** 数据源注册工具使用窗体身份验证针对 Active Directory 验证用户登录。 若要成功登录，Active Directory 管理员必须在全局身份验证策略中启用窗体身份验证。

在某些情况下，只有在用户处于公司网络时，或者从公司外部网络进行连接时才可能出现这样的错误行为。 使用全局验证策略，可以单独为 Intranet 和 Extranet 连接启用身份验证方法。 如果用户连接的网络未启用窗体身份验证，可能会发生登录错误。

有关详细信息，请参阅 [Configuring Authentication Policies](https://technet.microsoft.com/library/dn486781.aspx)（配置身份验证策略）。

**原因 2：网络代理配置** 如果公司网络使用代理服务器，注册工具可能无法通过代理连接到 Azure Active Directory。 通过编辑工具的配置文件，将此部分添加到文件，用户可以确保注册工具不出现上述问题：

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


若要找到 RegistrationTool.exe.config 文件，请启动注册工具，然后打开 Windows 任务管理器实用工具。 在任务管理器的“详细信息”选项卡上，右键单击 RegistrationTool.exe，然后从弹出菜单中选择“打开文件位置”。




<!--HONumber=Nov16_HO3-->


