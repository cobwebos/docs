---
title: 排查 Azure 自动化帐户问题
description: 本文介绍如何排查和解决 Azure 帐户问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bebc89514da7f28015da5cb145ae50bf3fccdb6a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680081"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>排查 Azure 自动化帐户问题

本文介绍使用 Azure 自动化帐户时可能会遇到的问题的解决方案。 有关自动化帐户的常规信息，请参阅 [Azure 自动化帐户身份验证概述](../automation-security-overview.md)。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>场景：无法为订阅注册自动化资源提供程序

### <a name="issue"></a>问题

在自动化帐户中使用管理功能（例如更新管理）时，遇到以下错误：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

未在订阅中注册自动化资源提供程序。

### <a name="resolution"></a>解决方法

若要注册自动化资源提供程序，请在 Azure 门户中执行以下步骤：

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 转到“订阅”，选择你的订阅。   

3. 在“设置”下，选择“资源提供程序”。

4. 在资源提供程序列表中，验证是否注册了 Microsoft.Automation 资源提供程序。

5. 如果未列出提供程序，请按照[解决资源提供程序注册错误](/azure/azure-resource-manager/resource-manager-register-provider-errors)中所述的方式进行注册。

## <a name="next-steps"></a>后续步骤

如果本文未解决你的问题，请尝试通过以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 联系 [@AzureSupport](https://twitter.com/azuresupport)。 这是官方的 Microsoft Azure 帐户，它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。