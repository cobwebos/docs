---
title: 了解最新的 Azure 来宾 OS 版本 | Microsoft Docs
description: 有关 Azure 云服务来宾 OS 的最新发行新闻以及 SDK 兼容性。
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 1/24/2020
ms.author: raiye
ms.openlocfilehash: c4bdb6fec5c20940e0afe4ab2e94702a2b21a60c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78344524"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure 来宾 OS 版本和 SDK 兼容性对照表
提供适用于云服务的最新 Azure 来宾 OS 版本的最新信息。 此信息可帮助在禁用来宾 OS 之前规划升级路径。 如果根据 *Azure 来宾 OS 更新设置*中所述将角色配置为使用[自动][Azure Guest OS Update Settings]进行来宾 OS 更新，则不一定要阅读本页面。

> [!IMPORTANT]
> 本页面适用于在来宾 OS 顶层运行的云服务 Web 角色和辅助角色， 而**不适用于** IaaS 虚拟机。
>
>


> [!TIP]
>  订阅[来宾 OS 更新 RSS 源]，接收有关所有来宾 OS 更改的最新通知。
>
>

> [!IMPORTANT]
> 仅来宾 OS 的最新 2 个版本受到支持，并在 Azure 门户中提供。
>
>

不确定如何更新来宾 OS？ 查看[此][cloud updates]问题。

## <a name="news-updates"></a>新闻更新

###### <a name="january-24-2020"></a>**2020年1月24日**
1 月版来宾 OS 已发布。 

###### <a name="january-8-2020"></a>**2020年1月8日**
12 月版来宾 OS 已发布。

###### <a name="december-5-2019"></a>**2019年12月5日**
11 月版来宾 OS 已发布。

###### <a name="november-1-2019"></a>**2019年11月1日**
10 月版来宾 OS 已发布。

###### <a name="october-7-2019"></a>**2019年10月7日**
9 月版来宾 OS 已发布。

###### <a name="september-4-2019"></a>**2019年9月4日**
8 月版来宾 OS 已发布。

###### <a name="july-26-2019"></a>**2019年7月26日**
7 月版来宾 OS 已发布。

###### <a name="july-8-2019"></a>**2019年7月8日**
6 月版来宾 OS 已发布。

###### <a name="june-6-2019"></a>**2019年6月6日**
5 月版来宾 OS 已发布。

###### <a name="may-7-2019"></a>**5月7日，2019**
4 月版来宾 OS 已发布。

###### <a name="march-26-2019"></a>**2019年3月26日**
3 月版来宾 OS 已发布。

###### <a name="march-12-2019"></a>**2019年3月12日**
1 月版来宾 OS 已发布。

###### <a name="february-5-2019"></a>**2019 年 2 月 5 日**
1 月版来宾 OS 已发布。

###### <a name="january-24-2019"></a>**2019 年 1 月 24 日**
系列 6 来宾 OS (Windows Server 2019) 已发布。

###### <a name="january-7-2019"></a>**2019 年 1 月 7 日**
12 月版来宾 OS 已发布。

###### <a name="december-14-2018"></a>**2018 年 12 月 14 日**
11 月版来宾 OS 已发布。

###### <a name="november-8-2018"></a>**2018 年 11 月 8 日**
10 月版来宾 OS 已发布。

###### <a name="october-12-2018"></a>**2018 年 10 月12 日**
9 月版来宾 OS 已发布。

## <a name="releases"></a>发行版本

## <a name="family-6-releases"></a>系列 6 发行版
**Windows Server 2019**

安装 .NET Framework：3.5、4.7。2

> [!NOTE]
> 可在[此处][Windows Azure SDK]下载 MICROSOFT Azure SDK for .net-3.0。
>
>安装步骤：
>1. 请卸载任何较早版本的 MicrosoftAzureAuthoringTools*
>2. 安装[用于 .net 的 AZURE SDK-3.0][Windows Azure SDK]
>3. 重新启动计算机
>4. 创建新的云服务项目并添加单个辅助角色
>5. 将操作系统系列更改为6并构建包
>6. 使用 Azure 门户或 Visual Studio 将包部署到 Azure
>


