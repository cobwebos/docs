---
title: "将 XML 数据与转换-Azure 逻辑应用转换 |Microsoft 文档"
description: "创建转换或 mapps 将 XML 数据中使用企业集成 SDK 逻辑应用的格式之间转换"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: fb6027769377b3527b11f7831dab3bb8d7061c84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="enterprise-integration-with-xml-transforms"></a>与 XML 转换的企业集成
## <a name="overview"></a>概述
企业集成转换连接器将数据从一种格式转换为另一种格式。 例如，你可能具有包含 YearMonthDay 格式中的当前日期的传入消息。 转换可用于重新设置格式的日期，采用 MonthDayYear 格式。

## <a name="what-does-a-transform-do"></a>转换的作用是什么？
转换，这是也称为地图，由源 XML 架构 （输入） 和目标 XML 架构 （输出） 组成。 你可以使用不同的内置函数帮助操作或控制的数据，包括字符串操作、 条件分配、 算术表达式、 日期时间格式化程序，并甚至循环构造。

## <a name="how-to-create-a-transform"></a>如何创建转换？
可以通过使用 Visual Studio 中创建转换/映射[企业集成 SDK](https://aka.ms/vsmapsandschemas)。 创建和测试转换完成后，你将转换上载到你的集成的帐户。 

## <a name="how-to-use-a-transform"></a>如何使用转换
将转换/映射上载到你的集成帐户后，可用于创建逻辑应用。 每次触发逻辑应用 （和需要进行转换的输入内容） 时，逻辑应用运行您的转换。

**下面是用于转换的步骤**:

### <a name="prerequisites"></a>必备条件

* 创建集成帐户并向其中添加映射  

现在，在您注意的系统必备组件，它是时候创建逻辑应用：  

1. 创建逻辑应用和[将其链接到你的集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md "了解如何将集成帐户链接到逻辑应用")包含的映射。
2. 添加**请求**逻辑应用到的触发器  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. 添加**转换 XML**操作通过先选择**添加操作**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. 输入 word*转换*在搜索框中，以筛选器为你想要使用的所有操作  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. 选择**转换 XML**操作   
6. 添加 XML**内容**，您将转换。 你可以使用作为 HTTP 请求中接收任何 XML 数据**内容**。 在此示例中，选择触发逻辑应用的 HTTP 请求的正文。
7. 选择的名称**映射**你想要用于执行转换。 映射已必须在你集成的帐户。 在前面的步骤，您已为你包含你的代码图的集成帐户提供逻辑应用访问。      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. 保存你的工作  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

此时，您已完成设置你的代码图。 在实际应用中，你可能想要存储的 LOB 应用程序，例如 SalesForce 中的转换后的数据。 您可以轻松地为要转换的输出发送到 Salesforce 的操作。 

现在可以通过 HTTP 终结点向发出请求来测试你的转换。  

## <a name="features-and-use-cases"></a>功能和用例
* 在映射中创建的转换可以是简单的如将一个名称和地址，从一个文档复制到另一个。 或者，你可以创建更复杂的转换使用的现成可用映射操作。  
* 多个映射操作或函数是易于使用，包括字符串、 日期时间函数，等等。  
* 你可以执行架构之间的直接数据副本。 在 SDK 中包含的映射器，这非常简单，绘制线条与目标架构中的对应连接的源架构中的元素。  
* 在创建地图时，你可以查看的图形表示形式映射，其中显示了所有的关系和你创建的链接。
* 使用测试映射功能添加的示例 XML 消息。 简单单击一下，你可以测试创建，该图，并查看生成的输出。  
* 上载现有的映射  
* 包括对 XML 格式的支持。

## <a name="learn-more"></a>了解详细信息
* [了解有关企业集成包](../logic-apps/logic-apps-enterprise-integration-overview.md "了解企业集成包")  
* [了解地图](../logic-apps/logic-apps-enterprise-integration-maps.md "了解企业集成映射")  

