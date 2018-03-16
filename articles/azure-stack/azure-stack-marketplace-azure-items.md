---
title: "适用于 Azure Stack 的 Azure Marketplace 项目 | Microsoft Docs"
description: "这些 Azure Marketplace 项目可以用在 Azure Stack 中。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: ae269c532773862e99884d84a05235cb904d13d4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="azure-marketplace-items-available-for-azure-stack"></a>适用于 Azure Stack 的 Azure Marketplace 项目

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*


## <a name="virtual-machine-extensions"></a>虚拟机扩展
每当你使用的虚拟机 (VM) 扩展的更新时，你应下载它们。 在产品中提供的扩展不正常的修补程序和更新过程; 中更新因此经常检查更新。 其他扩展只能通过 Marketplace 管理获取。

|  | 项名称 | 说明 | 发布者 | OS 类型 |
| --- | --- | --- | --- | --- |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [ SQL IaaS 扩展 ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)| <b>下载此扩展即可在 Windows Marketplace 项目上部署任何 SQL Server - 此项为必需项。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [ 自定义脚本扩展 ](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-customscript)| <b>请下载此更新，此更新针对用于 Windows 的自定义脚本扩展的内置版本。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/dsc.png) | [ PowerShell DSC 扩展 ](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-dsc-overview)| <b>请下载此更新，此更新针对 PowerShell DSC 扩展的内置版本。</b> | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [ Microsoft 反恶意软件扩展 ](https://docs.microsoft.com/en-us/azure/security/azure-security-antimalware)| 适用于 Azure 的 Microsoft 反恶意软件是一个针对应用程序和租户环境所提供的单一代理解决方案，可在在后台运行而无需人工干预。 | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/dockerextension.png) | [Docker](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.docker-arm) | 适用于 Linux 虚拟机的 Docker 扩展。 | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [ 适用于 Linux 的 VM 访问权限 ](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)| <b>请下载此更新，此更新针对适用于 Linux 的 VM 访问权限扩展的内置版本。此更新是重要信息： 如果你打算使用 Debian Linux Vm。</b> | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [ 适用于 Linux 的 Acronis 备份扩展 ](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-lin-arm) | Microsoft Azure Acronis 备份扩展是 Acronis 备份产品系列的数据保护的一部分。 | Acronis International GmbH。 | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [ 适用于 Windows 的 Acronis 备份扩展 ](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-win-arm) | Microsoft Azure Acronis 备份扩展是 Acronis 备份产品系列的数据保护的一部分。 | Acronis International GmbH。 | Windows |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [ 适用于 Linux CloudLink SecureVM 扩展 ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 VM。 | Dell EMC | Linux |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [ 适用于 Windows 的 CloudLink SecureVM 扩展 ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 VM。 | Dell EMC | Windows |

## <a name="microsoft-virtual-machine-images-and-solution-templates"></a>Microsoft 虚拟机映像和解决方案模板

Microsoft Azure 堆栈支持以下 Azure 应用商店虚拟机和解决方案模板。 所述单独下载任何依赖关系。 SQL Server 和 Machine Learning Server 之类的应用程序需要适当的许可，除非已标记为“免费”或“试用”。

|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/windowsserver.png) | [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) | 企业级解决方案，部署简单，经济高效，以应用程序和用户为中心。 这些映像会定期更新，发布最新修补程序。 <b>重要信息：必须删除 2018 年 1 月 18 日之前下载的映像并将其替换为最新版本。</b> | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/remotedesktopservicesdeployment.png) | [远程桌面服务 (RDS) 部署](https://azuremarketplace.microsoft.com/marketplace/apps/rds.remote-desktop-services-basic-deployment) | 创建基本的远程桌面服务 (RDS) 部署。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2013 试用版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2013Trial) | Windows Server 2012 Datacenter 和 Visual Studio 2017 社区版上的 Microsoft SharePoint Server 2013 试用版。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2016 试用版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2016Trial) | Windows Server 2016 Datacenter 上的 Microsoft SharePoint Server 2016 试用版。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2012 R2 上的 SQL Server 2014 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2014SP2-WS2012R2) | SQL Server 2014 Service Pack 2。  **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 适用于任务关键型智能应用程序的数据库平台。 **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016) | SQL Server 2016 SP1 的免费开发人员版，适用于事务处理、数据仓库、商业智能和分析型工作负荷。  **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016) | SQL Server 2016 SP1 的免费 Express 版本。 **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 适用于任务关键型智能应用程序的数据库平台。  **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 适用于任务关键型智能应用程序的数据库平台。  **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonWindowsServer2016) | 适用于任务关键型智能应用程序的数据库平台。 **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) | SQL Server 2016 SP1 的免费开发人员版，适用于事务处理、数据仓库、商业智能和分析型工作负荷。  **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) | SQL Server 2016 SP1 的免费 Express 版本。 **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseWindowsServer2016) | 适用于任务关键型智能应用程序的数据库平台。  **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonWindowsServer2016) | 适用于任务关键型智能应用程序的数据库平台。  **必需下载项：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) | SQL Server 2016 SP1 的免费开发人员版，适用于事务处理、数据仓库、商业智能和分析型工作负荷。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) | SQL Server 2016 SP1 的免费 Express 版本。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 自 2017 年 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseUbuntuServer1604LTS) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonUbuntuServer1604LTS) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 上的 SQL Server 自 2017 年 Standard SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017StandardonSLES12SP2) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux 企业服务器 (SLES) 12 上的 SQL Server 自 2017 年 Developer SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP) | SQL Server 2016 SP1 的免费开发人员版，适用于事务处理、数据仓库、商业智能和分析型工作负荷。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 上的 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) | SQL Server 2016 SP1 的免费 Express 版本。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 上的 SQL Server 自 2017 年 Enterprise SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017EnterpriseonSLES12SP2) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 自 2017 年 1 Web 在 SUSE Linux Enterprise Server (SLES) 12 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017WebonSLES12SP2) | 适用于任务关键型智能应用程序的数据库平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 机器学习 Windows Server 2016 上的服务器 9.3.0 ](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onWindowsServer2016) | Microsoft 机器学习服务器 9.3.0 Windows Server 2016 上。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 机器学习服务器 9.3.0 上 Ubuntu 16.04 ](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onUbuntu1604) | Microsoft 机器学习服务器 9.3.0 Ubuntu 16.04 上。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 机器学习 CentOS Linux 7.2 上的服务器 9.3.0 ](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onCentOSLinux72) | Microsoft 机器学习服务器 9.3.0 CentOS Linux 7.2 上。 | Microsoft + Rogue Wave |


## <a name="linux-distributions"></a>Linux 发行版
|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/coreos.png) | [CoreOS 提供的 Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS) | Container Linux 是一种新式的最小型 Linux 发行版，可以方便地运行容器、管理群集以及无缝地更新服务器 - 所有组件都启用了仓库规模的计算。 | CoreOS |
| ![](media/azure-stack-marketplace-azure-items/ubuntu.png) | [Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer) | Ubuntu Server 是全球流行的 Linux 云环境。 | Canonical |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 8“Jessie”](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) | Debian GNU/Linux 是最流行的 Linux 分发版之一。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 9“Stretch”](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian9) | Debian GNU/Linux 是最流行的 Linux 分发版之一。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 6.9](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased69) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic）  |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 7.4](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic） |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 CentOS 的 7.4-LVM](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74LVM) | 此 Linux 发行版基于 CentOS ，由 Rogue Wave Software 提供。 | Rogue Wave Software（之前为 OpenLogic） |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 12 SP3 (BYOS) ](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSELinuxEnterpriseServer12SP3BringYourOwnSubscription) | SUSE Linux Enterprise Server 12 SP3。 | SUSE |