| 配置字符串 | 发布日期 | 停用日期 |
| --- | --- | --- |
|  WA-6.15 _202001-01  |  2020年1月24日  |  Post 6.17  |  
| WA-6.14 _201912-01 | 2020年1月8日 | Post 6.16 |  
|~~WA-6.13 _201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-6.12 _201910-01~~| 2019年11月1日 | 2020年1月8日 |  
|~~WA-6.11 _201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-_201908-~~| 2019年8月4日 | 2019年11月1日  |  
|~~WA-_201907-6.9-0~~|2019年7月26日 | 2019年10月7日 |
|~~WA-_201906-6.8-01~~|2019年7月8日 |2019年8月4日 |
|~~WA-OS-6.7 _201905-01~~ |2019年6月6日 |2019年7月26日 |
|~~WA-OS-6.6 _201904-01~~ |5月7日，2019 |2019年7月8日 |
|~~WA-OS-6.5 _201903-01~~ |2019 年 3 月 26 日 |2019年6月6日 |
|~~WA-OS-6.4 _201902-01~~ |2019 年 3 月 12 日 |5月7日，2019 |
|~~WA-OS-6.3 _201901-01~~ |2019 年 2 月 5日 |2019 年 3 月 26 日 |
|~~WA-OS-6.2 _201812-01~~ |2019 年 1 月 24 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-6.1_201811-01~~ |2019 年 1 月 24 日 |2019 年 2 月 5日 |

## <a name="family-5-releases"></a>系列 5 发行版本
**Windows Server 2016**

安装 .NET Framework：3.5、4。6

> [!NOTE]
> OS 系列 5 的 RDP 密码至少必须有 10 个字符。
>


| 配置字符串 | 发布日期 | 停用日期 |
| --- | --- | --- |
|  WA-5.39 _202001-01  |  2020年1月24日  |  Post 5.41  |  
| WA-5.38 _201912-01 | 2020年1月8日 | Post 5.40 |  
|~~WA-5.37 _201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-5.36 _201910-01~~| 2019年11月1日 | 2020年1月8日 |  
|~~WA-5.35 _201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-5.34 _201908-01~~|  2019年8月4日  | 2019年11月1日 |  
|~~WA-5.33 _201907-01~~| 2019年7月26日 | 2019年10月7日 |  
|~~WA-5.32 _201906-01~~|2019年7月8日 |2019年8月4日 |
|~~WA-5.31 _201905-01~~ |2019年6月6日 |2019年7月26日 |
|~~WA-5.30 _201904-01~~ |5月7日，2019 |2019年7月8日 |
|~~WA-5.29 _201903-01~~ |2019 年 3 月 26 日 |2019年6月6日 |
|~~WA-5.28 _201902-01~~ |2019 年 3 月 12 日 |5月7日，2019 |
|~~WA-5.27 _201901-01~~ |2019 年 2 月 5日 |2019 年 3 月 26 日 |
|~~WA-5.26 _201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-5.25_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5日 |
|~~WA-GUEST-OS-5.24_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-5.23_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-4-releases"></a>系列 4 发行版本
**Windows Server 2012 R2**

安装 .NET Framework：3.5、4.5.1、4.5。2

| 配置字符串 | 发布日期 | 停用日期 |
| --- | --- | --- |
|  WA-4.74 _202001-01  |  2020年1月24日  |  Post 4.76  |  
| WA-4.73 _201912-01 | 2020年1月8日 | Post 4.75 |  
|~~WA-4.72 _201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-4.71 _201910-01~~| 2019年11月1日 | 2020年1月8日 |  
|~~WA-4.70 _201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-4.69 _201908-01~~| 2019年8月4日 | 2019年11月1日 |  
|~~WA-4.68 _201907-01~~| 2019年7月26日  | 2019年10月7日 |
|~~WA-4.67 _201906-01~~| 2019年7月8日 |2019年8月4日 |
|~~WA-4.66 _201905-01~~ |2019年6月6日 |2019年7月26日 |
|~~WA-4.65 _201904-01~~ |5月7日，2019 |2019年7月8日 |
|~~WA-4.64 _201903-01~~ |2019 年 3 月 26 日 |2019年6月6日 |
|~~WA-4.63 _201902-01~~ |2019 年 3 月 12 日 |5月7日，2019 |
|~~WA-4.62 _201901-01~~ |2019 年 2 月 5日 |2019 年 3 月 26 日 |
|~~WA-4.61 _201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-4.60_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5日 |
|~~WA-GUEST-OS-4.59_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-4.58_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-3-releases"></a>系列 3 发行版本
**Windows Server 2012**

已安装的 .NET Framework：3.5、4.5

