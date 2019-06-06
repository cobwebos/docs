---
title: 使用 Azure 门户中创建 web 应用程序防火墙策略的 Azure 第一道防线
titlesuffix: Azure web application firewall
description: 了解如何使用 Azure 门户创建 web 应用程序防火墙 (WAF) 策略。
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 15a80dac0e0601480e22ad960f2827f3d8f290c0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479051"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>使用 Azure 门户创建 WAF 策略用于 Azure 第一道防线

本文介绍如何创建基本的 Azure web 应用程序防火墙 (WAF) 策略并将其应用到前端的主机在 Azure 第一道防线。

## <a name="prerequisites"></a>必备组件

遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建 Front Door 配置文件](quickstart-create-front-door.md)。 

## <a name="create-a-waf-policy"></a>创建 WAF 策略

首先，创建一个基本的 WAF 策略使用托管默认规则设置 (DRS) 使用门户。 

1. 在屏幕的左上方，选择**创建资源**> 搜索**WAF**> 选择**Web 应用程序防火墙 （预览版）** > 选择**创建**。
2. 在中**基础知识**选项卡**创建 WAF 策略**页上，输入或选择以下信息，接受其余设置的默认值，然后选择**查看 + 创建**:

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅            |选择你的第一道防线订阅名称。|
    | 资源组          |选择你的第一道防线资源组名称。|
    | 策略名称             |输入 WAF 策略的唯一名称。|

   ![创建 WAF 策略](./media/waf-front-door-create-portal/basic.png)

3. 在中**关联**选项卡**创建 WAF 策略**页上，选择**添加前端主机**，输入以下设置，然后选择**添加**:

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 第一道防线              | 选择你的第一道防线配置文件名称。|
    | 前端主机           | 选择你的第一道防线主机的名称，然后选择**添加**。|
    
    > [!NOTE]
    > 如果前端主机与 WAF 策略相关联，它显示为灰显。必须先从关联的策略中删除前端主机，然后重新将前端主机与新的 WAF 策略关联。
1. 选择**查看 + 创建**，然后选择**创建**。

## <a name="configure-waf-rules-optional"></a>配置 WAF 规则 （可选）

### <a name="change-mode"></a>更改模式

当创建 WAF 策略时，通过默认 WAF 策略处于**检测**模式。 在中**检测**模式下，WAF 不会阻止任何请求，而是在 WAF 日志记录与 WAF 规则匹配的请求。
若要查看 WAF 操作中，可以更改中的模式下设置**检测**到**防护**。 在中**防护**模式下，请求定义在默认规则设置 (DRS) 的匹配规则是否阻止且在 WAF 日志记录。

 ![更改 WAF 策略模式](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>自定义规则

可以通过选择创建自定义规则**添加自定义规则**下**自定义规则**部分。 这将启动自定义规则配置页。 下面是配置自定义规则以阻止请求，如果查询字符串中包含的一个示例**blockme**。

![更改 WAF 策略模式](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>默认规则集 (DRS)

默认情况下启用了 azure 托管默认规则集。 若要禁用的规则组内的单个规则，请展开该规则组中，选择中的规则**复选框**规则数，并选择前面**禁用**上面的选项卡上。 若要更改的操作类型用于规则中的单个规则设置，选择规则数，前面的复选框，然后选择**更改操作**上面的选项卡。

 ![更改 WAF 规则集](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[Azure web 应用程序防火墙](waf-overview.md)。
- 详细了解如何[Azure 第一道防线](front-door-overview.md)。
