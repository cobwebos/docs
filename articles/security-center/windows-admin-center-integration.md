---
title: 如何通过 Azure 安全中心保护 Windows 管理中心服务器
description: 本文介绍了如何将 Azure 安全中心与 Windows 管理中心集成
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: 36f519ce41ccfbfb48ca696ed2a61c6131a75998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906322"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>利用安全中心保护 Windows 管理中心资源

Windows 管理中心是适用于 Windows 服务器的管理工具。 它在一个位置为系统管理员提供可访问的大部分常用管理工具。 从 Windows 管理中心内可直接将本地服务器加入到 Azure 安全中心。 然后，可以直接体验在 Windows 管理中心中查看安全建议和警报的摘要。

> [!NOTE]
> 你的 Azure 订阅和关联的 Log Analytics 工作区都需要启用 Azure Defender 才能实现 Windows 管理中心集成。
> 如果你之前未在订阅和工作区中使用，Azure Defender 将在前30天免费。 有关详细信息，请参阅[定价信息页](security-center-pricing.md)。
>

成功将服务器从 Windows 管理中心加入到 Azure 安全中心后，你可以：

* 在 Windows 管理中心查看安全中心扩展中的安全警报和建议
* 在 Azure 门户（或通过 API）的安全中心中查看安全状况，并检索 Windows 管理中心托管的服务器的其他详细信息

通过组合使用这两个工具，安全中心可成为查看所有安全信息（任意资源）的单一窗口：保护 Windows 管理中心托管的本地服务器、VM 和其他任何 PaaS 工作负载。

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>将 Windows 管理中心中心托管服务器集成到安全中心

1. 在 Windows 管理中心中，选择一个服务器，然后在“工具”窗格中选择 Azure 安全中心扩展：

    ![Windows 管理中心中的 Azure 安全中心扩展](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 如果服务器已加入到安全中心，则不会显示“设置”窗口。

1. 单击“登录到 Azure 并设置”。
    ![将 Windows 管理中心扩展加入到 Azure 安全中心](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 按照说明将服务器连接到安全中心。 输入必要的详细信息并进行确认后，安全中心会进行必要的配置更改，以确保满足以下所有条件：
    * 已注册 Azure 网关。
    * 服务器有可报告的工作区以及关联订阅。
    * 安全中心的 Log Analytics 解决方案在工作区中启用。 此解决方案为向此工作区报告的 *所有* 服务器和虚拟机提供 Azure Defender 功能。
    * 在订阅上启用了用于服务器的 Azure Defender。
    * Log Analytics 代理已安装在服务器上并配置为向所选工作区报告。 如果服务器已向另一个工作区报告，则将其配置为也向新选中的工作区报告。

    > [!NOTE]
    > 加入后可能需要一些时间才能显示建议。 事实上，根据服务器活动，你可能不会收到任何警报。 若要生成测试警报以测试警报是否正常工作，请按照[警报验证过程](security-center-alert-validation.md)中的说明进行操作。


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>在 Windows 管理中心中查看安全建议和警报

加入后，可以直接在 Windows 管理中心的 Azure 安全中心区域中查看警报和建议。 单击“建议”或“警报”，在 Azure 门户中进行查看。 接下来，你将获得其他信息并了解如何修正问题。

[![Windows 管理中心中显示的安全中心建议和警报](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>在安全中心查看 Windows 管理中心托管服务器的安全建议和警报
在 Azure 安全中心中：

* 若要查看所有 Windows 管理中心服务器的安全建议，请打开 " [资产清单](asset-inventory.md) " 并筛选到要调查的计算机类型。 选择 " **vm 和计算机** " 选项卡。

* 若要查看所有 Windows 管理中心服务器的安全警报，请打开“安全警报”。 单击“筛选”并确保仅选中“非 Azure” ：

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="筛选 Windows 管理中心托管的服务器的安全警报" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::