| 配置字符串 | 发布日期 | 停用日期 |
| --- | --- | --- |
|  WA-3.81 _202001-01  |  2020年1月24日  |  Post 3.83  |  
| WA-3.80 _201912-01 | 2020年1月8日 | Post 3.82 |  
|~~WA-3.79 _201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-3.78 _201910-01~~| 2019年11月1日 | 2020年1月8日 |  
|~~WA-3.77 _201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-3.76 _201908-01~~|  2019年8月4日  |  2019年11月1日  |  
|~~WA-OS-3.75 _201907-01~~| 2019年7月26日 | 2019年10月7日 |
|~~WA-3.74 _201906-01~~| 2019年7月8日 |2019年8月4日 |
|~~WA-3.73 _201905-01~~ |2019年6月6日 |2019年7月26日 |
|~~WA-3.72 _201904-01~~ |5月7日，2019 |2019年7月8日 |
|~~WA-3.71 _201903-01~~ |2019 年 3 月 26 日 |2019年6月6日 |
|~~WA-3.70 _201902-01~~ |2019 年 3 月 12 日 |5月7日，2019 |
|~~WA-3.69 _201901-01~~ |2019 年 2 月 5日 |2019 年 3 月 26 日 |
|~~WA-3.68 _201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-3.67_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5日 |
|~~WA-GUEST-OS-3.66_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-3.65_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="family-2-releases"></a>系列 2 发行版本
**Windows Server 2008 R2 SP1**

安装 .NET Framework：3.5 （包括2.0 和3.0）、4。5

| 配置字符串 | 发布日期 | 停用日期 |
| --- | --- | --- |
|  WA-2.94 _202001-01  |  2020年1月24日  |  Post 2.96  |  
| WA-2.93 _201912-01 | 2020年1月8日 | Post 2.95 |  
|~~WA-2.92 _201911-01~~| 2019 年 12 月 5 日 | 2020年1月24日 |  
|~~WA-2.91 _201910-01~~| 2019年11月1日 | 2020年1月8日 |  
|~~WA-2.90 _201909-01~~| 2019年10月7日 | 2019 年 12 月 5 日 |  
|~~WA-2.89 _201908-01~~| 2019年8月4日 | 2019年11月1日 |  
|~~WA-2.88 _201907-01~~| 2019年7月26日 | 2019年10月7日 |
|~~WA-2.87 _201906-01~~|2019年7月8日 | 2019年8月4日 |
|~~WA-2.86 _201905-01~~ |2019年6月6日 |2019年7月26日 |
|~~WA-2.85 _201904-01~~ |5月7日，2019 |2019年7月8日 |
|~~WA-2.84 _201903-01~~ |2019 年 3 月 26 日 |2019年6月6日 |
|~~WA-2.83 _201902-01~~ |2019 年 3 月 12 日 |5月7日，2019 |
|~~WA-2.82 _201901-01~~ |2019 年 2 月 5日 |2019 年 3 月 26 日 |
|~~WA-2.81 _201812-01~~ |2019 年 1 月 7 日 |2019 年 3 月 12 日 |
|~~WA-GUEST-OS-2.80_201811-01~~ |2018 年 12 月 14 日 |2019 年 2 月 5日 |
|~~WA-GUEST-OS-2.79_201810-01~~ |2018 年 11 月 8 日 |2019 年 1 月 7 日 |
|~~WA-GUEST-OS-2.78_201809-01~~ |2018 年 10 月 12 日 |2018 年 12 月 14 日 |

## <a name="msrc-patch-updates"></a>MSRC 修补程序更新
[此处][patches]提供了每月来宾 OS 版本随附的修补程序列表。

## <a name="sdk-support"></a>SDK 支持
尽管[AZURE SDK 的停用策略][retire policy sdk]指出仅支持高于2.2 的版本，但特定的来宾操作系统系列允许使用早期版本。 应该始终使用最新的受支持 SDK。

| 来宾 OS 系列 | 兼容的 SDK 版本 |
| --- | --- |
| 6 |版本 2.9.6+ |
| 5 |版本 2.9.5.1+ |
| 4 |版本 2.1+ |
| 3 |版本 1.8+ |
| 2 |版本 1.3+ |
| 1 |版本 1.0+ |

## <a name="guest-os-release-information"></a>来宾 OS 版本信息
来宾 OS 发行版有三个重要的日期：**发行日期**、**停用日期**和**失效日期**。 来宾 OS 在门户中可用，并可选作目标来宾 OS。 当来宾 OS 到达停用日期时，将从 Azure 中删除。 但是，面向该来宾 OS 的任何云服务仍会正常运行。

