---
title: Azure 密钥保管库监视和警报 |微软文档
description: 创建仪表板以监视密钥保管库的运行状况并配置警报。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726938"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>监视 Azure 密钥保管库并发出警报

## <a name="overview"></a>概述

开始使用密钥保管库存储生产机密后，请务必监视密钥保管库的运行状况，以确保服务按预期运行。 当您开始扩展服务时，发送到密钥保管库的请求数量将上升。 这可能会增加请求的延迟，在极端情况下，可能会导致请求受到限制，从而影响服务的性能。 如果密钥保管库发送了异常数量的错误代码，则还需要收到警报，以便快速通知您任何访问策略或防火墙配置问题。 本文档将涵盖以下主题：

+ 要监视的基本密钥保管库指标
+ 如何配置指标和创建仪表板 
+ 如何在指定阈值下创建警报 

## <a name="basic-key-vault-metrics-to-monitor"></a>要监视的基本密钥保管库指标

+ 保险库可用性  
+ 保险库饱和度 
+ 服务 API 延迟 
+ 服务 API 命中总数（按活动类型筛选） 
+ 错误代码（按状态代码筛选） 

**保管库可用性**- 此指标应始终为 100%，这是要监视的重要指标，因为它可以快速显示密钥保管库是否遇到中断。 

**保管库饱和**度 – 密钥保管库每秒可提供的请求数基于正在执行的操作类型。 某些保管库操作具有较低的每秒请求阈值。 此指标聚合所有工序类型中密钥保管库的总使用情况，得出指示当前密钥保管库使用情况的百分比值。 有关密钥保管库服务限制的完整列表，请参阅以下文档。 [Azure 密钥保管库服务限制](service-limits.md)

**服务 API 延迟**- 此指标显示对密钥保管库的调用的平均延迟。 尽管密钥保管库可能处于服务限制范围内，但密钥保管库的高利用率可能会引入延迟，从而导致下游应用程序失败。 

**API 总命中**- 此指标显示对密钥保管库进行的所有调用。 这将有助于您确定哪些应用程序正在调用密钥保管库。 

**错误代码**– 此指标将显示密钥保管库是否遇到异常数量的错误。 有关错误代码和故障排除指南的完整列表，请参阅以下文档。 [Azure Key Vault REST API 错误代码](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>如何配置指标和创建仪表板

1. 登录到 Azure 门户
2. 导航到密钥保管库
3. 在**监视**下选择**指标** 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-1.png)

4. 将图表的标题更新到您希望在仪表板上看到的内容。 
5. 选择范围。 在此示例中，我们将选择单个密钥保管库。 
6. 选择指标**整体保管库可用性**和聚合**平均值** 
7. 将时间范围更新为"最近 24 小时"，并将时间粒度更新为 1 分钟。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-2.png)

8. 对保管库饱和度和服务 API 延迟指标重复上述步骤。 选择 **"固定到仪表板**"可将指标保存到仪表板中。 

> [!IMPORTANT]
> 选择"固定到仪表板"并保存您配置的每个指标。 如果离开页面并在不保存的情况下返回到该页面，则配置更改将丢失。 

9. 要监视密钥保管库上的所有类型的操作，请使用 **"总服务 API 命中**指标"，然后选择**按活动类型应用拆分**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-3.png)

10. 要监视密钥保管库上的错误代码，请使用 **"总服务 API 结果**指标"，然后选择**按活动类型应用拆分**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-4.png)

现在，您将有一个如下所示的仪表板。 您可以单击每个磁贴右上角的 3 个点，并根据需要重新排列和调整磁贴的大小。 

保存并发布仪表板后，它将在 Azure 订阅中创建新资源。 通过搜索"共享仪表板"，您可以随时看到它。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>如何配置密钥保管库上的警报 

本节将介绍如何在密钥保管库上配置警报，以便提醒您的团队，如果密钥保管库处于不正常状态，请立即采取行动。 您可以配置发送电子邮件的警报，最好是向团队 DL 发送、触发事件网格通知或呼叫或发送电话号码。 您还可以根据固定值选择静态警报，或者选择动态警报，如果受监视指标在定义的时间范围内超过密钥保管库的平均限制一定次数，则动态警报将提醒您。 

> [!IMPORTANT]
> 请注意，新配置的警报最多可能需要 10 分钟才能开始发送通知。 

### <a name="configure-an-action-group"></a>配置操作组 

操作组是通知和属性的可配置列表。

1. 登录到 Azure 门户
2. 在搜索框中搜索**警报**
3. 选择 **"管理操作"**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-6.png)

4. 选择 **= 添加操作组**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-7.png)

5. 选择操作组**的操作类型**。 在此示例中，我们将创建电子邮件警报。

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-9.png)

6. 单击页面底部的“确定”。**** 您已成功创建一个操作组。 

现在，您已配置了操作组，我们将配置密钥保管库警报阈值。 

### <a name="configure-alert-thresholds"></a>配置警报阈值 

1. 在 Azure 门户中选择密钥保管库资源，然后选择 **"监视**下的**警报**"

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-10.png)

2. 选择**新的警报规则**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-11.png)

3. 选择警报规则的范围。 您可以选择单个保管库或多个保管库。 

> [!IMPORTANT]
> 请注意，当您为警报范围选择多个保管库时，所有选定的保管库必须位于同一区域。 您必须为不同区域中的保管库配置单独的警报规则。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-12.png)

4. 选择警报的条件。 您可以选择以下任何信号，并定义警报的逻辑。 密钥保管库团队建议配置以下警报阈值。 

    + 密钥保管库可用性降至 100% 以下（静态阈值）
    + 密钥保管库延迟大于 500 毫秒（静态阈值） 
    + 总体保管库饱和度大于 75%（静态阈值） 
    + 总体保管库饱和度超过平均值（动态阈值）
    + 总误差码高于平均值（动态阈值） 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>示例 1：配置延迟的静态警报阈值

选择**整体服务 API 延迟**作为信号名称
> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-14.png)

请参阅以下配置参数。

+ 将阈值设置为**静态** 
+ 将运算符设置为**大于**
+ 将聚合类型设置为**平均值**
+ 将阈值设置为**500**
+ 将聚合周期设置为**5 分钟**
+ 将评估频率设置为**1 分钟**
+ 选择“完成”   

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>示例 2：为保管库饱和配置动态警报阈值 

使用动态警报时，您将能够查看所选密钥保管库的历史数据。 蓝色区域表示密钥保管库的平均使用情况。 红色区域显示将触发警报的尖峰，前提是满足警报配置中的其他条件。 红点显示在聚合时间窗口期间满足警报条件的违规实例。 您可以在设定的时间内设置一定数量的违规后触发警报。 如果不想包含过去的数据，则可以在高级设置中排除下面的旧数据。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-16.png)

请参阅以下配置参数。

+ 将阈值设置为**动态** 
+ 将运算符设置为**大于**
+ 将聚合类型设置为**平均值**
+ 将阈值敏感度设置为**中等**
+ 将聚合周期设置为**5 分钟**
+ 将评估频率设置为**1 分钟**
+ **可选**配置高级设置 
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

恭喜您，您现在已成功创建了监视仪表板，并为密钥保管库配置了警报！ 遵循上述所有步骤后，当密钥保管库满足您配置的警报条件时，应收到电子邮件警报。 下面显示了一个示例。 使用本文中设置的工具主动监视密钥保管库的运行状况。 

### <a name="example-email-alert"></a>电子邮件警报示例 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-20.png)
