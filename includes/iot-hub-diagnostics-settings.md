---
title: include 文件
description: include 文件
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 6f7772eb7f2c500bbb58c391b1bc4b7a73141699
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675749"
---
### <a name="enable-logging-with-diagnostics-settings"></a>通过诊断设置启用日志记录

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。

2. 选择“诊断设置”。

3. 选择“启用诊断”。

   ![启用诊断](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. 为诊断设置提供名称。

5. 选择希望将日志发送到的目标。 可选择以下三个选项的任意组合：

   * 存档到存储帐户
   * 流式传输到事件中心
   * 发送到 Log Analytics

6. 选择要监视的操作，并为这些操作启用日志。 诊断设置可以报告的操作如下：

   * 连接
   * 设备遥测
   * 云到设备的消息
   * 设备标识操作
   * 文件上传
   * 消息路由
   * 云到设备孪生操作
   * 设备到云孪生操作
   * 孪生操作
   * 作业操作
   * 直接方法  
   * 分布式跟踪（预览版）
   * 配置
   * 设备流
   * 设备指标

6. 保存新设置。 

如果想要通过 PowerShell 打开诊断设置，请使用以下代码：

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

新设置在大约 10 分钟后生效。 在此之后，日志将出现在“诊断设置”边栏选项卡上配置的存档目标中。 有关配置诊断的详细信息，请参阅[从 Azure 资源收集和使用日志数据](../articles/azure-monitor/platform/diagnostic-logs-overview.md)。