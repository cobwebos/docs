---
title: 如何将 Windows 管理中心与 Azure 安全中心集成 |微软文档
description: 本文介绍如何将 Azure 安全中心与 Windows 管理中心集成
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cbbdf724b9d7fe4948553e7526410b994f491b49
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435261"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>将 Azure 安全中心与 Windows 管理中心集成

Windows 管理中心是 Windows 服务器的管理工具。 它是系统管理员访问大多数最常用的管理工具的单个位置。 在 Windows 管理中心内，可以直接将机上服务器登机到 Azure 安全中心。 然后，您可以在 Windows 管理中心体验中直接查看安全建议和警报的摘要。

> [!NOTE]
> Azure 订阅和关联的日志分析工作区都需要启用安全中心的标准层才能启用 Windows 管理中心集成。
> 如果您以前未在订阅和工作区上使用标准层，则标准层在前 30 天内是免费的。 有关详细信息，请参阅[定价信息页](security-center-pricing.md)。
>

成功将服务器从 Windows 管理中心连接到 Azure 安全中心后，可以：

* 在 Windows 管理中心的安全中心扩展室内查看安全警报和建议
* 查看安全状况，并在 Azure 门户（或通过 API）的安全中心中检索 Windows 管理中心托管服务器的其他详细信息

通过组合这两个工具，安全中心将成为您的单一窗格，以查看所有安全信息，无论资源是什么：保护您的 Windows 管理中心管理的前期服务器、VM 和任何其他 PaaS 工作负载。

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>将 Windows 管理中心托管服务器安装到安全中心

1. 在 Windows 管理中心，选择其中一台服务器，并在 **"工具"** 窗格中选择 Azure 安全中心扩展名：

    ![Windows 管理中心中的 Azure 安全中心扩展](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 如果服务器已装机到安全中心，则不会显示设置窗口。

1. 单击"**登录到 Azure 并设置**"。
    ![将 Windows 管理中心扩展到 Azure 安全中心](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 按照说明将服务器连接到安全中心。 输入必要的详细信息并确认后，安全中心会进行必要的配置更改，以确保以下所有内容都为 true：
    * 已注册 Azure 网关。
    * 服务器具有要向其报告的工作区和关联的订阅。
    * 安全中心的标准层日志分析解决方案在工作区上启用。 此解决方案为报告为此工作区*的所有*服务器和虚拟机提供安全中心的标准层功能。
    * 安全中心针对虚拟机的标准层定价在订阅上启用。
    * 日志分析代理安装在服务器上，并配置为向所选工作区报告。 如果服务器已报告到另一个工作区，则将其配置为也报告给新选择的工作区。

    > [!NOTE]
    > 在加入建议后可能需要一些时间才能显示建议。 事实上，根据服务器活动的不同，您可能不会收到*任何*警报。 要生成测试警报以测试警报是否正常工作，请按照[警报验证过程中](security-center-alert-validation.md)的说明操作。


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>在 Windows 管理中心查看安全建议和警报

板载后，可以直接在 Windows 管理中心的 Azure 安全中心区域查看警报和建议。 单击建议或警报以查看它们。 在那里，您将获得其他信息，并了解如何修复问题。

[![安全中心建议和警报，如 Windows 管理中心所示](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>查看安全中心中 Windows 管理中心托管服务器的安全建议和警报
从 Azure 安全中心：

* 要查看所有 Windows 管理中心服务器的安全建议，请打开 **"计算&应用"，** 然后单击 **"VM 和计算机**"选项卡。

    [![查看 Windows 管理中心托管服务器的安全建议](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* 要查看所有 Windows 管理中心服务器的安全警报，请打开**安全警报**。 单击 **"筛选器**"并确保**仅**选择"非 Azure"：

    ![筛选 Windows 管理中心托管服务器的安全警报](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![查看 Windows 管理中心托管服务器的安全警报](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)