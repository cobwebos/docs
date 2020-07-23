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
ms.openlocfilehash: 9beb617ed8626b1fda1c9db98d626ca70ee01755
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042911"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>在 Azure 安全中心修正建议

建议提供了有关如何更好地保护资源的意见。 可以按照建议中提供的修正步骤来实施建议。

## <a name="remediation-steps"></a>修正步骤<a name="remediation-steps"></a>

在查看完所有建议后，决定先修正哪一建议。 建议使用[安全分数影响](security-center-recommendations.md#monitor-recommendations)来帮助确定首先要执行的操作。

1. 从列表中单击建议。

1. 按照“修正步骤”部分中的说明进行操作。 每个建议都有其自己的一组指令。 以下屏幕截图显示了一些修正步骤，这些步骤用于将应用程序配置为仅允许通过 HTTPS 传输的流量。

    ![建议详细信息](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完成后，将显示一条通知，告知你修正是否成功。

## <a name="quick-fix-remediation"></a>“快速修复”修正<a name="one-click"></a>

快速修复使你能够针对多个资源快速修正某个建议。 它仅可用于特定的建议。 快速修复可简化修正，使你能够快速增加你的安全分数，提高环境的安全性。

若要实现“快速修复”修正，请执行以下操作：

1. 在带有“快速修复!”标签的建议的列表中， 单击建议。

    [![选择“快速修复!”](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. 从“不正常的资源”选项卡上，选择要对其实施建议的资源，然后单击“修正”。

    > [!NOTE]
    > 列出的某些资源可能已禁用，因为你没有相应的权限，无法修改它们。

1. 在确认框中，阅读修正详细信息和影响。

    ![快速修复](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 影响在单击“修正”后打开的“修正资源”窗口的灰色框中列出。 其中列出了在继续进行“快速修复”修正时会发生哪些更改。

1. 请插入相关参数（如有必要），并批准修正。

    > [!NOTE]
    > 修正完成后可能需要几分钟时间，才能在“正常的资源”选项卡中看到资源。若要查看修正操作，请查阅[活动日志](#activity-log)。

1. 完成后，将显示一条通知，告知你修正是否成功。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>活动日志中的“快速修复”修正日志记录 <a name="activity-log"></a>

修正操作使用模板部署或 REST PATCH API 调用，将配置应用于资源。 这些操作记录在 [Azure 活动日志](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="recommendations-with-quick-fix-remediation"></a>“快速修复”修正建议

|建议|影响|
|---|---|
|应启用对 SQL 数据库的审核|此操作将在这些服务器及其数据库上启用 SQL 审核。 <br>**注意**： <ul><li>对于所选 SQL 数据库的每个区域，将创建用于保存审核日志的存储帐户，并由该区域中的所有服务器共享。</li><li>为了确保正确审核，请勿删除或重命名资源组或存储帐户。</li></ul>|
|应在 SQL 托管实例上启用高级数据安全|此操作将在选定的 SQL 托管实例上启用 SQL 高级数据安全性（ADS）。 <br>**注意**： <ul><li>对于所选 SQL 托管实例的每个区域和资源组，该区域中的所有实例都将创建并共享用于保存扫描结果的存储帐户。</li><li> 每个 SQL 托管实例按 $15 对广告收费。</li></ul>|
|应在 SQL 托管实例上启用漏洞评估|此操作将在所选 SQL 托管实例上启用 SQL 漏洞评估。 <br>**注意**：<ul><li>SQL 漏洞评估是 SQL 高级数据安全 (ADS) 包的一部分。 如果尚未启用 ADS，那么它将在托管实例上自动启用。</li><li>对于所选 SQL 托管实例的每个区域和资源组，该区域中的所有实例都将创建并共享用于存储扫描结果的存储帐户。</li><li>每个 SQL 数据库的广告收费为 $15。</li></ul>||
|应在 SQL 数据库上启用高级数据安全性|此操作将在这些选定服务器及其数据库上启用高级数据安全 (ADS)。 <br>**注意**：<ul><li>对于所选 SQL 数据库的每个区域和资源组，该区域中的所有服务器都将创建并共享用于存储扫描结果的存储帐户。 <</li><li>每个 SQL 数据库的广告收费为 $15。</li></ul>||
|应在 SQL 数据库上启用漏洞评估|此操作将在这些选定服务器及其数据库上启用 SQL 漏洞评估。 <br>**注意**：<ul><li>SQL 漏洞评估是 SQL 高级数据安全 (ADS) 包的一部分。 如果尚未启用广告，将在 SQL 数据库上自动启用。</li><li>对于所选 SQL 数据库的每个区域和资源组，该区域中的所有实例都将创建并共享用于存储扫描结果的存储帐户。</li><li>每个 SQL 数据库的广告收费为 $15。</li></ul>||
|应在 SQL 数据库上启用透明数据加密|此操作在选定数据库上启用 SQL 数据库透明数据加密 (TDE)。 <br>**注意**：默认情况下，将使用服务管理的 TDE 密钥。
|应该启用安全传输到存储帐户|此操作将你的存储帐户安全设置更新为，仅允许来自安全连接的请求 (HTTPS)。 <br>**注意**：<ul><li>使用 HTTP 的任何请求都将被拒绝。</li><li>使用 Azure 文件服务时，在不加密的情况下进行连接将失败，包括在不加密的情况下使用 SMB 2.1、SMB 3.0 的方案，以及某些风格的 Linux SMB 客户端。 了解详细信息。</li></ul>|
|只能通过 HTTPS 访问 Web 应用程序|此操作会将所选资源上的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意**：<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示 "隐私错误"。 因此，拥有自定义域的用户需要验证他们是否已设置 SSL 证书。</li><li>请确保用于保护应用服务的数据包和 Web 应用程序防火墙允许 HTTPS 会话转发。</li></ul>|
|应该只能通过 HTTPS 访问函数应用|此操作会将所选资源上的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意**：<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示 "隐私错误"。 因此，拥有自定义域的用户需要验证他们是否已设置 SSL 证书。</li><li>请确保用于保护应用服务的数据包和 Web 应用程序防火墙允许 HTTPS 会话转发。</li></ul>|
|只能通过 HTTPS 访问 API 应用|此操作会将所选资源上的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意**：<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示 "隐私错误"。 因此，拥有自定义域的用户需要验证他们是否已设置 SSL 证书。</li><li>请确保用于保护应用服务的数据包和 Web 应用程序防火墙允许 HTTPS 会话转发。</li></ul>|
|应禁用 Web 应用程序的远程调试|此操作将禁用远程调试。|
|应对函数应用禁用远程调试|此操作将禁用远程调试。|
|应为 API 应用禁用远程调试|此操作将禁用远程调试。|
|CORS 不应允许所有资源都能访问你的 Web 应用程序|此操作将阻止其他域访问你的 Web 应用程序。 若要允许特定的域，请在“允许的源”字段中输入它们（用逗号分隔）。 <br>**注意**：如果将此字段留空，则将阻止所有跨源调用。 "参数字段标题：" 允许的来源 "|
|CORS 不应允许所有资源都能访问函数应用|此操作将阻止其他域访问你的函数应用程序。 若要允许特定的域，请在“允许的源”字段中输入它们（用逗号分隔）。 <br>**注意**：如果将此字段留空，则将阻止所有跨源调用。 "参数字段标题：" 允许的来源 "|
|CORS 不应允许所有资源访问 API 应用|此操作将阻止其他域访问你的 API 应用程序。 若要允许特定的域，请在“允许的源”字段中输入它们（用逗号分隔）。 <br>**注意**：如果将此字段留空，则将阻止所有跨源调用。 "参数字段标题：" 允许的来源 "|
|应在虚拟机上启用监视代理|此操作在所选虚拟机上安装监视代理。 选择代理要向其报告的工作区。<ul><li>如果更新策略设置为“自动”，它会部署到新的现有实例上。</li><li>如果更新策略设置为“手动”，并且你想要在现有实例上安装代理，请选中相应的复选框选项。 [了解详细信息](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|应启用 Key Vault 中的诊断日志|此操作在密钥保管库上启用诊断日志。 诊断日志和指标保存在选定工作区中。|
|应启用服务总线中的诊断日志|此操作在服务总线上启用诊断日志。 诊断日志和指标保存在选定工作区中。|

## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍了如何在安全中心修正建议。 若要了解有关安全中心的详细信息，请参阅以下主题：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
