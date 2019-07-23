---
title: 快速入门 - 使用 Azure 门户创建流量管理器配置文件以确保应用程序的高度可用性
description: 本快速入门文章介绍如何创建流量管理器配置文件，以便生成高度可用的 Web 应用程序。
services: traffic-manager
author: asudbring
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: allensu
ms.openlocfilehash: 1f7fd3398c24eb82b1a2308f3b52df382c0aab7e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224660"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建流量管理器配置文件

本快速入门介绍如何创建流量管理器配置文件，以便实现 Web 应用程序的高度可用性。

本快速入门介绍 Web 应用程序的两个实例。 每个实例在不同的 Azure 区域运行。 需根据[终结点优先级](traffic-manager-routing-methods.md#priority)创建流量管理器配置文件。 此配置文件将用户流量定向到运行 Web 应用程序的主站点。 流量管理器持续监视 Web 应用程序。 如果主站点不可用，它会提供目标为备份站点的自动故障转移。

如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="prerequisites"></a>先决条件

本快速入门需要两个部署在两个不同的 Azure 区域（美国东部和西欧）的 Web 应用程序实例。   每个都可以充当流量管理器的主终结点和故障转移终结点。

1. 在屏幕的左上方，选择“创建资源”   > “Web”   >   “Web 应用”。

1. 在“创建 Web 应用”中，在“基本信息”选项卡中键入或选择以下值：  

   - **订阅** > **资源组**：选择“新建”，然后键入 **myResourceGroupTM1**。 
   - “实例详细信息”   >   “名称”：键入 myWebAppEastUS  。
   - “实例详细信息”   >   “发布”：选择“代码”  。
   - **实例详细信息** > **运行时堆栈**：选择“ASP.NET V4.7” 
   - **实例详细信息** > 操作系统  ：选择“Windows”  。
   - “实例详细信息” > “区域”：   选择“美国东部”  。
   - “应用服务计划”   >   “Windows 计划(美国东部)”：选择“新建”，然后键入 myAppServicePlanEastUS  
   - **应用服务计划** > **SKU 和大小**：选择“标准版 S1”  。
   
3. 选择“监视”选项卡，或选择“下一步: 监视”   。  在“监视”下，将“Application Insights” > “启用 Application Insights”设置为“否”     。

4. 选择“查看并创建” 

5. 查看设置，并单击“创建”。   Web 应用在成功部署后会创建默认的网站。

6. 按步骤创建另一个 Web 应用，其名称为 myWebAppWestEurope，其资源组名称为 myResourceGroupTM2，其位置为西欧，其应用服务计划名称为 myAppServicePlanWestEurope，其他所有设置与 myWebAppEastUS 相同         。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

创建根据终结点优先级定向用户流量的流量管理器配置文件。

1. 在屏幕左上方，选择“创建资源”   > “网络”   >   “流量管理器配置文件”。
2. 在“创建流量管理器配置文件”中，输入或选择以下设置： 

    | 设置 | 值 |
    | --------| ----- |
    | 名称 | 为流量管理器配置文件输入唯一名称。|
    | 路由方法 | 选择“优先级”。 |
    | 订阅 | 选择要将流量管理器配置文件应用到其中的订阅。 |
    | 资源组 | 选择“myResourceGroupTM1”  。|
    | 位置 |此设置是指资源组的位置。 它对将要全局部署的流量管理器配置文件没有影响。|

3. 选择“创建”  。

## <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

将“美国东部”区域的网站添加为用于路由所有用户流量的主终结点。  将“西欧”区域的网站添加为故障转移终结点。  当主终结点不可用时，流量自动路由到故障转移终结点。

1. 在门户的搜索栏中，输入在上一部分创建的流量管理器配置文件名称。
2. 从搜索结果中选择该配置文件。
3. 在“流量管理器配置文件”  的“设置”  部分，选择“终结点”  ，然后选择  “添加”。
4. 输入或选择以下设置：

    | 设置 | 值 |
    | ------- | ------|
    | 类型 | 选择“Azure 终结点”。  |
    | 名称 | 输入 *myPrimaryEndpoint*。 |
    | 目标资源类型 | 选择“应用服务”。  |
    | 目标资源 | 选择“选择应用服务”   >   “美国东部” |
    | 优先度 | 选择“1”。  如果此终结点处于正常状态，则所有流量都会转到此终结点。 |

    ![将终结点添加到流量管理器配置文件的屏幕截图。](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. 选择“确定”  。
6. 若要为第二个 Azure 区域创建故障转移终结点，请使用以下设置重复步骤 3 和 4：

    | 设置 | 值 |
    | ------- | ------|
    | 类型 | 选择“Azure 终结点”。  |
    | 名称 | 输入 *myFailoverEndpoint*。 |
    | 目标资源类型 | 选择“应用服务”。  |
    | 目标资源 | 选择“选择应用服务”   >   “西欧”。 |
    | 优先度 | 选择 **2**。 如果主终结点不正常，则所有流量都会转到此故障转移终结点。 |

7. 选择“确定”  。

添加完两个终结点后，它们会显示在**流量管理器配置文件**中。 请注意，它们的监视状态现在为“联机”  。

## <a name="test-traffic-manager-profile"></a>测试流量管理器配置文件

在此部分，需检查流量管理器配置文件的域名。 此外还需将主终结点配置为不可用。 最后可以看到该 Web 应用仍然可用。 这是因为流量管理器将流量发送到故障转移终结点。

### <a name="check-the-dns-name"></a>检查 DNS 名称

1. 在门户的搜索栏中，搜索在前面部分中创建的**流量管理器配置文件**名称。
2. 选择流量管理器配置文件。 此时会显示“概览”  。
3. “流量管理器配置文件”  会显示新建的流量管理器配置文件的 DNS 名称。
  
   ![流量管理器 DNS 名称位置的屏幕截图](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>查看正在运行的流量管理器

1. 在 Web 浏览器中输入流量管理器配置文件的 DNS 名称，以便查看 Web 应用的默认网站。

    > [!NOTE]
    > 在本快速入门方案中，所有请求都路由到主终结点。 它设置为“优先级 1”。 

    ![用于确认流量管理器配置文件可用性的网页的屏幕截图](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. 若要查看流量管理器故障转移如何进行，请禁用主站点：
    1. 在“流量管理器配置文件”页的“概览”部分，选择“myPrimaryEndpoint”。  
    2. 在 *myPrimaryEndpoint* 中选择“禁用” > “保存”。  
    3. 关闭 **myPrimaryEndpoint**。 请注意，状态现在为“禁用”。 
3. 从上一步复制流量管理器配置文件的 DNS 名称即可在新的 Web 浏览器会话中查看网站。
4. 验证 Web 应用是否仍然可用。

主终结点不可用，因此系统将你路由到故障转移终结点。

## <a name="clean-up-resources"></a>清理资源

完成后，请删除资源组、Web 应用程序以及所有相关资源。 为此，请从仪表板选择每个单独的项，然后在每个页面的顶部选择“删除”。 

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建流量管理器配置文件， 以便将用户流量定向到高度可用的 Web 应用程序。 若要详细了解如何路由流量，请继续学习流量管理器教程。

> [!div class="nextstepaction"]
> [流量管理器教程](tutorial-traffic-manager-improve-website-response.md)
