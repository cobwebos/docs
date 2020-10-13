---
title: Azure Advisor Windows 虚拟桌面演练-Azure
description: 如何解决适用于 Windows 虚拟桌面的 Azure 顾问建议。
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033024"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>如何解决 Azure 顾问的建议

本文介绍如何解决 Azure Advisor for Windows 虚拟桌面中显示的建议。

## <a name="no-validation-environment-enabled"></a>"未启用验证环境"

>[!div class="mx-imgBorder"]
>![Azure 顾问卓越运营页的屏幕截图。 "未启用验证环境" 建议以红色突出显示。](media/no-validation-environment.png)

此建议显示在 "卓越运营" 下。 建议还应显示一条警告消息，如下所示：

"你没有在此订阅中启用验证环境。 创建主机池时，在 "属性" 选项卡中为 "验证环境" 选择 " **否** "。若要通过 Windows 虚拟桌面服务部署确保业务连续性，请确保至少有一个主机池与验证环境结合，可在其中测试潜在问题。 "

可以通过在一个主机池中启用验证环境来使此警告消息消失。

启用验证环境：

1. 转到 Azure 门户主页，然后选择要更改的主机池。

2. 接下来，选择要从生产环境更改为验证环境的主机池。

3. 在主机池中，选择左栏中的 " **属性** "。 接下来，向下滚动，直到看到 "验证环境"。 选择 **"是**"，然后选择 " **应用**"。

>[!div class="mx-imgBorder"]
>!["属性" 菜单的屏幕截图。 "验证环境" 以红色突出显示，并且 "是" 气泡处于选中状态。](media/validation-yes.png)

这些更改不会立即出现警告，但最终会消失。 Azure Advisor 一天更新了两次。 在此之前，你可以手动推迟或取消建议。 建议你自行退出建议。 这样一来，当设置发生变化时，Azure 顾问就会让你知道它是否遇到任何问题。

## <a name="not-enough-production-non-validation-environments-enabled"></a>"生产 (非验证) 环境已启用"）

此建议显示在 "卓越运营" 下。

对于此建议，会出于以下原因之一显示警告消息：

- 验证环境中的主机池太多。
- 你没有任何生产主机池。

建议用户在验证环境中的主机池数不到一半。

若要解决此警告：

1. 中转到 Azure 门户主页。

2. 选择要从验证更改为生产的主机池。

3. 在主机池中，选择屏幕右侧列中的 " **属性** " 选项卡。 接下来，向下滚动，直到看到 "验证环境"。 选择 " **否**"，然后选择 " **应用**"。

>[!div class="mx-imgBorder"]
>!["属性" 菜单的屏幕截图。 "验证环境" 以红色突出显示，并选中 "无" 气泡。](media/validation-no.png)

这些更改不会立即出现警告，但最终会消失。 Azure Advisor 一天更新了两次。 在此之前，你可以手动推迟或取消建议。 建议你自行退出建议。 这样一来，当设置发生变化时，Azure 顾问就会让你知道它是否遇到任何问题。

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"未阻止足够的链接来成功实现 VM"

此建议显示在 "卓越运营" 下。

需要取消阻止特定 Url，以确保虚拟机 (VM) 正常工作。 可以在 " [安全 URL" 列表](safe-url-list.md)中查看列表。 如果未解除锁定 Url，你的 VM 将不能正常工作。

若要解决此建议，请确保取消阻止 " [安全 URL" 列表](safe-url-list.md)中的所有 url。 您也可以使用服务标记或 FQDN 标记来取消阻止 Url。

## <a name="propose-new-recommendations"></a>建议新建议

可以通过提交建议建议，帮助我们改进 Azure Advisor。 你的建议可以帮助其他用户离开困难的地方。 若要提交建议，请访问 [我们的 UserVoice 论坛](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) 并填写提交窗体。 填写表单时，请确保提供尽可能多的详细信息。

## <a name="next-steps"></a>后续步骤

如果正在寻找有关如何解决常见问题的更深入的指南，请查看 [Windows 虚拟桌面的故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
