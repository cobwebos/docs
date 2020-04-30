---
title: Azure Key Vault 监视和警报 |Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726938"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Azure Key Vault 的监视和警报

## <a name="overview"></a>概述

一旦你开始使用密钥保管库来存储你的生产机密，就必须监视密钥保管库的运行状况，以确保你的服务按预期方式运行。 开始扩展服务时，发送到密钥保管库的请求数将增加。 这可能会增加请求的延迟时间，在极端情况下，会导致请求受到限制，从而影响服务的性能。 如果密钥保管库发送了异常数量的错误代码，则还需要发出警报，以便你可以快速通知任何访问策略或防火墙配置问题。 本文档将介绍以下主题：

+ 要监视的基本 Key Vault 度量值
+ 如何配置度量值并创建仪表板 
+ 如何按指定阈值创建警报 

## <a name="basic-key-vault-metrics-to-monitor"></a>要监视的基本 Key Vault 度量值

+ 保管库可用性  
+ 保管库饱和 
+ 服务 API 延迟 
+ 服务 API 命中总计（按活动类型筛选） 
+ 错误代码（按状态代码筛选） 

**保管库可用性**-此指标应始终为100%。这是要监视的重要指标，因为它可以快速显示你的密钥保管库是否遇到中断。 

**保管库饱和**–密钥保管库可以为其提供服务的每秒请求数取决于正在执行的操作的类型。 某些保管库操作的每秒请求数降低了阈值。 此指标聚合所有操作类型中的密钥保管库的总使用量，并显示一个百分比值，指示当前密钥保管库的使用情况。 有关密钥保管库服务限制的完整列表，请参阅以下文档。 [Azure 密钥保管库服务限制](service-limits.md)

**服务 API 滞后时间**-此指标显示对密钥保管库的调用的平均延迟。 尽管密钥保管库可能会超出服务限制，但密钥保管库的高利用率可能会导致应用程序出现故障的延迟。 

**API 命中总次数**-此指标显示对密钥保管库进行的所有调用。 这将帮助你识别哪些应用程序正在调用你的密钥保管库。 

**错误代码**–此指标会显示密钥保管库是否遇到异常错误。 有关错误代码和故障排除指南的完整列表，请参阅以下文档。 [Azure Key Vault REST API 错误代码](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>如何配置度量值并创建仪表板

1. 登录到 Azure 门户
2. 导航到 Key Vault
3. 选择要**监视**的**度量值** 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-1.png)

4. 将图表的标题更新为要在仪表板上看到的内容。 
5. 选择范围。 在此示例中，我们将选择一个密钥保管库。 
6. 选择指标**总体保管库可用性**和聚合**平均** 
7. 将时间范围更新到最近24小时并将时间粒度更新为1分钟。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-2.png)

8. 针对保管库饱和和服务 API 延迟指标重复上述步骤。 选择 "**固定到仪表板**"，将指标保存到仪表板中。 

> [!IMPORTANT]
> 选择 "固定到仪表板" 并保存配置的每个指标。 如果离开页面并返回到该页面而不保存，则配置更改将丢失。 

9. 若要监视密钥保管库上的所有类型的操作，请使用 "**服务 API 命中总数**" 指标，并选择 "**按活动类型应用拆分**"

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-3.png)

10. 若要监视密钥保管库中的错误代码，请使用 "**服务 API 结果总数**" 指标，然后选择 "**按活动类型应用拆分**"

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-4.png)

现在，你将看到一个类似于下面的仪表板。 可以单击每个磁贴右上方的3个点，并根据需要重新排列和调整磁贴大小。 

保存并发布仪表板后，它将在 Azure 订阅中创建新的资源。 可以通过搜索 "共享仪表板" 随时查看它。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>如何配置 Key Vault 上的警报 

本部分将介绍如何在 key vault 上配置警报，以便在 key vault 处于不正常状态时提醒你的团队立即采取措施。 您可以配置警报，以便发送电子邮件，最好是发送到团队 DL，激发事件网格通知，或者拨打或发送电话号码。 还可以根据固定值选择静态警报，也可以选择在受监视的度量值超过定义的时间范围内的密钥保管库的平均次数限制时发出警报的动态警报。 

> [!IMPORTANT]
> 请注意，新配置的警报开始发送通知可能需要长达10分钟的时间。 

### <a name="configure-an-action-group"></a>配置操作组 

操作组是通知和属性的可配置列表。

1. 登录到 Azure 门户
2. 在搜索框中搜索**警报**
3. 选择**管理操作**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-6.png)

4. 选择 **+ 添加操作组**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-7.png)

5. 选择操作组的 "**操作类型**"。 在此示例中，我们将创建电子邮件警报。

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-9.png)

6. 单击页面底部的“确定”。**** 已成功创建操作组。 

现在，你已配置了一个操作组，接下来我们将配置密钥保管库警报阈值。 

### <a name="configure-alert-thresholds"></a>配置警报阈值 

1. 在 Azure 门户中选择密钥保管库资源，并选择 "**监视**" 下的 "**警报**"

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-10.png)

2. 选择**新警报规则**

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-11.png)

3. 选择警报规则的作用域。 您可以选择一个或多个保管库。 

> [!IMPORTANT]
> 请注意，当你为警报范围选择多个保管库时，所有选定的保管库必须位于同一区域中。 需要为不同区域中的保管库配置单独的警报规则。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-12.png)

4. 为警报选择条件。 你可以选择以下任何信号，并为警报定义逻辑。 Key Vault 团队建议配置以下警报阈值。 

    + Key Vault 可用性低于100% （静态阈值）
    + Key Vault 延迟大于500毫秒（静态阈值） 
    + 整体保管库饱和度大于75% （静态阈值） 
    + 整体保管库饱和量超过平均（动态阈值）
    + 错误代码总数高于平均值（动态阈值） 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>示例1：为延迟配置静态警报阈值

选择 "**总体服务 API 延迟**" 作为信号名称
> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-14.png)

请参阅以下配置参数。

+ 将阈值设置为**Static** 
+ 将运算符设置为**大于**
+ 将 "聚合类型" 设置为 "**平均值**"
+ 将阈值设置为**500**
+ 将聚合期间设置为**5 分钟**
+ 将评估频率设置为**1 分钟**
+ 选择“完成”   

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>示例2：为保管库饱和配置动态警报阈值 

使用动态警报时，你将能够看到所选密钥保管库的历史数据。 蓝区表示密钥保管库的平均使用量。 红色区域显示在满足警报配置中提供的其他条件时触发警报的高峰。 红点显示在聚合时间范围内满足警报条件的冲突实例。 你可以将警报设置为在设置的时间内发生一定次数的冲突后激发。 如果不想包含过去的数据，则可以选择在高级设置中排除以下旧数据。 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-16.png)

请参阅以下配置参数。

+ 将阈值设置为**动态** 
+ 将运算符设置为**大于**
+ 将 "聚合类型" 设置为 "**平均值**"
+ 将阈值敏感度设置为**中等**
+ 将聚合期间设置为**5 分钟**
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

恭喜，你现在已成功创建了一个用于密钥保管库的监视仪表板和配置的警报！ 完成上述所有步骤后，当 key vault 满足你配置的警报条件时，应会收到电子邮件警报。 下面显示了一个示例。 使用本文中设置的工具主动监视密钥保管库的运行状况。 

### <a name="example-email-alert"></a>示例电子邮件警报 

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图](../media/alert-20.png)
