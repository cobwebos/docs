---
title: "可供 Azure 堆栈的 azure 应用商店项 |Microsoft 文档"
description: "可以在 Azure 堆栈中使用这些 Azure 应用商店项。"
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: JeffGoldner
ms.openlocfilehash: d6a0cbf696d59cec5b70e147de2879a311d88687
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="azure-marketplace-items-available-for-azure-stack"></a>可供 Azure 堆栈的 azure 应用商店项

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*


## <a name="virtual-machine-extensions"></a>虚拟机扩展
最好下载这些需要 VM 扩展的更新时。 在产品中提供的扩展将不会更新在正常的修补程序和更新过程;请经常检查更新。 仅通过应用商店管理提供了其他扩展。

|  | 项名称 | 说明 | 发布者 | OS 类型 |
| --- | --- | --- | --- | --- |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [SQL IaaS 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)| <b>下载此扩展来部署 Windows 应用商店项上的任何 SQL Server-这是必需的。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [自定义脚本扩展](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-customscript)| <b>下载此更新到框中的自定义脚本扩展适用于 Windows 的版本。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/dsc.png) | [PowerShell DSC 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-dsc-overview)| <b>下载此更新的现成新版 PowerShell DSC 扩展。</b> | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [Microsoft 反恶意软件扩展](https://docs.microsoft.com/en-us/azure/security/azure-security-antimalware)| 适用于 Azure 的 Microsoft 反恶意软件是一个针对应用程序和租户环境所提供的单一代理解决方案，可在在后台运行而无需人工干预。 | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/dockerextension.png) | [Docker](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.docker-arm) | Linux 虚拟机的 docker 扩展。 | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [适用于 Linux 的 VM 访问](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)| <b>下载此更新的现成新版 VMAccess for Linux 扩展。这是重要信息： 如果你打算使用 Debian Linux Vm。</b> | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [适用于 Linux Acronis 备份扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-lin-arm) | Microsoft Azure Acronis 备份扩展是 Acronis 备份产品系列的数据保护的一部分。 | Acronis 国际 GmbH。 | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [适用于 Windows 的 Acronis 备份扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-win-arm) | Microsoft Azure Acronis 备份扩展是 Acronis 备份产品系列的数据保护的一部分。 | Acronis 国际 GmbH。 | Windows |
| ![](media/azure-stack-marketplace-azure-items/trendmicro.png) | [适用于 Linux 的 deep Security 代理](https://azuremarketplace.microsoft.com/marketplace/apps/TrendMicroDeepSecurity.dsa-linux) | 适用于 Linux 的 trend Micro Deep Security 扩展。 | Trend Micro | Linux |
| ![](media/azure-stack-marketplace-azure-items/trendmicro.png) | [适用于 Windows 的 deep Security 代理](https://azuremarketplace.microsoft.com/marketplace/apps/TrendMicroDeepSecurity.dsa-windows-arm) | 适用于 Windows 的 trend Micro Deep Security 扩展。 | Trend Micro | Windows |

## <a name="microsoft-virtual-machine-images-and-solution-templates"></a>Microsoft 虚拟机映像和解决方案模板

Microsoft Azure 堆栈支持以下 Azure 应用商店虚拟机和解决方案模板。 请注明单独下载任何依赖项。 SQL Server 和机器学习 Server 等应用程序需要正确地执行授权，除了在标记为可用或试用版。

|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/windowsserver.png) | [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) | 为部署简单、 经济高效、 应用程序为中心和以用户为中心的企业级的解决方案。 这些映像被定期更新的最新修补程序。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/remotedesktopservicesdeployment.png) | [远程桌面服务 (RDS) 部署](https://azuremarketplace.microsoft.com/marketplace/apps/rds.remote-desktop-services-basic-deployment) | 创建基本的远程桌面服务 (RDS) 部署。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2013 Trial](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2013Trial) | Microsoft SharePoint Server 2013 试用版 Windows Server 2012 Datacenter 和 Visual Studio 2017 社区版上。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2016 Trial](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2016Trial) | Microsoft SharePoint Server 2016 试用版 Windows Server 2016 数据中心内。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2014 SP2 on Windows Server 2012 R2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2014SP2-WS2012R2) | SQL Server 2014 Service Pack 2.  **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 智能、 任务关键型应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 开发人员](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016) | 免费的开发人员版本的 SQL Server 2016 SP1 事务，数据仓库、 商业智能和分析工作负荷。  **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2016 SP1 Express on Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016) | 免费 express 版本的 SQL Server 2016 SP1。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 2016 SP1 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 智能、 任务关键型应用程序的数据库平台。  **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2016 SP1 Web on Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 智能、 任务关键型应用程序的数据库平台。  **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 自 2017 年 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonWindowsServer2016) | 智能、 任务关键型应用程序的数据库平台。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 自 2017 年开发人员](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) | 免费的开发人员版本的 SQL Server 2016 SP1 事务，数据仓库、 商业智能和分析工作负荷。  **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2017 Express on Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) | 免费 express 版本的 SQL Server 2016 SP1。 **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 上的 SQL Server 自 2017 年 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseWindowsServer2016) | 智能、 任务关键型应用程序的数据库平台。  **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2017 Web on Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonWindowsServer2016) | 智能、 任务关键型应用程序的数据库平台。  **所需的下载：** SQL IaaS 扩展。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 自 2017 年 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS) | 智能、 任务关键型应用程序的数据库平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 自 2017 年开发人员在 Ubuntu Server 16.04 LTS 上](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) | 免费的开发人员版本的 SQL Server 2016 SP1 事务，数据仓库、 商业智能和分析工作负荷。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 自 2017 年 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) | 免费 express 版本的 SQL Server 2016 SP1。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 自 2017 年 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseUbuntuServer1604LTS) | 智能、 任务关键型应用程序的数据库平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS 上的 SQL Server 自 2017 年 1 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonUbuntuServer1604LTS) | 智能、 任务关键型应用程序的数据库平台。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 上的 SQL Server 自 2017 年 Standard SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017StandardonSLES12SP2) | 智能、 任务关键型应用程序的数据库平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux 企业服务器 (SLES) 12 上的 SQL Server 自 2017 年 Developer SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP) | 免费的开发人员版本的 SQL Server 2016 SP1 事务，数据仓库、 商业智能和分析工作负荷。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 上的 SQL Server 自 2017 年 Express SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) | 免费 express 版本的 SQL Server 2016 SP1。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 上的 SQL Server 自 2017 年 Enterprise SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017EnterpriseonSLES12SP2) | 智能、 任务关键型应用程序的数据库平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 自 2017 年 1 Web 在 SUSE Linux Enterprise Server (SLES) 12 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017WebonSLES12SP2) | 智能、 任务关键型应用程序的数据库平台。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 机器学习 Windows Server 2016 上的服务器 9.2.1](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer921onWindowsServer2016) | Microsoft 机器学习服务器 9.2.1 Windows Server 2016 上。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 机器学习服务器 9.2.1 上 Ubuntu 16.04](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer921onUbuntu1604) | Microsoft 机器学习服务器 9.2.1 Ubuntu 16.04 上。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 机器学习 CentOS Linux 7.2 上的服务器 9.2.1](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer921onCentOSLinux72) | Microsoft 机器学习服务器 9.2.1 CentOS Linux 7.2 上。 | Microsoft + Rogue Wave |


## <a name="linux-distributions"></a>Linux 分发
|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/coreos.png) | [通过 CoreOS 容器 Linux](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS) | 容器 Linux 是现代和最少的 Linux 分发，提供运行容器、 管理群集和无缝地将服务器更新的简单办法-启用仓库缩放的所有组件都计算。 | CoreOS |
| ![](media/azure-stack-marketplace-azure-items/ubuntu.png) | [Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer) | Ubuntu Server 是全球流行的 Linux 云环境。 | Canonical |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) | Debian GNU/Linux 是最流行的 Linux 分发版之一。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 9"Stretch"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian9) | Debian GNU/Linux 是最流行的 Linux 分发版之一。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 centOS 的 6.9](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased69) | 这种 Linux 分发基于 CentOS，以及由恶意批软件。 | 恶意批软件 (以前称为 OpenLogic)  |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 centOS 的 7.4](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74) | 这种 Linux 分发基于 CentOS，以及由恶意批软件。 | 恶意批软件 (以前称为 OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [基于 centOS 的 7.4 LVM](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74LVM) | 这种 Linux 分发基于 CentOS，以及由恶意批软件。 | 恶意批软件 (以前称为 OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 12 SP3 (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSELinuxEnterpriseServer12SP3BringYourOwnSubscription) | SUSE Linux Enterprise Server 12 SP3。 | SUSE |

## <a name="third-party-byol-free-and-trial-images-and-solution-templates"></a>第三方 BYOL、 免费版和试用版映像和解决方案模板

|  | 项名称 | 说明 | 发布者 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/abantecart.png) | [AbanteCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.abantecart) | 打开源电子商务购物车。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/activemq.png) | [ActiveMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.activemq) | 在 Java 中打开源消息代理。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/akeneo.png) | [Akeneo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.akeneo) | 功能强大的 PIM 旨在简化管理过程。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/alfrescocommunity.png) | [Alfresco Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.alfrescocommunity) | ECM 易于进行文档管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/apachesolr.png) | [Apache Solr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.apachesolr) | 可靠的开源企业搜索平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/canvaslms.png) | [Canvas LMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.canvaslms) | 打开源学习管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra) | 具有高可用性的可缩放的开源数据库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/civicrm.png) | [CiviCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.civicrm) | 简单的基于 web 的关系管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cmsmadesimple.png) | [CMS 变得简单](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cmsmadesimple) | 快速轻松地创建和管理网站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codedx.png) | [代码 Dx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codedx) | 代码分析和漏洞管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codiad.png) | [Codiad](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codiad) | 使用最小占用空间打开源云 IDE。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/concrete5.png) | [Concrete5](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.concrete5) | 轻松部署 web 应用程序、 网站、 存储和论坛。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/coppermine.png) | [Coppermine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.coppermine) | 多用途、 完备 web 库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/couchdb.png) | [CouchDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.couchdb) | 能够轻松使用开放源代码数据库系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/diaspora.png) | [Diaspora](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.diaspora) | 常用的个人 web 服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/discourse.png) | [Discourse](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.discourse) | 高分辨率的开放源代码讨论平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/django.png) | [Django](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.djangostack) | 高级 Python Web 框架。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dolibarr.png) | [Dolibarr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dolibarr) | 免费的开源软件程序包。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dokuwiki.png) | [DokuWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dokuwiki) | 通用开源 wiki 软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dreamfactory.png) | [DreamFactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dreamfactory) | 使用服务，例如 SQL、 NoSQL，打开源 REST API 和 BLOB。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elasticsearch.png) | [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elastic-search) | 灵活且功能强大的开放源代码分析引擎。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elk.png) | [ELK](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elk) | Elasticsearch、 Kibana 和 Logstash 组成的大数据套件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/erpnext.png) | [ERPNext](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.erpnext) | 打开源企业资源规划 (ERP) 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/espocrm.png) | [EspoCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.espocrm) | 简单 CRM 系统，可帮助管理客户的关系。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/exoplatform.png) | [eXo 平台](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.exoplatform) | 打开源，而设计的企业社交软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/fatfreecrm.png) | [Fat 可用的 CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.fatfreecrm) | 打开源 Ruby on Rails 基于 CRM。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/bitnami.png) | [GitLab Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.gitlab) | 快速、 安全 Git 管理软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ghost.png) | [Ghost](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ghost) | 一个专用于发布的平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hadoop.png) | [Hadoop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hadoop) | 可靠、 可伸缩的分布式计算的框架。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hhvm.png) | [HHVM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hhvmstack) | 完全集成且做好运行开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hordegroupwarewebmail.png) | [许多组件 Webmail](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hordegroupwarewebmail) | 免费的企业准备就绪后，基于浏览器的通信套件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jenkins) | 集成服务器支持 SCM 工具： CVS、 Subversion 和 Git。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jfrogartifactory.png) | [JFrog Artifactory 由 Bitnami 认证](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.artifactory) | 来自前导发布者的二进制存储库软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/joomla.png) | [Joomla!](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.joomla) | 为简单的网站的用户友好 CMS 生成。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jruby.png) | [JRuby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jrubystack) | 高性能的 Ruby Java 实现。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka) | 强大分发发布-订阅消息传送系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lamp.png) | [LAMP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lampstack) | 完全集成，并准备好运行开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lapp.png) | [LAPP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lappstack) | 完成 PHP、 PostgreSQL、 和 Apache 开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/letschat.png) | [让我们聊天](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.letschat) | 打开源持久消息处理应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/limesurvey.png) | [LimeSurvey](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.limesurvey) | 问题答案轮询管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/livehelperchat.png) | [实时帮助程序聊天](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.livehelperchat) | 打开源实时聊天支持。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mahara.png) | [Mahara](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mahara) | 流行的开放源代码 ePortfolio 和社交网络的 web 应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/magento.png) | [Magento](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.magento) | 常用的电子商务软件和平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mantis.png) | [Mantis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mantis) | 高级 bug 跟踪系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mattermostteamedition.png) | [Mattermost Team Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mattermost) | 打开源工作区的消息传送解决方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mautic.png) | [Mautic](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mautic) | 打开源，市场营销自动化平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mean.png) | [平均值](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mean) | MongoDB 和 Node.js 的流行的开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mediawiki.png) | [MediaWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mediawiki) | 功能强大、 可扩展的 wiki 实现。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached) | 高性能、 分布式内存对象缓存系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/modx.png) | [MODX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.modx) | 直观 Web CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mongodb) | C + + 编写的高性能打开源 NoSQL 数据库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/moodle.png) | [Moodle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.moodle) | 为在线学习社区设计的有效 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/multicraft.png) | [Multicraft](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.multicraft) | 托管解决方案和控制面板的 Minecraft 服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mybb.png) | [MyBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mybb) | 免费的开源论坛软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql) | 最常用的数据库系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/neos.png) | [Neos](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neos) | 通用的开源内容管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nginx.png) | [Nginx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nginxstack) | 一个完整 PHP、 MySQL 和 Nginx 的开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/noalyss.png) | [Noalyss](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.noalyss) | 功能强大的复式记帐系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nodejs.png) | [node.js](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nodejs) | 打开轻松构建用 Javascript 编写的源环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/odoo.png) | [Odoo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.odoo) | 有效地连接业务流程的 ERP 和 CRM 系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openatrium.png) | [打开厅](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openatrium) | 灵活、 更多分面 Intranet 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/opencart.png) | [OpenCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.opencart) | 联机商人的免费电子商务平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openedx.png) | [打开 edX 由 Bitnami 认证](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openedx) | 来自领先的发布者的电子教学软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openfire.png) | [OpenFire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openfire) | 使用符合 XMPP 打开源实时协作服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openproject.png) | [OpenProject](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openproject) | 流行的开放源项目管理软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orangehrm.png) | [OrangeHRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orangehrm) | HR 管理系统中通过丰富的模块。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orocrm.png) | [OroCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orocrm) | 灵活的开放源代码 CRM 应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/osclass.png) | [Osclass](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.osclass) | 创建和管理而无需技术专业知识的免费分类广告。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/owncloud.png) | [ownCloud](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.owncloud) | 常用打开源文件同步，并共享解决方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/oxideshop.png) | [OXID 电子商店](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.oxid-eshop) | 受信任的开源电子商务系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phpbb.png) | [phpBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phpbb) | 可自定义公告板解决方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phplist.png) | [phpList](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phplist) | 单向电子邮件公告传送系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pimcore.png) | [Pimcore](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pimcore) | 功能强大的参与管理平台 (CEM/CXM)。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/piwik.png) | [Piwik](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.piwik) | 实时 web 分析软件程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/plone.png) | [Plone](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.plone) | 免费的开源虚拟设备。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pootle.png) | [Pootle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pootle) | 转换项目的能够轻松使用 web 门户。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgresql) | 高度高级的开源数据库。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/prestashop.png) | [PrestaShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.prestashop) | 打开源电子商务网站生成器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [进程 Maker Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakeropensourceedition) | 业务流程管理和工作流自动化平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [进程 Maker Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakerenterprise) | 打开源工作流和业务流程管理软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processwire.png) | [ProcessWire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processwire) | 常用 PHP5 开源 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/publify.png) | [Publify](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.publify) | Ruby on Rails 基于博客创建平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq) | 提供一个通用平台高效的消息传递代理。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redash.png) | [重新： 短划线](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redash) | 打开源数据可视化和协作平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redis.png) | [Redis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis) | 功能强大的开源键-值存储。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmine.png) | [Redmine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmine) | 强大的项目管理 web 应用 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmineagile.png) | [Redmine+Agile](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmineplusagile) | 项目管理应用程序预先配置为使用敏捷插件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserver.png) | [ReportServer 社区](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserver) | 打开源 business intelligence 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserverenterprise.png) | [ReportServer 企业](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserverenterprise) | 企业业务智能平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/resourcespace.png) | [ResourceSpace](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.resourcespace) | 改进协作的数字资产管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/roundcube.png) | [Roundcube](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.roundcube) | 基于浏览器的功能，如 MIME 支持 IMAP 客户端。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ruby.png) | [Ruby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rubystack) | Ruby on Rails 能够轻松使用开发环境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/seopanel.png) | [SEO 面板](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.seopanel) | 打开源 SEO 用于管理应用程序跟踪多个网站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/shopware.png) | [Shopware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.shopware) | 打开源电子商务平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/simplemachinesforum.png) | [简单机论坛](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.simplemachinesforum) | 若要生成你自己的在线社区的简单论坛软件。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/spree.png) | [Spree](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.spree) | 能够轻松使用电子商务平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/subversion.png) | [Subversion](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.subversion) | 打开源版本控制系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/sugarcrm.png) | [SugarCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.sugarcrm) | 灵活的客户关系管理解决方案。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/suitecrm.png) | [SuiteCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.suitecrm) | 常用的企业级 CRM 应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/testlink.png) | [TestLink](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.testlink) | 测试管理软件推进质量保证。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tikiwikicmsgroupware.png) | [Tiki Wiki CMS 组件](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tikiwikicmsgroupware) | 完备的 wiki 平台。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tinytinyrss.png) | [小小 RSS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tinytinyrss) | 灵活 Spen 源基于 web 的新闻源和聚合器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tomcat.png) | [Tomcat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tom-cat) | 常用平台实现从 Java 社区的规范。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/trac.png) | [Trac](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.trac) | 增强的 wiki 和跟踪系统的问题。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/typo3.png) | [Typo3](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.typo3) | 完全灵活 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/weblate.png) | [Weblate](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.weblate) | 基于 web 的翻译管理系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/webmailprophp.png) | [WebMail Pro PHP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.webmailpro) | 使用企业版功能 Webmail 系统。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wildfly.png) | [WildFly](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wildfly) | 包括 Apache、 WildFly、 MySQL 和 Java 的应用程序服务器。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress) | 最常用的和已准备就绪的 CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/x2enginesalescrm.png) | [X2Engine 销售 CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.x2enginesalescrm) | Compact 市场营销、 销售和客户服务应用程序。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/xoops.png) | [Xoops](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.xoops) | CMS Web 门户和程序和创建动态网站。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/zurmo.png) | [Zurmo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zurmo) | 打开源 CRM 系统： 移动、 社会、 和 Gamified。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/checkpoint.png) | [检查点 vSEC 安全管理](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | 检查点 vSEC 提供高级、 多层次的威胁防护来保护 Azure 中的客户资产免受恶意软件和复杂的威胁。 <b>下载这两个检查点 vSEC 应用商店项。</b> | 检查点 |
| ![](media/azure-stack-marketplace-azure-items/chefautomate.png) | [Chef 自动执行](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) | 生成、 部署和使用 Chef 自动执行，持续的自动化平台进行管理。 <b>下载这两个 Chef 应用商店项</b> | Chef Software, Inc |
| ![](media/azure-stack-marketplace-azure-items/hortonworks.png) | [Hortonworks 数据平台 (HDP) 沙盒](https://azuremarketplace.microsoft.com/marketplace/apps/hortonworks.hortonworks-sandbox) | 采用 HDP 2.5 100%打开源平台 Hadoop、 Spark、 Storm、 HBase、 Kafka、 Hive、 Ambari。 | Hortonworks |
| ![](media/azure-stack-marketplace-azure-items/kemp.png) | [LoadMaster 负载平衡器 ADC 内容交换机](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) | 图 4-7 层应用程序传递控制器 (ADC) 负载平衡器、 内容交换机和流量管理器。 | KEMP Technologies inc. |
| ![](media/azure-stack-marketplace-azure-items/noobaa.png) | [NooBaa 混合 AWS S3 compatibe-Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/noobaa.noobaa-hybrid-s3-archive-05) | 跨公用和本地容量资源的 S3 兼容存储服务。 | NooBaa |
| ![](media/azure-stack-marketplace-azure-items/puppet.png) | [Puppet Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2) | Puppet Enterprise 使你能够自动你 Azure 堆栈的基础结构的整个生命周期。 <b>下载这两个 Puppet 应用商店项。</b> | Puppet |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SUSE Manager 3.0 代理 (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSEManager30ProxyBringYourOwnSubscription) | 同类最佳打开源基础结构管理。 | SUSE |
| ![](media/azure-stack-marketplace-azure-items/zerodown.png) | [ZeroDown 软件工具](https://azuremarketplace.microsoft.com/marketplace/apps/zerodown_software.zerodown-software-tool-7_1) | ZeroDown 软件工具 v7.1-迁移 + HA。 | ZeroDown Software |
