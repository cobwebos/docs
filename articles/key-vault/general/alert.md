---
title: Azure Key Vault 监视和警报 | Microsoft Docs
description: 创建仪表板以监视密钥保管库的运行状况并配置警报。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 6b179dd2662bf6fa545ea44b723671f1499b9e35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88589004"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Azure Key Vault 的监视和警报

## <a name="overview"></a>概述

一旦开始使用密钥保管库来存储你的生产机密，就必须监视密钥保管库的运行状况，以确保你的服务按预期运转。 如果开始扩展服务，则发送到密钥保管库的请求数将增加。 这可能会增加请求的延迟时间，并且在极端情况下，会导致请求被限制，从而影响服务的性能。 如果密钥保管库发送了异常数量的错误代码，则也需要发出警报，以便你可以快速了解任何访问策略或防火墙配置的问题。 本文档包含以下主题：

+ 要监视的基本 Key Vault 指标
+ 如何配置指标并创建仪表板 
+ 如何根据指定阈值创建警报 

## <a name="basic-key-vault-metrics-to-monitor"></a>要监视的基本 Key Vault 指标

+ 保管库可用性  
+ 保管库饱和度 
+ 服务 API 延迟 
+ 服务 API 命中总计（按活动类型筛选） 
+ 错误代码（按状态代码筛选） 

**保管库可用性**此指标应始终为 100%。这是要监视的重要指标，因为它可以快速向你显示密钥保管库是否遇到中断情况。 

**保管库饱和**密钥保管库每秒可服务的请求数取决于正在执行的操作类型。 某些保管库操作具有较低的每秒请求数阈值。 此指标聚合了跨所有操作类型密钥保管库的总使用量，得出一个百分比值，用来指示当前密钥保管库的使用情况。 有关密钥保管库服务限制的完整列表，请参阅以下文档。 [Azure Key Vault 服务限制](service-limits.md)

**服务 API 延迟** - 此指标显示调用密钥保管库的平均延迟。 尽管密钥保管库可能未超过服务限制，但其高利用率可能会因下游的应用程序出现故障而导致延迟。 

**API 命中总计**此指标显示对密钥保管库进行的所有调用。 这将帮助你识别哪些应用程序正在调用密钥保管库。 

**错误代码**此指标会显示密钥保管库是否遇到异常数量的错误。 如需错误代码的完整列表和故障排除指南，请参阅以下文档。 [Azure Key Vault REST API 错误代码](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>如何配置指标并创建仪表板

1. 登录到 Azure 门户
2. 导航到 Key Vault
3. 在“监视”下选择“指标”  

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-1.png)

4. 将图表的标题更新为想在仪表板上看到的内容。 
5. 选择“作用域”。 在此示例中，我们将选择单个密钥保管库。 
6. 选择指标“保管库总体可用性”，然后选择聚合方式为“平均”  
7. 更新时间范围为最近 24 小时，更新时间粒度为 1 分钟。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-2.png)

8. 为保管库饱和度和服务 API 延迟指标重复上述步骤。 选择“固定到仪表板”，将指标保存到仪表板中。 

> [!IMPORTANT]
> 选择“固定到仪表板”并保存每个配置的指标。 如果离开页面时未保存，则返回该页面时配置更改将会丢失。 

9. 若要监视密钥保管库上所有类型的操作，请使用“服务 API 命中总计”指标，然后选择“按活动类型应用拆分”  

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-3.png)

10. 若要监视密钥保管库上的错误代码，请使用“服务 API 结果总计”指标，然后选择“按活动类型应用拆分”  

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-4.png)

现在，你将看到一个类似下图的仪表板。 可以单击每个磁贴右上方的 3 个点，并根据需要重新排列和调整磁贴大小。 

保存并发布仪表板后，它将在 Azure 订阅中创建新的资源。 可以通过搜索“共享仪表板”随时查看它。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>如何配置 Key Vault 上的警报 

