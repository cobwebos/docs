---
title: Azure 安全中心中的补救建议 |Microsoft Docs
description: 本文介绍如何在 Azure 安全中心修正建议，以保护资源并遵守安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603498"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure 安全中心的补救建议

建议提供有关如何更好地保护资源的建议。 按照建议中提供的补救步骤来实施建议。

## 修正步骤<a name="remediation-steps"></a>

查看所有建议后，首先确定要修正的建议。 建议你使用[安全分数影响](security-center-recommendations.md#monitor-recommendations)来帮助确定首先要执行的操作。

1. 在列表中，单击 "建议"。

1. 按照**更正步骤**部分中的说明进行操作。 每个建议都有其自己的一组指令。 以下屏幕截图显示了用于将应用程序配置为仅允许通过 HTTPS 的流量的补救步骤。

    ![建议详细信息](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完成后，将显示一条通知，告知你更新是否成功。

## 快速修复修补<a name="one-click"></a>

快速修复使你能够快速修正针对多个资源的建议。 它仅可用于特定的建议。 快速修复可简化修正，并使你能够快速增加安全分数，提高环境的安全性。

若要实现快速修复修正：

1. 从具有**快速修补程序**的建议列表中，！ 标签上，单击 "建议"。

    [![选择 "快速修复"！](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. 从 "不**正常资源**" 选项卡中，选择要对其实施建议的资源，然后单击 "**修正**"。

    > [!NOTE]
    > 某些列出的资源可能已禁用，因为你没有适当的权限来修改它们。

1. 在确认框中，阅读修正详细信息和影响。

    ![快速修复](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 单击 "**修正**" 后，将打开 "**修正资源**" 窗口中的灰色框。 其中列出了在继续进行快速修复修正时所发生的更改。

1. 如有必要，插入相关参数，并批准修正。

    > [!NOTE]
    > 修正完成后可能需要几分钟时间，才能在 "**正常资源**" 选项卡中查看资源。若要查看更正操作，请检查[活动日志](#activity-log)。

1. 完成后，将显示一条通知，告知你更新是否成功。

## 活动日志中的快速修复修补日志记录<a name="activity-log"></a>

更正操作使用模板部署或 REST 修补程序 API 调用对资源应用配置。 这些操作记录在[Azure 活动日志](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="recommendations-with-quick-fix-remediation"></a>有关快速修复修补的建议

|建议|隐含|
|---|---|
|应启用 SQL server 审核|此操作将在这些服务器及其数据库上启用 SQL 审核。 <br>**注意：** <ul><li>对于所选 SQL server 的每个区域，将创建用于保存审核日志的存储帐户，并由该区域中的所有服务器共享。</li><li>若要确保适当的审核，请不要删除或重命名资源组或存储帐户。</li></ul>|
|应在 SQL 托管实例上启用高级数据安全性|此操作将在选定的 SQL 托管实例上启用 SQL 高级数据安全性（ADS）。 <br>**注意：** <ul><li>对于所选 SQL 托管实例的每个区域和资源组，该区域中的所有实例都将创建并共享用于保存扫描结果的存储帐户。</li><li> 每个 SQL 托管实例的广告收费为 $15。</li></ul>|
|应对 SQL 托管实例启用漏洞评估|此操作将对选定的 SQL 托管实例启用 SQL 漏洞评估。 <br>**注意：**<ul><li>SQL 漏洞评估是 SQL 高级数据安全（ADS）包的一部分。 如果尚未启用广告，将在托管实例上自动启用。</li><li>对于所选 SQL 托管实例的每个区域和资源组，该区域中的所有实例都将创建并共享用于存储扫描结果的存储帐户。</li><li>每个 SQL server 的广告收费为 $15。</li></ul>||
|应在 SQL server 上启用高级数据安全性|此操作将在所选服务器及其数据库上启用高级数据安全性（ADS）。 <br>**注意：**<ul><li>对于所选 SQL server 的每个区域和资源组，该区域中的所有服务器都将创建并共享用于存储扫描结果的存储帐户。 <</li><li>每个 SQL server 的广告收费为 $15。</li></ul>||
|应在 SQL server 上启用漏洞评估|此操作将对所选服务器及其数据库启用 SQL 漏洞评估。 <br>**注意：**<ul><li>SQL 漏洞评估是 SQL 高级数据安全（ADS）包的一部分。 如果尚未启用广告，将在 SQL server 上自动启用。</li><li>对于所选 SQL server 的每个区域和资源组，该区域中的所有实例都将创建并共享用于存储扫描结果的存储帐户。</li><li>每个 SQL server 的广告收费为 $15。</li></ul>||
|应在 SQL 数据库上启用透明数据加密|此操作对所选数据库启用 SQL Database 透明数据加密（TDE）。 <br>**注意**：默认情况下，将使用服务托管的 TDE 密钥。
|应该启用安全传输到存储帐户|此操作会将你的存储帐户安全更新为仅允许安全连接请求。 （HTTPS）。 <br>**注意：**<ul><li>使用 HTTP 的任何请求都将被拒绝。</li><li>使用 Azure 文件服务时，如果不加密，则连接将失败，包括使用 SMB 2.1 的方案、不加密的 SMB 3.0 以及 Linux SMB 客户端的某些种类。 了解详细信息。</li></ul>|
|只能通过 HTTPS 访问 Web 应用程序|此操作会将所选资源的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意：**<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示 "隐私错误"。 因此，拥有自定义域的用户需要验证其是否已设置 SSL 证书。</li><li>请确保数据包和 web 应用程序防火墙保护应用服务，允许 HTTPS 会话转发。</li></ul>|
|应该只能通过 HTTPS 访问函数应用|此操作会将所选资源的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意：**<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示 "隐私错误"。 因此，拥有自定义域的用户需要验证其是否已设置 SSL 证书。</li><li>请确保数据包和 web 应用程序防火墙保护应用服务，允许 HTTPS 会话转发。</li></ul>|
|只能通过 HTTPS 访问 API 应用|此操作会将所选资源的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意：**<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示 "隐私错误"。 因此，拥有自定义域的用户需要验证其是否已设置 SSL 证书。</li><li>请确保数据包和 web 应用程序防火墙保护应用服务，允许 HTTPS 会话转发。</li></ul>|
|应禁用 Web 应用程序的远程调试|此操作将禁用远程调试。|
|应对函数应用禁用远程调试|此操作将禁用远程调试。|
|应为 API 应用禁用远程调试|此操作将禁用远程调试。|
|CORS 不应允许所有资源都能访问你的 Web 应用程序|此操作阻止其他域访问您的 Web 应用程序。 若要允许特定的域，请在 "允许的来源" 字段中输入（用逗号分隔）。 <br>**注意**：如果将此字段留空，则将阻止所有跨源调用。 "参数字段标题：" 允许的来源 "|
|CORS 不应允许所有资源都能访问函数应用|此操作阻止其他域访问函数应用程序。 若要允许特定的域，请在 "允许的来源" 字段中输入（用逗号分隔）。 <br>**注意**：如果将此字段留空，则将阻止所有跨源调用。 "参数字段标题：" 允许的来源 "|
|CORS 不应允许每个资源访问 API 应用|此操作阻止其他域访问 API 应用程序。 若要允许特定的域，请在 "允许的来源" 字段中输入（用逗号分隔）。 <br>**注意**：如果将此字段留空，则将阻止所有跨源调用。 "参数字段标题：" 允许的来源 "|
|应在虚拟机上启用监视代理|此操作将在选定的虚拟机上安装监视代理。 选择要向其报告的代理的工作区。<ul><li>如果更新策略设置为 "自动"，则会将其部署到新的现有实例上。</li><li>如果将更新策略设置为 "手动"，并且想要在现有实例上安装代理，请选中 "复选框" 选项。 [了解详细信息](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|应启用 Key Vault 中的诊断日志|此操作将启用密钥保管库上的诊断日志。 诊断日志和指标保存在选定的工作区中。|
|应启用 Service bus 中的诊断日志|此操作可在服务总线上启用诊断日志。 诊断日志和指标保存在选定的工作区中。|

## <a name="next-steps"></a>后续步骤

本文档演示了如何在安全中心修正建议。 若要了解有关安全中心的详细信息，请参阅以下主题：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