在停用日期与失效日期之间有一个缓冲期，可让你轻松地从一个来宾 OS 版本转换到新的版本。 如果正在使用*自动更新版*的来宾 OS，则始终可以获得最新版本，而无需担心失效的问题。

**到期**日期过后，仍旧使用该来宾 OS 的所有云服务将被停止、删除或强行升级。 可在[此处][retirepolicy]阅读有关停用策略的详细信息。

## <a name="guest-os-family-version-explanation"></a>来宾 OS 系列版本解释
来宾 OS 系列基于发布的 Microsoft Windows Server 版本。 来宾 OS 是运行 Azure 云服务的基本操作系统。 每个来宾 OS 都具有系列、版本和版本号。

* **来宾 OS 系列**  
  来宾 OS 所基于的 Windows Server 操作系统版本。 例如，*系列 3* 基于 Windows Server 2012。
* **来宾 OS 版本**  
  特定于来宾 OS 系列映像以及在生成新的来宾 OS 版本时提供的相关[Microsoft 安全响应中心（MSRC）][msrc]修补程序。 并非提供了所有修补程序。

    版本号从 0 开始，并在每次添加新的一组更新时增加 1。 仅在比较重要时，才会显示尾随零。 即，2.10 版是与 2.1 版不同的版本，并且比它晚得多。
* **来宾 OS 发行版**  
  来宾 OS 版本的再发行版。 如果 Microsoft 在测试期间发现需要更改的问题，就会出现再发行版。 最新的发行版始终会取代任何以前的发行版（无论是否公开）。 Azure 门户将只允许用户选取给定版本的最新发行版。 通常，不会对运行在以前版本上的部署进行强制升级，具体取决于 Bug 的严重性。

在下面的示例中，2 是系列，12 是版本，而“rel2”是发行版本。

**来宾 OS 发行版** - 2.12 rel2

**此发行版本的配置字符串** - WA-GUEST-OS-2.12_201208-02

来宾 OS 的配置字符串嵌入了该相同信息，以及显示考虑为该发行版本包括哪些 MSRC 修补程序的日期。 在此示例中，考虑包括 2012 年 8 月之前（含该日期）为 Windows Server 2008 R2 开发的 MSRC 修补程序。 仅包括专门应用于该版本的 Windows Server 的修补程序。 例如，如果某个 MSRC 修补程序应用于 Microsoft Office，则它将不包括在内，因为该产品不属于 Windows Server 基本映像。

## <a name="guest-os-system-update-process"></a>来宾 OS 系统更新过程
本页包含有关即将发布的来宾 OS 版本的信息。 客户已表明想知道什么时候发行版本，因为如果未设为“自动”更新，他们的云服务角色将重新启动。 来宾 OS 发行版本通常会在每月第二个星期二发布 MSRC 更新之后的 2-3 周发布。 新版本包含针对每个来宾 OS 系列的所有相关 MSRC 修补程序。

Microsoft Azure 不断地发布更新。 来宾 OS 只不过是此类更新的其中一种。 一个发行版本会受到许多因素影响，不胜列举。 此外，Azure 实际上在成千上万的计算机上运行。 这意味着无法提供重新启动你角色的准确日期和时间。 我们正在制定限制或定时重新启动的计划。

在发布新的来宾 OS 发行版本时，可能需要一段时间才能在 Azure 中完全传播。 在将服务更新为新的来宾 OS 时，将重新启动这些服务以满足更新域限制。 设置为使用“自动”更新的服务将先获取发行版本。 更新后将在 Azure 门户中显示为所选服务列出的新来宾 OS 版本。 在此期间，可能会发布再发行版本。 某些版本可能需要较长的部署时间，可能不会在正式发行日期后的几个星期内执行自动升级重新启动。 在来宾 OS 可用后，你可以从门户或配置文件中显式选择该版本。

有关重新启动和指针的大量宝贵信息以及有关来宾和主机操作系统更新的详细信息，请参阅 MSDN 博客文章[角色实例因操作系统升级而重新启动][restarts]。

如果你手动更新来宾 OS，请参阅[来宾 os 停用策略][retirepolicy]以获取其他信息。

## <a name="guest-os-supportability-and-retirement-policy"></a>来宾 OS 可支持性和停用策略
[此处][retirepolicy]解释了来宾 OS 可支持性和停用策略。

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[来宾 OS 更新 RSS 源]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
