---
title: Azure 自动化帐户疑难解答
description: 了解如何对 Azure 帐户进行故障排除和解决问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864124"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Azure 自动化帐户故障排除

本文介绍使用 Azure 自动化帐户时可能会遇到的问题的解决方案。 有关自动化帐户的一般信息，请参阅[创建 Azure 帐户](../automation-quickstart-create-account.md)。

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

2. 中转到 "**订阅**"，然后选择订阅。   

3. 在 "**设置**" 下，选择 "**资源提供程序**"。

4. 在资源提供程序列表中，验证是否注册了**Microsoft. Automation**资源提供程序。

5. 如果提供程序未列出，请按照[解决资源提供程序注册的错误](/azure/azure-resource-manager/resource-manager-register-provider-errors)中所述进行注册。

## <a name="next-steps"></a>后续步骤

如果本文未解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 连接到[@AzureSupport](https://twitter.com/azuresupport)。 这是将 Azure 社区连接到正确资源的官方 Microsoft Azure 帐户：答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。