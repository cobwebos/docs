---
title: 配置并为 Azure 托管应用程序批准在实时访问权限
description: 介绍 Azure 托管应用程序的使用者如何审批中实时访问托管应用程序的请求。
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481709"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>配置并为 Azure 托管应用程序批准在实时访问权限

作为托管应用程序的使用者，您可能不愿意提供发布服务器上对托管的资源组的永久访问权限。 若要向你提供更好地授予对托管资源的访问控制，Azure 托管应用程序提供了名为在实时 (JIT) 访问，目前以预览版提供的功能。 它使您批准时，并为多长时间发布服务器有权访问的资源组。 发布服务器可以进行此期间，所需的更新，但通过该时间时，发布者的访问权限过期。

授予访问权限的工作流是：

1. 发布服务器将添加到应用商店的托管应用程序，并且指定 JIT 访问可用。

1. 在部署期间，您将启用 JIT 访问托管应用程序的实例。

1. 在部署后，可以更改针对 JIT 访问设置。

1. 发布服务器发送访问请求。

1. 批准的请求。

本文重点介绍使用者执行若要启用 JIT 访问，以及审批请求的操作。 若要了解有关发布使用 JIT 访问托管应用程序的信息，请参阅[请求在 Azure 托管应用程序中实时访问](request-just-in-time-access.md)。

> [!NOTE]
> 若要使用在实时访问，必须具有[Azure Active Directory P2 许可证](../active-directory/privileged-identity-management/subscription-requirements.md)。

## <a name="enable-during-deployment"></a>在部署过程中启用

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 查找托管应用程序启用 jit 的 marketplace 项。 选择“创建”  。

1. 针对新的托管应用程序，提供值的同时**JIT 配置**步骤允许您启用或禁用 JIT 访问托管应用程序。 选择**是**有关**启用 JIT 访问**。 默认情况下，对于使用 JIT 启用在 marketplace 中定义的托管应用程序选择此选项。

   ![配置访问权限](./media/approve-just-in-time-access/configure-jit-access.png)

   只能在部署期间启用 JIT 访问。 如果选择**否**，发布服务器到托管的资源组获取永久访问权限。 无法启用 JIT 访问更高版本。

1. 若要更改默认审批设置，请选择**自定义 JIT 配置**。

   ![自定义访问](./media/approve-just-in-time-access/customize-jit-access.png)

   默认情况下，启用 jit 的托管应用程序具有以下设置：

   * 批准模式 – 自动
   * 最大访问持续时间 – 8 小时
   * 审批者 – 无

   如果批准模式设置为**自动**，审批者将收到的每个请求的通知，但自动批准该请求。 如果设置为**手动**、 审批者会收到的每个请求的通知和其中一个必须先批准它。

   激活的最大持续时间指定的最长的时间发布者可以请求托管的资源组的访问权限。

   审批者列表是可批准的 JIT 访问请求的 Azure Active Directory 用户。 若要添加审批者，请选择**添加审批者**和搜索用户。

   在更新后的设置，选择**保存**。

## <a name="update-after-deployment"></a>更新部署后

可以更改请求得到批准后的值。 但是，如果未在部署期间启用 JIT 访问，则不能更高版本启用它。

若要更改已部署的托管应用程序的设置：

1. 在门户中，选择管理应用程序。

1. 选择**JIT 配置**并根据需要更改设置。

   ![更改访问设置](./media/approve-just-in-time-access/change-settings.png)

1. 完成后，选择“保存”  。

## <a name="approve-requests"></a>审批请求

当发布服务器上请求访问时，通知您有该请求。 您可以批准 JIT 访问请求直接通过托管应用程序，或在通过 Azure AD Privileged Identity Management 服务的所有托管应用程序。 若要使用在实时访问，必须具有[Azure Active Directory P2 许可证](../active-directory/privileged-identity-management/subscription-requirements.md)。

若要审批请求通过托管应用程序：

1. 选择**JIT 访问**托管应用程序，然后选择**批准请求**。

   ![审批请求](./media/approve-just-in-time-access/approve-requests.png)
 
1. 选择要批准的请求。

   ![选择请求](./media/approve-just-in-time-access/select-request.png)

1. 在表单中，提供适用于审批和选择的原因**批准**。

若要审批请求通过 Azure AD Privileged Identity Management:

1. 选择**所有服务**并开始搜索**Azure AD Privileged Identity Management**。 从可用选项中选择它。

   ![搜索服务](./media/approve-just-in-time-access/search.png)

1. 选择**批准请求**。

   ![选择批准的请求](./media/approve-just-in-time-access/select-approve-requests.png)

1. 选择**Azure 托管应用程序**，并选择要批准的请求。

   ![选择请求](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>后续步骤

若要了解有关发布使用 JIT 访问托管应用程序的信息，请参阅[请求在 Azure 托管应用程序中实时访问](request-just-in-time-access.md)。
