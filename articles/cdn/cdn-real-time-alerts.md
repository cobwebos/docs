---
title: "Azure CDN 实时警报 | Microsoft Docs"
description: "Microsoft Azure CDN 中的实时警报。 实时警报可针对 CDN 配置文件中终结点的性能提供有关通知。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 51dce1680be5f5f4387c2ba02827195bcdbe9b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Microsoft Azure CDN 中的实时警报
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概述
本文档介绍 Microsoft Azure CDN 中的实时警报。 此功能可为 CDN 配置文件中终结点的性能提供有关通知。  可以基于以下各项设置电子邮件或 HTTP 警报：

* 带宽
* 状态代码
* 缓存状态
* 连接

## <a name="creating-a-real-time-alert"></a>创建实时警报
1. 在 [Azure 门户](https://portal.azure.com)中，导航到 CDN 配置文件。
   
    ![CDN 配置文件](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件管理按钮](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    CDN 管理门户打开。
3. 将鼠标悬停在“**分析**”选项卡，然后悬停在“**实时统计信息**”浮出控件。  单击“**实时警报**”。
   
    ![CDN 管理门户](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    随即显示现有的警报配置（如果有） 列表。
4. 单击“**添加警报**”按钮。
   
    ![“添加警报”按钮](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    随即显示用于创建新警报的表单。
   
    ![新警报表单](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. 如果希望在单击“**保存**”时此警报处于活动状态，请勾选“**启用警报**”复选框。
6. 在“**名称**”字段中输入警报的描述性名称。
7. 在“**媒体类型**”下拉列表中，选择“**HTTP 大型对象**”。
   
    ![HTTP 大型对象处于选中状态的媒体类型](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > 必须选择“**HTTP 大型对象**”作为“**媒体类型**”。  **来自 Verizon 的 Azure CDN** 会不使用其他选项。  如果未能选择“HTTP 大型对象”，永远不会触发警报。
   > 
   > 
8. 通过选择“**指标**”、“**运算符**”和“**触发值**”，创建一个**表达式**来进行监视。
   
   * 对于”**指标**”，请选择要监视的条件类型。  “**带宽 Mbps**”是每秒兆位的带宽使用量。  “**总连接数**”是边缘服务器的并发 HTTP 连接数。  有关各种缓存状态和状态代码的定义，请参阅 [Azure CDN 缓存状态代码](https://msdn.microsoft.com/library/mt759237.aspx)和 [Azure CDN HTTP 状态代码](https://msdn.microsoft.com/library/mt759238.aspx)
   * “**运算符**”是在指标和触发值之间建立关系的数学运算符。
   * “触发值”是发送通知之前必须满足的阈值。
     
     在以下示例中，创建的表达式指示在 404 状态代码数大于 25 时发送通知。
     
     ![实时警报示例表达式](./media/cdn-real-time-alerts/cdn-expression.png)
9. 对于“**间隔**”，输入希望表达式进行计算的频率。
10. 在“**通知时间**”下拉列表中，选择在表达式为真时何时想获得通知。
    
    * “条件开始”指明在第一次检测到指定的条件时会发送通知。
    * “条件结束”指明在未检测到指定的条件时会发送通知。 只有在网络监视系统检测到指定的条件发生后，才会触发此通知。
    * “连续”指明在网络监视系统每次检测到指定的条件时都将发送通知。 请记住，对于指定的条件，网络监视系统每个间隔只会检查一次。
    * “条件开始和结束”指明在第一次检测到指定的条件时发送一次通知，并在不能再检测到条件时再次发送一次通知。
1. 如果想要通过电子邮件接收通知，请勾选“**通过电子邮件通知**”复选框。  
    
    ![通过电子邮件的形式通知](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    在“**收件人**”字段中，输入要将通知发送到的电子邮件地址。 对于“**主题**”和“**正文**”，可以保留默认值，或者也可以使用“**可用关键字**”列表自定义消息，以在发送消息时动态插入警报数据。
    
    > [!NOTE]
    > 可以通过单击“**测试通知**”按钮来测试电子邮件通知，但只能在保存警报的配置之后进行。
    > 
    > 
12. 如果希望通知被发布到 Web 服务器，请勾选“**通过 HTTP Post 通知**”复选框。
    
    ![“通过 HTTP Post 通知”表单](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    在“**Url**”字段中，输入要将 HTTP 消息发布到的 URL。 在“**标头**”文本框中，输入要在请求中发送的 HTTP 标头。  对于“正文”，可使用“可用关键字”列表自定义消息，在发送消息时动态插入警报数据。  “标头”和“正文”默认为 XML 有效负载，类似于以下示例：
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > 可以通过单击“**测试通知**”按钮来测试 HTTP Post 通知，但只能在保存警报的配置之后进行。
    > 
    > 
13. 单击“**保存**”按钮以保存警报配置。  如果在步骤 5 中勾选了“**启用警报**”，通知现在将处于活动状态。

## <a name="next-steps"></a>后续步骤
* 分析 [Azure CDN 中的实时统计信息](cdn-real-time-stats.md)
* 更深入地挖掘[高级 HTTP 报表](cdn-advanced-http-reports.md)
* 分析[使用模式](cdn-analyze-usage-patterns.md)

