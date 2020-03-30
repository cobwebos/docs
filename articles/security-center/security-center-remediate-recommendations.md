---
title: Azure 安全中心的修复建议 |微软文档
description: 本文介绍如何在 Azure 安全中心中修复建议，以保护资源和遵守安全策略。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603498"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure 安全中心的修正建议

建议为您提供有关如何更好地保护资源的建议。 您可以按照建议中提供的修正步骤来实现建议。

## <a name="remediation-steps"></a>补救步骤<a name="remediation-steps"></a>

在审阅所有建议后，首先决定修复哪一个。 我们建议您使用[安全分数影响](security-center-recommendations.md#monitor-recommendations)来帮助确定优先操作。

1. 从列表中单击建议。

1. 按照“修正步骤”部分**** 中的说明进行操作。 每个建议都有自己的一组说明。 以下屏幕截图显示了用于将应用程序配置为仅允许通过 HTTPS 的流量的修正步骤。

    ![建议详细信息](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完成后，将显示一条通知，通知您修复是否成功。

## <a name="quick-fix-remediation"></a>快速修复修复<a name="one-click"></a>

通过快速修复，您可以快速修复对多个资源的建议。 它仅适用于具体建议。 快速修复简化了补救，使您能够快速提高安全分数，提高环境的安全性。

要实现快速修复修正：

1. 从具有**快速修复**的建议列表中！ 标签，单击建议。

    [![选择快速修复！](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. 从 **"不正常资源**"选项卡中，选择要实现建议的资源，然后单击"**修复**"。

    > [!NOTE]
    > 某些列出的资源可能会被禁用，因为您没有修改它们的适当权限。

1. 在确认框中，阅读修复详细信息和含义。

    ![快速修复](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 影响在 **"修复资源"** 窗口中的灰色框中列出，该窗口中单击"修复 **"** 后将打开该含义。 它们列出在继续快速修复修正时发生的更改。

1. 如有必要，插入相关参数，并批准修正。

    > [!NOTE]
    > 修复完成后可能需要几分钟才能在 **"正常资源**"选项卡中查看资源。要查看修正操作，请检查[活动日志](#activity-log)。

1. 完成后，将显示一条通知，通知您修复是否成功。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>快速修复活动日志中的修正日志记录<a name="activity-log"></a>

修正操作使用模板部署或 REST PATCH API 调用在资源上应用配置。 这些操作记录在[Azure 活动日志](../azure-resource-manager/management/view-activity-logs.md)中。


## <a name="recommendations-with-quick-fix-remediation"></a>具有快速修复修复的建议

|建议|意义|
|---|---|
|应启用 SQL 服务器上的审核|此操作将启用对这些服务器及其数据库的 SQL 审核。 <br>**注意：** <ul><li>对于所选 SQL 服务器的每个区域，该区域中的所有服务器都将创建并共享用于保存审核日志的存储帐户。</li><li>为确保正确审核，请勿删除或重命名资源组或存储帐户。</li></ul>|
|应在 SQL 托管实例上启用高级数据安全性|此操作将在选定的 SQL 托管实例上启用 SQL 高级数据安全 （ADS）。 <br>**注意：** <ul><li>对于所选 SQL 托管实例的每个区域和资源组，将创建用于保存扫描结果的存储帐户，并由该区域中的所有实例共享。</li><li> ADS 收费，每个 SQL 托管实例为 15 美元。</li></ul>|
|应对 SQL 托管实例启用漏洞评估|此操作将启用所选 SQL 托管实例的 SQL 漏洞评估。 <br>**注意：**<ul><li>SQL 漏洞评估是 SQL 高级数据安全 （ADS） 包的一部分。 如果 AD 尚未启用，则会自动在托管实例上启用它。</li><li>对于所选 SQL 托管实例的每个区域和资源组，将创建存储扫描结果的存储帐户，并由该区域中的所有实例共享。</li><li>ADS 每台 SQL 服务器收费 15 美元。</li></ul>||
|应在 SQL 服务器上启用高级数据安全性|此操作将在这些选定的服务器及其数据库上启用高级数据安全 （ADS）。 <br>**注意：**<ul><li>对于所选 SQL 服务器的每个区域和资源组，该区域中的所有服务器都将创建并共享用于存储扫描结果的存储帐户 <。</li><li>ADS 每台 SQL 服务器收费 15 美元。</li></ul>||
|应在 SQL 服务器上启用漏洞评估|此操作将启用这些选定服务器及其数据库上的 SQL 漏洞评估。 <br>**注意：**<ul><li>SQL 漏洞评估是 SQL 高级数据安全 （ADS） 包的一部分。 如果 AD 尚未启用，则 SQL 服务器上将自动启用它。</li><li>对于所选 SQL 服务器的每个区域和资源组，将创建存储扫描结果的存储帐户，并由该区域中的所有实例共享。</li><li>ADS 每台 SQL 服务器收费 15 美元。</li></ul>||
|应启用 SQL 数据库上的透明数据加密|此操作支持所选数据库上的 SQL 数据库透明数据加密 （TDE）。 <br>**注意**：默认情况下，将使用服务管理的 TDE 密钥。
|应该启用安全传输到存储帐户|此操作会更新存储帐户安全性，使其仅允许安全连接的请求。 （HTTPS）。 <br>**注意：**<ul><li>使用 HTTP 的任何请求都将被拒绝。</li><li>使用 Azure 文件服务时，没有加密的连接将失败，包括使用 SMB 2.1、没有加密的 SMB 3.0 以及 Linux SMB 客户端的某些风格。 了解详细信息。</li></ul>|
|只能通过 HTTPS 访问 Web 应用程序|此操作将所选资源上的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意：**<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示"隐私错误"。 因此，具有自定义域的用户需要验证自己已设置 SSL 证书。</li><li>确保数据包和 Web 应用程序防火墙保护应用服务，允许 HTTPS 会话转发。</li></ul>|
|应该只能通过 HTTPS 访问函数应用|此操作将所选资源上的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意：**<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示"隐私错误"。 因此，具有自定义域的用户需要验证自己已设置 SSL 证书。</li><li>确保数据包和 Web 应用程序防火墙保护应用服务，允许 HTTPS 会话转发。</li></ul>|
|只能通过 HTTPS 访问 API 应用|此操作将所选资源上的所有流量从 HTTP 重定向到 HTTPS。 <br>**注意：**<ul><li>没有 SSL 证书的 HTTPS 终结点将在浏览器中显示"隐私错误"。 因此，具有自定义域的用户需要验证自己已设置 SSL 证书。</li><li>确保数据包和 Web 应用程序防火墙保护应用服务，允许 HTTPS 会话转发。</li></ul>|
|应禁用 Web 应用程序的远程调试|此操作将禁用远程调试。|
|应对函数应用禁用远程调试|此操作将禁用远程调试。|
|应为 API 应用禁用远程调试|此操作将禁用远程调试。|
|CORS 不应允许所有资源都能访问你的 Web 应用程序|此操作阻止其他域访问 Web 应用程序。 要允许特定域，请在"允许的原点"字段中输入它们（用逗号分隔）。 <br>**注意**：将字段留空将阻止所有跨源调用。|
|CORS 不应允许所有资源都能访问函数应用|此操作阻止其他域访问函数应用程序。 要允许特定域，请在"允许的原点"字段中输入它们（用逗号分隔）。 <br>**注意**：将字段留空将阻止所有跨源调用。|
|CORS 不应允许所有资源都能访问 API 应用|此操作阻止其他域访问 API 应用程序。 要允许特定域，请在"允许的原点"字段中输入它们（用逗号分隔）。 <br>**注意**：将字段留空将阻止所有跨源调用。|
|应在虚拟机上启用监视代理|此操作在选定的虚拟机上安装监视代理。 选择要向座席报告的工作区。<ul><li>如果更新策略设置为自动，它将部署在新的现有实例上。</li><li>如果更新策略设置为手动，并且希望在现有实例上安装代理，请选择复选框选项。 [了解详细信息](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|应启用 Key Vault 中的诊断日志|此操作支持密钥保管库上的诊断日志。 诊断日志和指标保存在所选工作区中。|
|应启用服务总线中的诊断日志|此操作可在服务总线上启用诊断日志。 诊断日志和指标保存在所选工作区中。|

## <a name="next-steps"></a>后续步骤

在本文档中，您被显示如何在安全中心修复建议。 要了解有关安全中心的更多内容，请参阅以下主题：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
