---
title: 在 Azure IoT Central 中使用 Webhook 触发 Azure Functions
description: 创建每次在 Azure IoT 中心中触发规则时运行的函数应用。
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c80b007c3c9c1a35540e690554603a5ae8f16d62
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58284623"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>在 Azure IoT Central 中使用 Webhook 触发 Azure Functions

本主题适用于构建者和管理员。

使用 Azure Functions 对来自 IoT Central 规则的 Webhook 输出运行无服务器代码。 无需预配 VM 或发布 web 应用以使用 Azure Functions，但可以改为运行此无服务器代码。 先使用 Azure Functions 转换 Webhook 有效负载，然后再将它发送到最终目标，如 SQL 数据库或事件网格。

## <a name="prerequisites"></a>必备组件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="how-to-connect-azure-functions"></a>如何连接 Azure Functions

1. [在 Azure 门户中创建新的 function app](https://ms.portal.azure.com/#create/Microsoft.FunctionApp)。

    ![在 Azure 门户中创建新的 function app](media/howto-trigger-azure-functions/createfunction.png)

2. 展开 function app，然后选择 **+ 按钮**旁边函数。 如果此函数在函数应用中的第一个，请选择**门户中**作为开发环境，然后选择**继续**。

    ![在函数应用中选择自定义函数](media/howto-trigger-azure-functions/customfunction.png)

3. 选择**Webhook + API**模板，然后选择**创建**。 本主题使用基于.NET 的 Azure 函数。

    ![选择通用 Webhook 触发器](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. 在新函数中，选择 **<> / 获取函数 URL**，然后复制并保存该值。 此值将用于配置 webhook。

    ![获取函数的 URL](media/howto-trigger-azure-functions/getfunctionurl.png)

4. 在 IoT Central 中，导航到要连接到函数应用的规则。

5. 添加 Webhook 操作。 输入“显示名称”，并将之前复制的函数 URL 粘贴到“回调 URL”中。

    ![向回调 URL 字段中输入函数 URL](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. 保存规则。 现在当触发规则时，webhook 调用要运行的函数应用。 在 function app 中，您可以选择**监视器**以查看该函数的调用历史记录。 可以使用 App Insights 或经典视图查看历史记录。

    ![监视函数的调用历史记录](media/howto-trigger-azure-functions/monitorfunction.PNG)

有关详细信息，请访问有关[创建由泛型 Webhook 触发的函数](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)的 Azure Functions 文章。

## <a name="next-steps"></a>后续步骤
现在，你已了解如何设置和使用 Webhook，建议下一步是探索[在 Microsoft Flow 中生成工作流](howto-add-microsoft-flow.md)。