本部分将介绍如何在密钥保管库上配置警报，这样就可以在密钥保管库处于不正常状态时提醒团队立即采取措施。 可以配置发送电子邮件的警报，最好是发送到团队 DL，触发事件网格通知，或者拨打电话号码或发信息。 可以选择基于固定值的静态警报，也可以选择动态警报，在一定时间范围内，如果受监视的指标超过密钥保管库的平均限制一定次数时发出警报。 

> [!IMPORTANT]
> 请注意，新配置的警报需要长达 10 分钟的时间才能开始发送通知。 

### <a name="configure-an-action-group"></a>配置操作组 

操作组是可配置的通知和属性列表。

1. 登录到 Azure 门户
2. 在搜索框中搜索“警报”
3. 选择“管理操作”

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-6.png)

4. 选择“+ 添加操作组”

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-7.png)

5. 为操作组选择“操作类型”。 本示例中，将创建电子邮件警报。

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-9.png)

6. 单击页面底部的“确定”。 已成功创建操作组。 

现在，已配置了一个操作组，接下来我们将配置密钥保管库警报阈值。 

### <a name="configure-alert-thresholds"></a>配置警报阈值 

1. 在 Azure 门户中选择密钥保管库资源，然后选择“监视”下的“警报” 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-10.png)

2. 选择“新建警报规则”

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-11.png)

3. 选择警报规则的作用域。 可以选择一个或多个保管库。 

> [!IMPORTANT]
> 请注意，如果选择多个保管库作为警报作用域，则所有选定的保管库必须位于同一区域中。 不同区域中的保管库需要配置单独的警报规则。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-12.png)

4. 为警报选择条件。 可以选择以下任何信号，并为警报定义逻辑。 Key Vault 团队建议配置以下警报阈值。 

    + Key Vault 可用性低于 100%（静态阈值）
    + Key Vault 延迟大于 500ms（静态阈值） 
    + 保管库整体饱和度大于 75%（静态阈值） 
    + 保管库整体饱和度超过平均数（动态阈值）
    + 错误代码总计高于平均值（动态阈值） 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>示例 1：为延迟配置静态警报阈值

选择“整体服务 API 延迟”作为信号名称
> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-14.png)

请查看以下配置参数。

+ 将阈值设置为“静态” 
+ 将运算符设置为“大于”
+ 将聚合类型设置为“平均”
+ 将阈值设置为“500”
+ 将聚合期间设置为“5 分钟”
+ 将评估频率设置为“1 分钟”
+ 选择“完成”  

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>示例 2：为保管库饱和度配置动态警报阈值 

使用动态警报时，可以看到所选密钥保管库的历史数据。 蓝色区域表示密钥保管库的平均使用情况。 红色区域显示可能触发警报的高峰，前提是满足警报配置中的其他条件。 红点显示在聚合时间范围内满足警报条件的冲突实例。 可以设置在一定时间内发生一定次数的冲突后触发警报。 如果不想包含过去的数据，如下所示在高级设置中有一个选项可以排除旧数据。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-16.png)

请查看以下配置参数。

+ 将阈值设置为“动态” 
+ 将运算符设置为“大于”
+ 将聚合类型设置为“平均”
+ 将阈值敏感度设置为“中等”
+ 将聚合期间设置为“5 分钟”
+ 将评估频率设置为“1 分钟”
+ “可选”配置高级设置 
+ 选择“完成”

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-17.png)

5. 添加已配置的操作组

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-18.png)

6. 启用警报并分配严重性

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-19.png)

7. 创建警报 


## <a name="next-steps"></a>后续步骤

恭喜，现在已成功创建了一个用于密钥保管库的监视仪表板并配置了警报！ 完成上述所有步骤后，当密钥保管库满足配置的警报条件时，就会收到电子邮件警报。 下面显示了一个示例。 使用本文中设置的工具积极监视密钥保管库的运行状况。 

### <a name="example-email-alert"></a>示例电子邮件警报 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-20.png)
