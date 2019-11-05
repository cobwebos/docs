---
title: 如何将 Windows 管理中心与 Azure 安全中心集成 |Microsoft Docs
description: 本文介绍如何将 Azure 安全中心与 Windows 管理中心集成
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 48bf2cc3feb9ea3983200a2ef778669b5cfe36fa
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521587"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>将 Azure 安全中心与 Windows 管理中心集成

Windows 管理中心是适用于 Windows 服务器的管理工具。 它是系统管理员访问大部分最常用管理工具的单一位置。 从 Windows 管理中心，可以直接在 Azure 安全中心中本地服务器。 然后，你可以直接在 Windows 管理中心体验中查看安全建议和警报的摘要。

> [!NOTE]
> 你的 Azure 订阅和关联的 Log Analytics 工作区都需要启用安全中心的标准层，才能启用 Windows 管理中心集成。
> 如果你之前未在订阅和工作区中使用过标准层，则该标准级别可免费使用。 有关详细信息，请参阅[定价信息页](security-center-pricing.md)。
>

如果已成功将服务器从 Windows 管理中心载入到 Azure 安全中心，可以：

* 在 Windows 管理中心的安全中心扩展内查看安全警报和建议
* 在 Azure 门户（或通过 API）的安全中心查看安全状况并检索 Windows 管理中心托管服务器的其他详细信息

通过组合使用这两种工具，安全中心成为单个窗格，可查看所有安全信息，无论资源如何：保护 Windows 管理中心管理的本地服务器、Vm 和其他 PaaS 工作负荷。

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>将 Windows 管理中心中心托管服务器载入安全中心

1. 在 Windows 管理中心中，选择一个服务器，然后在 "**工具**" 窗格中选择 "Azure 安全中心" 扩展：

    ![Windows 管理中心中的 Azure 安全中心扩展](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 如果服务器已载入到安全中心，则将不会显示 "设置" 窗口。

1. 单击 "**登录到 Azure" 并设置**。
    ![将 Windows 管理中心扩展扩展到 Azure 安全中心](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 按照说明将服务器连接到安全中心。 输入必要的详细信息并进行确认后，安全中心会进行必要的配置更改，以确保满足以下所有条件：
    * Azure 网关已注册。
    * 服务器有一个要向其报告的工作区和一个关联的订阅。
    * 安全中心的标准层 Log Analytics 解决方案已在工作区中启用。 此解决方案为向此工作区报告的*所有*服务器和虚拟机提供安全中心的标准层功能。
    * 针对虚拟机的安全中心的标准层定价在订阅上启用。
    * Microsoft Monitoring Agent （MMA）安装在服务器上并配置为向所选工作区报告。 如果服务器已向另一个工作区报告，则会将其配置为向新选择的工作区报告。

    > [!NOTE]
    > 载入后可能需要一些时间才能显示建议。 事实上，根据您的服务器活动，您可能不会收到*任何*警报。 若要生成测试警报以测试警报是否正常工作，请按照[警报验证过程](security-center-alert-validation.md)中的说明进行操作。


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>在 Windows 管理中心中查看安全建议和警报

载入后，可以直接在 Windows 管理中心的 Azure 安全中心区域中查看警报和建议。 单击 "建议" 或 "警报" 以在 Azure 门户中查看它们。 在这里，你将获得其他信息并了解如何修正问题。

[![在 Windows 管理中心中显示的安全中心建议和警报](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>在安全中心查看 Windows 管理中心托管服务器的安全建议和警报
从 Azure 安全中心：

* 若要查看所有 Windows 管理中心服务器的安全建议，请打开**计算 & 应用**，然后单击 " **vm 和计算机**" 选项卡。按资源 "服务器" 筛选列表，如下所示：

    [![查看 Windows 管理中心托管服务器的安全建议](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* 若要查看所有 Windows 管理中心服务器的安全警报，请打开 "**安全警报**"。 单击 "**筛选**"，并确保**仅**选择 "非 Azure"：

    ![筛选 Windows 管理中心托管服务器的安全警报](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![查看 Windows 管理中心托管服务器的安全警报](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)