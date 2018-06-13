---
title: 在 BizTalk 服务中创建和还原备份 | Microsoft Docs
description: BizTalk 服务包括备份和还原。 了解如何创建和还原备份，并确定备份内容。 MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102335"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk 服务：备份和还原

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk 服务包括备份和还原功能。 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> 无论版本为何，均不备份混合连接。 必须重新创建混合连接。


## <a name="before-you-begin"></a>开始之前
* 备份和还原可能不适用于所有版本。 请参阅 [BizTalk 服务：版本图表](biztalk-editions-feature-chart.md)。
* 备份内容可以还原到同一个 BizTalk 服务或者新的 BizTalk 服务。 若要使用相同名称还原 BizTalk 服务，必须删除现有 BizTalk 服务，且名称必须可用。 删除 BizTalk 服务后，可能需要长于预期的时间才能使用此相同名称。 如果无法等到此名称可用，请还原到新的 BizTalk 服务。
* BizTalk 服务可以还原到相同版本或更高版本。 不支持从执行备份时还原到更低版本的 BizTalk 服务。
  
    例如，使用基本版的备份可以还原到高级版。 使用高级版的备份不能还原到标准版。
* 对 EDI 控制编号进行备份以便保持控制编号的连续性。 如果消息是在最后的备份后处理的，则还原此备份内容可能会导致重复的控制编号。
* 如果批次含有活动消息，请在运行备份**之前**处理该批次。 在创建备份（根据需要或计划）时，永远不会存储批中的消息。 
  
    **如果对某一批次中的活动消息执行备份，不会备份这些消息，因此这些消息将丢失。**
* 可选：在 BizTalk 服务门户中，停止任何管理操作。

## <a name="create-a-backup"></a>创建备份
备份可以随时进行并由你完全控制。 若要创建备份，请使用[用于在 Azure 上管理 BizTalk 服务的 REST API](https://msdn.microsoft.com/library/azure/dn232347.aspx)。

## <a name="restore"></a>还原
若要还原备份，请使用[用于在 Azure 上管理 BizTalk 服务的 REST API](https://msdn.microsoft.com/library/azure/dn232347.aspx)。

### <a name="postrestore"></a>还原备份后
BizTalk 服务始终在“挂起”状态中还原。 在此状态下，可在新环境正常运行前进行任何配置更改，其中包括：

* 如果使用 Azure BizTalk 服务 SDK 创建 BizTalk 服务应用程序，可能需要更新这些应用程序中的访问控制 (ACS) 凭据以使用还原的环境。
* 还原 BizTalk 服务以复制现有的 BizTalk 服务环境。 在此情况中，如果有在使用源 FTP 文件夹的原始 BizTalk 服务门户中配置的协议，可能需要在新还原的环境中更新协议，以使用不同的源 FTP 文件夹。 否则，可能有两个不同的协议尝试提取同一条消息。
* 如果还原以具有多个 BizTalk 服务环境，请确保针对 Visual Studio 应用程序、PowerShell cmdlet、REST Api 或贸易合作伙伴管理 OM API 中正确的环境。
* 最好在新还原的 BizTalk 服务环境中配置自动化备份。

## <a name="what-gets-backed-up"></a>备份的内容
在创建某一备份时，将备份以下项目：

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>备份的项目</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk 服务门户</strong></td>
</tr> 
<tr>
<td>配置和运行时</td> 
<td>
<ul>
<li>合作伙伴和配置文件详细信息</li>
<li>合作伙伴协议</li>
<li>部署的自定义程序集</li>
<li>部署的桥</li>
<li>证书</li>
<li>部署的转换</li>
<li>管道</li>
<li>在 BizTalk 服务门户中创建和保存的模板</li>
<li>X12 ST01 和 GS01 映射</li>
<li>控制编号 (EDI)</li>
<li>AS2 消息 MIC 值</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Azure BizTalk 服务</strong></td>
</tr> 
<tr>
<td>SSL 证书</td> 
<td>
<ul>
<li>SSL 证书数据</li>
<li>SSL 证书密码</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk 服务设置</td> 
<td>
<ul>
<li>缩放单位计数</li>
<li>版本</li>
<li>产品版本</li>
<li>区域/数据中心</li>
<li>Access Control 服务 (ACS) 命名空间和密钥</li>
<li>跟踪数据库连接字符串</li>
<li>存档存储帐户连接字符串</li>
<li>监视存储帐户连接字符串</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>其他项</strong></td>
</tr> 
<tr>
<td>跟踪数据库</td> 
<td>在创建 BizTalk 服务时，将输入跟踪数据库详细信息，包括 Azure SQL 数据库 Server 和跟踪数据库名称。 不会自动备份跟踪数据库。
<br/><br/>
<strong>重要说明</strong><br/>
如果删除跟踪数据库，并且需要恢复该数据库，必须存在上一个备份。 如果某一备份不存在，则跟踪数据库及其数据将无法恢复。 在此情况下，应使用相同数据库名称创建新的跟踪数据库。 建议执行地域复制。</td>
</tr> 
</table>

## <a name="next"></a>下一步
若要创建 Azure BizTalk 服务，请转到 [BizTalk 服务：预配](http://go.microsoft.com/fwlink/p/?LinkID=302280)。 若要开始创建应用程序，请转到 [Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## <a name="see-also"></a>另请参阅
* [Backup BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=325584)（备份 BizTalk 服务）
* [从备份还原 BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk 服务：开发人员版、基本版、标准版和高级版图表](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk 服务：预配](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk 服务：预配状态图表](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk 服务：限制](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk 服务：颁发者名称和颁发者密钥](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

