---
title: 如何在 Azure 春季云中自行诊断和解决问题
description: 了解如何在 Azure 春季云中自行诊断和解决问题。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9d572545f589475dc2b460db90329fe8b0a838c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904307"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>如何在 Azure 春季云中自行诊断和解决问题

本文适用于：✔️ Java ✔️ C#

Azure 春季云诊断是一种交互式体验，可帮助你对应用进行故障排除。 不需要任何配置。 当你发现问题时，Azure 春季云诊断会指出问题所在，并指导你获取有助于排查和解决此问题的信息。

## <a name="prerequisites"></a>必备条件
要完成本教程，需要：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 部署的 Azure Spring Cloud 服务实例。 按[有关如何通过 Azure CLI 来部署应用的快速入门](spring-cloud-quickstart.md)操作即可入门。
* 至少已在该服务实例中创建一个应用程序。

## <a name="navigate-to-the-diagnostics-page"></a>导航到 "诊断" 页
1. 登录到 Azure 门户。
2. 转到 Azure Spring Cloud 的“概览”页。
3. 在页面左侧的菜单中打开 " **诊断并解决问题** "。

 ![诊断，求解对话框](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>搜索记录的问题
若要查找问题，可以通过键入关键字或单击 "解决方案组" 进行搜索，以浏览该类别中的所有内容。

 ![搜索问题](media/spring-cloud-diagnose/search-detectors.png)

选择 " **配置服务器运行状况检查**"、" **配置服务器运行状况状态**" 或 " **配置服务器更新历史记录** " 将显示各种结果。

![问题选项](media/spring-cloud-diagnose/detectors-options.png)

找到目标探测器，并单击它执行。 执行检测器后，将显示诊断摘要。 您可以选择 " **查看完整报表** " 以检查诊断详细信息，或者单击 " **显示磁贴菜单** " 按钮返回到 "检测器列表"。

 ![摘要诊断](media/spring-cloud-diagnose/summary-diagnostics.png)

在诊断详细信息页中，你可以在右上角更改诊断时间范围和控制器。 若要查看更多指标或日志，请切换每个诊断。 对于指标和日志，可能有15分钟的延迟。

 ![诊断详细信息](media/spring-cloud-diagnose/diagnostics-details.png)

某些结果包含相关文档。

 ![相关详细信息](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>后续步骤
* [使用警报和操作组监视 Spring Cloud 资源](spring-cloud-tutorial-alerts-action-groups.md)
* [Azure Spring Cloud 服务的安全控制](spring-cloud-concept-security-controls.md)