## <a name="third-party-byol-free-and-trial-images-and-solution-templates"></a>第三方 BYOL、 空闲和试用版映像和解决方案模板

|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/abantecart.png) | [AbanteCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.abantecart) | 开源电子商务购物车。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/activemq.png) | [ActiveMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.activemq) | 在 Java 中的开放源代码消息代理。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/akeneo.png) | [Akeneo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.akeneo) | 强大的 PIM，旨在简化管理过程。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/alfrescocommunity.png) | [Alfresco Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.alfrescocommunity) | ECM 系统，方便文档管理。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/apachesolr.png) | [Apache Solr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.apachesolr) | 可靠的开源企业搜索平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/canvaslms.png) | [Canvas LMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.canvaslms) | 开放源代码学习管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra) | 具有高可用性的可缩放的开源数据库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/civicrm.png) | [CiviCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.civicrm) | 简单的基于 Web 的关系管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cmsmadesimple.png) | [CMS Made Simple](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cmsmadesimple) | 快速轻松地创建和管理网站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codedx.png) | [Code Dx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codedx) | 代码分析和漏洞管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codiad.png) | [Codiad](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codiad) | 最小占用具有开放源代码云 IDE。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/concrete5.png) | [Concrete5](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.concrete5) | 轻松部署 Web 应用程序、网站、存储和论坛。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/coppermine.png) | [Coppermine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.coppermine) | 完备的多用途 Web 库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/couchdb.png) | [CouchDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.couchdb) | 能够轻松使用开放源代码数据库系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/diaspora.png) | [Diaspora](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.diaspora) | 常用的个人 Web 服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/discourse.png) | [Discourse](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.discourse) | 高分辨率的开源讨论平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/django.png) | [Django](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.djangostack) | 高级 Python Web 框架。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dolibarr.png) | [Dolibarr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dolibarr) | 免费的开源软件程序包。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dokuwiki.png) | [DokuWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dokuwiki) | 通用的开源 wiki 软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dreamfactory.png) | [DreamFactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dreamfactory) | 使用服务，例如 SQL、 NoSQL 和 BLOB 的开放源代码 REST API。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elasticsearch.png) | [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elastic-search) | 灵活且功能强大的开源分析引擎。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elk.png) | [ELK](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elk) | 由 Elasticsearch、Kibana、Logstash 组成的大数据套件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/erpnext.png) | [ERPNext](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.erpnext) | 开放源代码企业资源规划 (ERP) 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/espocrm.png) | [EspoCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.espocrm) | 简单的 CRM 系统，可帮助管理客户关系。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/exoplatform.png) | [eXo Platform](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.exoplatform) | 对于企业而设计的开放源代码、 社交软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/fatfreecrm.png) | [Fat Free CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.fatfreecrm) | 开放源代码 Ruby on Rails 基于 CRM。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/bitnami.png) | [GitLab Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.gitlab) | 快速、安全的 Git 管理软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ghost.png) | [Ghost](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ghost) | 专用于发布的平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hadoop.png) | [Hadoop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hadoop) | 适用于可靠、可伸缩分布式计算的框架。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hhvm.png) | [HHVM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hhvmstack) | 完全集成且已做好运行准备的开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hordegroupwarewebmail.png) | [Horde Groupware Webmail](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hordegroupwarewebmail) | 基于浏览器的企业就绪型免费通信套件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jenkins) | 集成服务器，支持以下 SCM 工具：CVS、Subversion 和 Git。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jfrogartifactory.png) | [由 Bitnami 认证的 JFrog Artifactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.artifactory) | 由领先的发布者提供的二进制存储库软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/joomla.png) | [Joomla!](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.joomla) | 用户友好型 CMS，可以方便地生成网站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jruby.png) | [JRuby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jrubystack) | Ruby 的高性能 Java 实现。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka) | 强大的分布式发布-订阅消息传送系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lamp.png) | [LAMP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lampstack) | 完全集成且已做好运行准备的开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lapp.png) | [LAPP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lappstack) | 完整的 PHP、PostgreSQL 和 Apache 开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/letschat.png) | [Let's Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.letschat) | 开放源代码持久的消息处理应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/limesurvey.png) | [LimeSurvey](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.limesurvey) | 问答轮询管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/livehelperchat.png) | [Live Helper Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.livehelperchat) | 开放源代码实时聊天支持。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mahara.png) | [Mahara](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mahara) | 常用的开源 ePortfolio 和社交网络的 web 应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/magento.png) | [Magento](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.magento) | 常用的电子商务软件和平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mantis.png) | [Mantis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mantis) | 高级 Bug 跟踪系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mattermostteamedition.png) | [Mattermost Team Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mattermost) | 消息传送解决方案的开源工作区。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mautic.png) | [Mautic](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mautic) | 开放源代码的市场营销自动化平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mean.png) | [MEAN](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mean) | 适用于 mongoDB 和 Node.js 的常用开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mediawiki.png) | [MediaWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mediawiki) | 功能强大的可缩放 Wiki 实现。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached) | 高性能分布式内存对象缓存系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/modx.png) | [MODX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.modx) | 直观的 Web CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mongodb) | C + + 编写高性能开源 NoSQL 数据库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/moodle.png) | [Moodle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.moodle) | 为在线学习社区设计的有效 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/multicraft.png) | [Multicraft](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.multicraft) | 托管解决方案和控制面板的 Minecraft 服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mybb.png) | [MyBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mybb) | 免费版和开放源代码论坛软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql) | 最常用的数据库系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/neos.png) | [Neos](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neos) | 通用的开源内容管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nginx.png) | [Nginx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nginxstack) | 完整的 PHP、 MySQL 和 Nginx 开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/noalyss.png) | [Noalyss](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.noalyss) | 功能强大的复式记帐系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nodejs.png) | [node.js](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nodejs) | 轻松构建用 Javascript 编写的开源环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/odoo.png) | [Odoo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.odoo) | 可以有效地连接业务流程的 ERP 和 CRM 系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openatrium.png) | [Open Atrium](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openatrium) | 灵活且多面的 Intranet 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/opencart.png) | [OpenCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.opencart) | 适用于在线商家的免费电子商务平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openedx.png) | [Bitnami 认证的 Open edX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openedx) | 由领先的发布者提供的电子教学软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openfire.png) | [OpenFire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openfire) | 使用符合 XMPP 的开放源代码实时协作服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openproject.png) | [OpenProject](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openproject) | 流行的开放源代码项目管理软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orangehrm.png) | [OrangeHRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orangehrm) | 包含丰富模块的 HR 管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orocrm.png) | [OroCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orocrm) | 灵活的开源 CRM 应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/osclass.png) | [Osclass](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.osclass) | 在不需技术知识的情况下创建和管理免费分类广告。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/owncloud.png) | [ownCloud](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.owncloud) | 流行的开放源代码文件同步，并共享解决方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/oxideshop.png) | [OXID eShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.oxid-eshop) | 受信任的开源电子商务系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phpbb.png) | [phpBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phpbb) | 可自定义的公告板解决方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phplist.png) | [phpList](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phplist) | 单向电子邮件公告传送系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pimcore.png) | [Pimcore](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pimcore) | 功能强大的业务管理平台 (CEM/CXM)。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/piwik.png) | [Piwik](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.piwik) | 实时 Web 分析软件程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/plone.png) | [Plone](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.plone) | 免费的开源虚拟设备。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pootle.png) | [Pootle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pootle) | 适用于翻译项目的易用 Web 门户。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgresql) | 高度高级的开源数据库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/prestashop.png) | [PrestaShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.prestashop) | 开源电子商务网站生成器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker 社区版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakeropensourceedition) | 业务流程管理和工作流自动化平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker 企业版](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakerenterprise) | 开放源代码工作流和业务流程管理软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processwire.png) | [ProcessWire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processwire) | 常用 PHP5 的开源 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/publify.png) | [Publify](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.publify) | 基于 Ruby on Rails 的博客创建平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq) | 提供通用平台的高效消息传递代理。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redash.png) | [Re:dash](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redash) | 打开源数据可视化和协作平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redis.png) | [Redis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis) | 功能强大的开源键-值存储。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmine.png) | [Redmine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmine) | 强大的项目管理 Web 应用 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmineagile.png) | [Redmine+Agile](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmineplusagile) | 使用 Agile 插件预先配置的项目管理应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserver.png) | [ReportServer 社区](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserver) | 开放源代码业务智能平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserverenterprise.png) | [ReportServer Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserverenterprise) | 企业商业智能平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/resourcespace.png) | [ResourceSpace](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.resourcespace) | 改进了协作的数字资产管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/roundcube.png) | [Roundcube](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.roundcube) | 基于浏览器的 IMAP 客户端，包含 MIME 支持之类的功能。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ruby.png) | [Ruby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rubystack) | 适用于 Ruby on Rails 的易用开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/seopanel.png) | [SEO Panel](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.seopanel) | 开放源代码 SEO 用于管理应用程序跟踪多个网站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/shopware.png) | [Shopware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.shopware) | 开源电子商务平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/simplemachinesforum.png) | [Simple Machines Forum](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.simplemachinesforum) | 简单的论坛软件，适用于生成自己的在线论坛。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/spree.png) | [Spree](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.spree) | 易用的电子商务平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/subversion.png) | [Subversion](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.subversion) | 开放源代码版本控制系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/sugarcrm.png) | [SugarCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.sugarcrm) | 灵活的客户关系管理解决方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/suitecrm.png) | [SuiteCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.suitecrm) | 常用的企业级 CRM 应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/testlink.png) | [TestLink](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.testlink) | 测试管理软件，增强质量保证。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tikiwikicmsgroupware.png) | [Tiki Wiki CMS Groupware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tikiwikicmsgroupware) | 完备的 Wiki 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tinytinyrss.png) | [Tiny Tiny RSS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tinytinyrss) | 灵活的开源基于 web 的新闻源和聚合器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tomcat.png) | [Tomcat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tom-cat) | 实现 Java 社区规范的常用平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/trac.png) | [Trac](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.trac) | 增强的 Wiki 和问题跟踪系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/typo3.png) | [Typo3](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.typo3) | 十分灵活的 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/weblate.png) | [Weblate](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.weblate) | 基于 Web 的翻译管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/webmailprophp.png) | [WebMail Pro PHP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.webmailpro) | 带企业版功能的 Webmail 系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wildfly.png) | [WildFly](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wildfly) | 包括 Apache、WildFly、MySQL 和 Java 在内的应用程序服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress) | 最常用且已准备就绪的 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/x2enginesalescrm.png) | [X2Engine Sales CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.x2enginesalescrm) | 紧凑的营销、销售和客户服务应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/xoops.png) | [Xoops](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.xoops) | CMS 和 Web 门户程序，适用于创建动态网站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/zurmo.png) | [Zurmo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zurmo) | 开放源代码 CRM 系统： 移动、 社会、 和 Gamified。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda 应用程序安全控制中心](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-app-sec-control-center) | 集中管理多个 Barracuda Web 应用程序防火墙 (WAF)。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda 电子邮件安全网关](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-email-security-gateway) | 电子邮件安全网关，以防范入站电子邮件面临的威胁。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Web 应用程序防火墙 (WAF)](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf) | 安全和针对自动和目标的攻击的 DDoS 保护。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/checkpoint.png) | [Check Point vSEC Security Management](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | Check Point vSEC 提供高级的多层威胁防护，可以保护客户在 Azure 中的资产免受恶意软件和复杂威胁的损害。 <b>下载两种 Check Point vSEC Marketplace 项目。</b> | 检查点 |
| ![](media/azure-stack-marketplace-azure-items/chefautomate.png) | [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) | 使用 Chef Automate 这种持续自动化平台进行生成、部署和管理。 <b>下载两种 Chef Marketplace 项目</b> | Chef Software, Inc |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [ CloudLink SecureVM ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 轻松可靠地控制、监视和加密 VM。 <b>下载所有 CloudLink SecureVM 项。<b> | Dell EMC |
| ![](media/azure-stack-marketplace-azure-items/hortonworks.png) | [Hortonworks 数据平台 (HDP) 沙盒](https://azuremarketplace.microsoft.com/marketplace/apps/hortonworks.hortonworks-sandbox) | 由提供支持 HDP 2.5 100%的开源平台 Hadoop、 Spark、 Storm、 HBase、 Kafka、 Hive、 Ambari。 | Hortonworks |
| ![](media/azure-stack-marketplace-azure-items/kemp.png) | [LoadMaster 负载均衡器 ADC 内容交换机](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) | 第 4-7 层应用程序传送控制器 (ADC) 负载均衡器、内容交换机和流量管理器。 | KEMP Technologies Inc. |
| ![](media/azure-stack-marketplace-azure-items/noobaa.png) | [NooBaa Hybrid AWS S3 compatible - Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/noobaa.noobaa-hybrid-s3-archive-05) | 兼容 S3 的存储服务，涵盖公用的和本地的容量资源。 | NooBaa |
| ![](media/azure-stack-marketplace-azure-items/puppet.png) | [Puppet Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2) | 使用 Puppet Enterprise 可以实现 Azure Stack 基础结构整个生命周期的自动化。 <b>下载两种 Puppet Marketplace 项目。</b> | Puppet |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SUSE Manager 3.0 代理 (BYOS) ](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSEManager30ProxyBringYourOwnSubscription) | 同类最佳的开源基础结构管理。 | SUSE |
| ![](media/azure-stack-marketplace-azure-items/zerodown.png) | [ZeroDown Software Tool ](https://azuremarketplace.microsoft.com/marketplace/apps/zerodown_software.zerodown-software-tool-7_1) | ZeroDown Software Tool v7.1 - 迁移 + 高可用性。 | ZeroDown Software |
