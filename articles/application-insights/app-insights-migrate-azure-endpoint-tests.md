---
title: "将 Azure 终结点迁移到 Application Insights 可用性测试"
description: "在 2016 年 10 月 31 日前将经典 Azure 终结点监视测试迁移到 Application Insights 可用性测试。"
services: application-insights
documentationcenter: 
author: soubhagyadash
manager: douge
ms.assetid: 74cc1ddf-1b70-4ad2-bdb5-1bd895937861
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 9b3cf88473cd3f4e4e5a383dfdadfe06d4f34f36


---
# <a name="moving-from-azure-endpoint-monitoring-to-application-insights-availability-tests"></a>从 Azure 终结点监视移至 Application Insights 可用性测试
是否要将[终结点监视](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/)用于 Azure 应用？ 在 *2016 年 10 月 31 日*前，我们会将它们替换为新的功能更强大的[可用性测试](app-insights-monitor-web-app-availability.md)。 我们已创建一些新测试，尽管这些测试在 2016 年 10 月 31 日前处于禁用状态。 

用户可以编辑新测试并根据需要执行切换。 用户可以在 [Azure 门户](https://portal.azure.com)的 Default-ApplicationInsights-CentralUS 资源组中找到它们。

## <a name="what-are-availability-tests"></a>什么是可用性测试？
可用性测试是 Azure 的一项功能，可通过从全球范围内多达 16 个位置向网站或服务发送 HTTP 请求（单个 ping 测试或 Visual Studio Web 测试）来不断地检查任何网站或服务是否已启动且正在运行。 

在 [经典 Azure 门户](https://manage.windowsazure.com)中，这些测试称为“终结点监视”。 它们在作用域中更受限制。 新的可用性测试取得实质性改善：

* 可以对每个 Application Insights 资源最多进行 10 次 Visual Studio Web 测试或 ping 测试。 
* 可以从全球范围内多达 16 个位置向 Web 应用发送 HTTP 请求。 更好地控制测试成功标准。 
* 测试任何网站或服务，而不只是 Azure Web 应用。
* 测试重试：减少由于暂时性网络问题而导致的假阳性警报。 
* Webhook 可以接收有关警报的 HTTP POST 通知。

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

在[此处](app-insights-monitor-web-app-availability.md)详细了解可用性测试。

可用性测试属于 [Azure Application Insights](app-insights-overview.md)，后者是任何 Web 应用程序的可扩展分析服务。

## <a name="so-whats-happening-to-my-endpoint-tests"></a>那么我的终结点测试将发生什么情况？
* 我们已将终结点监视测试复制到新的 Application Insights 可用性测试。
* 新的可用性测试当前处于禁用状态，而旧的终结点测试仍在运行。
* 警报规则尚*未*迁移。 新测试最初已设置为使用默认规则：
  * 当在 5 分钟内有 1 个以上的位置报告故障时触发。
  * 向订阅管理员发送电子邮件。

用户可以在 [Azure 门户](https://portal.azure.com)的“Default-ApplicationInsights-CentralUS”资源组中找到已迁移的测试。 测试名称的前缀为“Migrated-”。 

## <a name="what-do-i-need-to-do"></a>我需要做什么？
* 如果由于某种原因我们遗漏了迁移你的测试，新的可用性测试[易于设置](app-insights-monitor-web-app-availability.md)。

### <a name="option-a-do-nothing-leave-it-to-us"></a>选项 A：什么也不做。 将其留给我们做。
**在 2016 年 10 月 31 日，**我们将：

* 禁用旧的终结点测试。
* 启用已迁移的可用性测试。

### <a name="option-b-you-manage-andor-enable-the-new-tests"></a>选项 B：你管理和/或启用新的测试。
* 在新的 [Azure 门户](https://portal.azure.com)中查看和编辑新的可用性测试。 
  * 查看触发条件
  * 查看电子邮件收件人
* 启用新的测试
* 我们将在 2016 年 10 月 31 日禁用旧的终结点监视测试 

### <a name="option-c-opt-out"></a>选项 C：选择退出
如果你不想使用可用性测试，则可以在 [Azure 门户](https://portal.azure.com)中删除它们。 此外，通知电子邮件的底部也有“取消订阅”链接。

我们仍将在 2016 年 10 月 31 日删除旧的终结点测试。 

## <a name="how-do-i-edit-the-new-tests"></a>如何编辑新测试？
登录到 [Azure 门户](https://portal.azure.com)并查找“Migrated-” Web 测试： 

![选择“资源组”、Default-ApplicationInsights-CentralUS，并打开“已迁移”的测试。](./media/app-insights-migrate-azure-endpoint-tests/20.png)

编辑和/或启用测试：

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)

## <a name="why-is-this-happening"></a>为何会发生这种情况？
提供更好的服务。 旧的终结点服务能力要小得多。 你只能提供两个 URL，用于从 3 个地理位置对 Azure VM 或 Web 应用进行简单的 ping 测试。 新的测试可以从多达 16 位置运行多步骤 Web 测试，并且可以为一个应用程序最多指定 10 个测试。 你可以测试任何 URL，它不必是 Azure 站点。

新的测试从要测试的 Web 应用或 VM 单独进行配置。 

我们正在迁移这些测试，以确保你在使用新门户时可以继续对其进行控制。 

## <a name="what-is-application-insights"></a>什么是 Application Insights？
新的可用性测试属于 [Azure Application Insights](app-insights-overview.md)。 下面是一个 [2 分钟的视频](http://go.microsoft.com/fwlink/?LinkID=733921)。

## <a name="am-i-paying-for-the-new-tests"></a>我要购买新测试吗？
在默认免费计划中，已迁移的测试设置在 Application Insights 资源中。 这允许由最多 5 百万个数据点组成的集合。 该集合轻松涵盖你的测试当前将使用的数据量。 

当然，如果你喜欢 Application Insights 并创建更多可用性测试，或者采用其更多性能监视和诊断功能，则将生成更多数据点。  但是，结果只会是你可能会达到免费计划的配额。 除非你选择加入“标准”或“高级”计划，否则将不会收到帐单。 

[详细了解 Application Insights 定价和配额监视](app-insights-pricing.md)。 

## <a name="what-is-and-isnt-migrated"></a>什么迁移，什么不迁移？
从旧的终结点测试保留以下项：

* 要测试的终结点 URL。
* 从其发送请求的地理位置。
* 测试频率仍为 5 分钟。
* 测试超时仍为 30 秒。 

不迁移：

* 警报触发规则。 我们已设置在 5 分钟内 1 个位置报告故障时触发的规则。
* 警报收件人。 通知电子邮件将发送给订阅所有者和共同所有者。 

## <a name="how-do-i-find-the-new-tests"></a>如何找到新测试？
如果需要，现在就可以编辑任何新测试。 登录到 [Azure 门户](https://portal.azure.com)，打开“资源组”，并选择“Default-ApplicationInsights-CentralUS”。 在该组中，你将找到新的 Web 测试。 [了解新的可用性测试](app-insights-monitor-web-app-availability.md)。

请注意，新的警报电子邮件将从此地址发送：App Insights 警报(ai-noreply@microsoft.com)

## <a name="what-happens-if-i-do-nothing"></a>如果我什么也不做，会发生什么情况？
选项 A 将适用。 我们将启用已迁移的测试，并设置默认警报规则，如上所述。 你将需要添加任何自定义的警报规则、收件人，如上所述。 我们将禁用旧的终结点监视测试。 

## <a name="where-can-i-provide-feedback-on-this"></a>我可以在哪里提供相关反馈？
我们非常感谢你的反馈。 请[向我们发送电子邮件](mailto:vsai@microsoft.com)。 




<!--HONumber=Nov16_HO3-->


