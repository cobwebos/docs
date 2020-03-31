---
title: 自动化帐户故障排除
description: 了解如何对 Azure 帐户进行故障排除和解决问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c66b1728144b8517f6ac444059b3a8def956c6e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301000"
---
# <a name="automation-account-troubleshooting"></a>自动化帐户故障排除

本文讨论有关使用自动化帐户时可能会遇到的问题的解决方案。 以下各节重点介绍特定错误消息和每个错误消息的可能解决方法。 有关自动化帐户的一般信息，请参阅[创建 Azure 帐户](../automation-quickstart-create-account.md)。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>方案：无法注册订阅的自动化资源提供程序

### <a name="issue"></a>问题

当您在自动化帐户中使用管理解决方案时，您会遇到以下错误：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

未在订阅中注册自动化资源提供程序。

### <a name="resolution"></a>解决方法

要注册自动化资源提供程序，请在 Azure 门户中执行以下步骤：

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 导航到**订阅**并从"订阅"页面选择订阅。   

3. 在 **"设置"** 下，选择 **"资源提供程序**"。

4. 从资源提供程序列表中验证**Microsoft.自动化**资源提供程序是否注册。

5. 如果未列出，请按照["解决资源提供程序注册错误"](/azure/azure-resource-manager/resource-manager-register-provider-errors)中的步骤注册**Microsoft.自动化**提供程序。

## <a name="next-steps"></a>后续步骤

如果您没有看到问题或无法解决问题，请尝试以下渠道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。