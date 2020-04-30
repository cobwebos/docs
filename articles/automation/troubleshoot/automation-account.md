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
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679383"
---
# <a name="troubleshoot-the-automation-account"></a>自动化帐户疑难解答

本文讨论在使用自动化帐户时可能会遇到的问题的解决方案。 以下各节重点介绍了每个特定的错误消息和可能的解决方法。 有关自动化帐户的一般信息，请参阅[创建 Azure 帐户](../automation-quickstart-create-account.md)。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>方案：无法注册订阅的自动化资源提供程序

### <a name="issue"></a>问题

在自动化帐户中使用管理解决方案时，遇到以下错误：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

未在订阅中注册 Automation 资源提供程序。

### <a name="resolution"></a>解决方法

若要注册自动化资源提供程序，请在 Azure 门户中执行以下步骤：

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 导航到 **"订阅"，然后从**"订阅" 页中选择你的订阅。   

3. 在 "**设置**" 下，选择 "**资源提供程序**"。

4. 在资源提供程序列表中，验证是否注册了**Microsoft. Automation**资源提供程序。

5. 如果提供程序未列出，请按照[解决资源提供程序注册的错误](/azure/azure-resource-manager/resource-manager-register-provider-errors)中所述进行注册。

## <a name="next-steps"></a>后续步骤

如果你没有看到你的问题或无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。