<properties title="Securing an Azure Website" pageTitle="保护 Azure 网站。" description="了解如何保护 Azure 网站。" metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


# 在 Azure 网站中保护 web 应用程序

Web 应用程序开发的挑战之一是如何为您的客户提供安全的服务。在本文中，您将了解到可以保护您的 web 应用程序的 Azure 网站功能的相关信息。

> [AZURE.NOTE] 关于基于 web 的应用程序安全注意事项的全面讨论超出了本文的范围。作为保护 Web 应用程序的更多指导的起点，请参阅[打开 Web 应用程序安全项目 (OWASP)]( https://www.owasp.org/index.php/Main_Page)，尤其是[前 10 大项目](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)，其列出当前 10 大 Web 应用程序安全缺陷（由 OWASP 成员确定）。

### 目录

* [安全通信](#https)
* [安全开发](#develop)
* [后续步骤](#next)
 
## <a name="https"></a>安全通信

如果您使用为您的网站创建的 **\*.chinacloudsites.cn** 域名，您创建可以立即使用 HTTPS，因为针对所有 **\*.chinacloudsites.cn** 域名提供了 SSL 证书。如果您的网站使用[自定义域名](/zh-cn/documentation/articles/web-sites-custom-domain-name/)，您可以上载 SSL 证书来为自定义域启用 HTTPS。

## <a name="develop"></a>安全开发 

### 发布配置文件和发布设置

在开发应用程序，执行管理任务，或使用实用程序（如 **Visual Studio**、**Web Matrix**、**Azure PowerShell** 或 Azure **跨平台命令行界面**）来自动执行任务时，您可以使用 *publish settings*文件或 *publishing profile*。两者都向 Azure 验证您的身份，并应受保护以防止未经授权的访问。

* 一个**发布设置**文件包含

	* 您的 Azure 订阅 ID

	* 管理证书，允许您为订阅执行管理任务 *without having to provide an account name or password*。

* 一个**发布配置文件**包含

	* 用于发布到 Azure 网站的信息

如果您使用一个利用发布设置或发布配置文件的实用程序，请将包含发布设置或配置文件的该文件导入到实用程序，然后**删除**该文件。如果您必须保留该文件以便与处理项目的其他人共享（举例），请将其存储在安全的位置，例如，权限受限的**加密**目录。

此外，应确保已导入的凭证受到保护。例如， **Azure PowerShell** 和 **Azure 跨平台命令行界面**都将导入的信息存储在您的**主目录**(在 Linux 或 OS X 系统上为*~*，Windows 系统上为 */users/yourusername*。）为了提供额外的安全性，您可能希望使用可用于您的操作系统的加密工具来**加密**这些位置。

### 配置设置和连接字符串
常见的做法是，将连接字符串、 身份验证凭证和其他敏感信息存储在配置文件中。遗憾的是，这些文件可能会在您的网站上被公开或将其检入一个公共存储库，从而公开此类信息。

Azure 网站允许您将配置信息作为**应用设置**和**连接字符串**存储为网站运行时环境的一部分。对于大多数编程语言，这些值通过  *environment variables* 在运行时披露给您的应用程序。对于 .NET 应用程序，在运行时这些值被注入到.NET 配置。

**应用设置**和**连接字符串**可以使用 Azure 管理门户或实用程序（如 PowerShell 或 Azure 跨平台命令行界面）来配置。

有关应用设置和连接字符串的更多信息，请参阅[配置网站](/zh-cn/documentation/articles/web-sites-configure/)。

### FTPS

Azure 通过 **FTPS** 提供对您网站文件系统的安全 FTP 访问。这使您可以安全地访问网站上的应用程序代码以及诊断日志。可以在 [Azure 管理门户](中的站点**仪表板**中找到您网站的 FTPS 链接https://manage.windowsazure.cn)。

有关 FTPS 的更多信息，请参阅[文件传输协议](http://en.wikipedia.org/wiki/File_Transfer_Protocol)。

## 后续步骤

有关 Azure 平台的更多信息、关于报告**安全事件或滥用**的信息，或者通知 Microsoft 您将对网站执行**渗透测试**，请参阅 [Microsoft Azure Trust Center](/zh-cn/support/trust-center/security/)的安全部分。

有关 Azure 网站中 **web.config** 或 **applicationhost.config** 文件的更多信息，请参阅[ Azure 网站中解锁的配置选项](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)。

有关 Azure 网站的日志记录信息（可能在检测攻击时很有用），请参阅[启用诊断日志记录](/zh-cn/documentation/articles/web-sites-enable-diagnostic-log/)。
