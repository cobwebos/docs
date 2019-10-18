---
title: 审批实时访问-Azure 托管应用程序
description: 介绍 Azure 托管应用程序的使用者如何批准请求对托管应用程序的实时访问。
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: dd5c3f21b1a4b71d129ccbebeaa7ee66274a672f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529124"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>配置和批准 Azure 托管应用程序的实时访问

作为托管应用程序的使用者，你可能不会为发布者提供对托管资源组的永久访问权限。 为了使你能够更好地控制如何授予对托管资源的访问权限，Azure 托管应用程序提供了一种称为实时（JIT）访问的功能，该功能目前处于预览阶段。 它使你能够批准发布者有权访问资源组的时间和时间。 发布者可以在该时间进行所需的更新，但当该时间结束时，发布者的访问权限将过期。

用于授予访问权限的工作流为：

1. 发布者将托管应用程序添加到 marketplace，并指定 JIT 访问可用。

1. 在部署过程中，将为托管应用程序的实例启用 JIT 访问。

1. 部署后，可以更改 JIT access 的设置。

1. 发布者发送访问请求。

1. 你批准该请求。

本文重点介绍使用者执行的操作，以便启用 JIT 访问和批准请求。 若要了解如何使用 JIT 访问发布托管应用程序，请参阅[在 Azure 托管应用程序中请求实时访问](request-just-in-time-access.md)。

> [!NOTE]
> 若要使用实时访问，必须有[Azure Active Directory P2 许可证](../active-directory/privileged-identity-management/subscription-requirements.md)。

## <a name="enable-during-deployment"></a>在部署过程中启用

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 查找启用了 JIT 的托管应用程序的 marketplace 条目。 选择**创建**。

1. 为新托管应用程序提供值时， **JIT 配置**步骤允许您启用或禁用对托管应用程序的 JIT 访问。 对于 "**启用 JIT 访问**"，选择 **"是"** 。 对于在 marketplace 中启用了 JIT 的托管应用程序，默认情况下选择此选项。

   ![配置访问权限](./media/approve-just-in-time-access/configure-jit-access.png)

   只能在部署期间启用 JIT 访问。 如果选择 "**否**"，则发布者将获取对托管资源组的永久访问权限。 以后不能启用 JIT 访问。

1. 若要更改默认的审批设置，请选择 "**自定义 JIT 配置**"。

   ![自定义访问](./media/approve-just-in-time-access/customize-jit-access.png)

   默认情况下，启用了 JIT 的托管应用程序具有以下设置：

   * 审批模式–自动
   * 最长访问持续时间–8小时
   * 审批者–无

   当审批模式设置为 "**自动**" 时，审批者将收到每个请求的通知，但会自动批准请求。 如果设置为 "**手动**"，则审批者将收到每个请求的通知，其中一个请求必须批准。

   "激活最长持续时间" 指定发布者可以请求访问托管资源组的最长时间。

   审批者列表是可批准 JIT 访问请求的 Azure Active Directory 用户。 若要添加审批者，请选择 "**添加审批者**" 并搜索用户。

   更新设置后，请选择 "**保存**"。

## <a name="update-after-deployment"></a>部署后更新

您可以更改请求的批准方式的值。 但是，如果在部署过程中未启用 JIT 访问，以后将无法启用。

更改已部署的托管应用程序的设置：

1. 在门户中，选择 "管理应用程序"。

1. 选择 " **JIT 配置**" 并根据需要更改设置。

   ![更改访问设置](./media/approve-just-in-time-access/change-settings.png)

1. 完成后，选择“保存”。

## <a name="approve-requests"></a>审批请求

当发布者请求访问时，会收到请求的通知。 您可以直接通过托管应用程序或通过 Azure AD Privileged Identity Management 服务在所有托管应用程序中批准 JIT 访问请求。 若要使用实时访问，必须有[Azure Active Directory P2 许可证](../active-directory/privileged-identity-management/subscription-requirements.md)。

若要通过托管应用程序批准请求：

1. 为托管应用程序选择 " **JIT 访问**"，然后选择 "**批准请求**"。

   ![审批请求](./media/approve-just-in-time-access/approve-requests.png)
 
1. 选择要批准的请求。

   ![选择请求](./media/approve-just-in-time-access/select-request.png)

1. 在窗体中，提供审批原因并选择 "**批准**"。

通过 Azure AD Privileged Identity Management 批准请求：

1. 选择 "**所有服务**" 并开始搜索**Azure AD Privileged Identity Management**。 从可用选项中选择它。

   ![搜索服务](./media/approve-just-in-time-access/search.png)

1. 选择 "**批准请求**"。

   ![选择批准请求](./media/approve-just-in-time-access/select-approve-requests.png)

1. 选择 " **Azure 托管应用程序**"，然后选择要批准的请求。

   ![选择请求](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>后续步骤

若要了解如何使用 JIT 访问发布托管应用程序，请参阅[在 Azure 托管应用程序中请求实时访问](request-just-in-time-access.md)。
