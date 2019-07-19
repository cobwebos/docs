---
title: 使用 Azure 门户为 Azure 前门创建 web 应用程序防火墙策略
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
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846392"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>使用 Azure 门户为 Azure 前门创建 WAF 策略

本文介绍如何创建基本的 Azure web 应用程序防火墙 (WAF) 策略, 并将其应用于 Azure 前门上的前端主机。

## <a name="prerequisites"></a>先决条件

遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建 Front Door 配置文件](quickstart-create-front-door.md)。 

## <a name="create-a-waf-policy"></a>创建 WAF 策略

首先, 使用门户创建具有托管默认规则集 (DRS) 的基本 WAF 策略。 

1. 在屏幕的左上方, 选择 "**创建资源**" > 搜索**WAF**> 选择 " **Web 应用程序防火墙 (预览版)** " > 选择 "**创建**"。
2. 在 "**创建 WAF 策略**" 页的 "**基本**信息" 选项卡中, 输入或选择以下信息, 接受其余设置的默认值, 然后选择 "**查看 + 创建**":

    | 设置                 | ReplTest1                                              |
    | ---                     | ---                                                |
    | 订阅            |选择前门订阅名称。|
    | 资源组          |选择前门资源组名称。|
    | 策略名称             |输入 WAF 策略的唯一名称。|

   ![创建 WAF 策略](./media/waf-front-door-create-portal/basic.png)

3. 在 "**创建 WAF 策略**" 页的 "**关联**" 选项卡中, 选择 "**添加前端主机**", 输入以下设置, 然后选择 "**添加**":

    | 设置                 | ReplTest1                                              |
    | ---                     | ---                                                |
    | 前门              | 选择前门配置文件名称。|
    | 前端主机           | 选择前门主机的名称, 然后选择 "**添加**"。|
    
    > [!NOTE]
    > 如果前端主机关联到 WAF 策略, 它将显示为灰显。必须首先从关联的策略中删除前端主机, 然后将前端主机重新关联到新的 WAF 策略。
1. 依次选择 "**查看**" 和 "创建", 然后选择 "**创建**"。

## <a name="configure-waf-rules-optional"></a>配置 WAF 规则 (可选)

### <a name="change-mode"></a>更改模式

创建 WAF 策略时, 默认 WAF 策略处于**检测**模式。 在**检测**模式下, WAF 不会阻止任何请求, 而是在 WAF 日志中记录与 WAF 规则匹配的请求。
若要查看操作中的 WAF, 可以将模式设置从**检测**更改为**阻止**。 在**阻止模式下**, 会阻止和记录在 WAF 日志中定义的匹配规则的请求。

 ![更改 WAF 策略模式](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>自定义规则

您可以通过在 "**自定义规则**" 部分下选择 "**添加自定义规则**" 来创建自定义规则。 这会启动 "自定义规则配置" 页。 下面是配置自定义规则以阻止请求 (如果查询字符串包含**blockme**) 的示例。

![更改 WAF 策略模式](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>默认规则集 (DRS)

默认情况下启用 Azure 托管默认规则集。 若要禁用规则组中的单个规则, 请展开该规则组中的规则, 选中规则编号前面的**复选框**, 并在上面的选项卡上选择 "**禁用**"。 若要更改规则集中单个规则的操作类型, 请选中该规则编号前面的复选框, 然后选择上面的 "**更改操作**" 选项卡。

 ![更改 WAF 规则集](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Web 应用程序防火墙](waf-overview.md)。
- 详细了解[Azure 前门](front-door-overview.md)。
