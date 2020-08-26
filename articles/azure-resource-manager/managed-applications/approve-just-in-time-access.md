---
title: 批准实时访问
description: 介绍了 Azure 托管应用程序的使用者如何批准对托管应用程序的实时访问请求。
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651015"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>配置和批准对 Azure 托管应用程序的实时访问

作为托管应用程序的使用者，你可能不愿意让发布者永久访问托管资源组。 为了让你在授予对托管资源的访问权限时能够更好地进行控制，Azure 托管应用程序提供了一种称为实时 (JIT) 访问的功能，该功能目前为预览版。 它使你能够批准发布者有权访问资源组的时间和时长。 发布者可以在该时间内进行所需的更新，但当该时间结束时，发布者的访问权限将过期。

用于授予访问权限的工作流为：

1. 发布者将托管应用程序添加到市场，并指定 JIT 访问可用。

1. 在部署过程中，你为托管应用程序的实例启用 JIT 访问。

1. 在部署后，可以更改 JIT 访问设置。

1. 发布者发送访问请求。

1. 你批准该请求。

本文重点介绍了使用者在启用 JIT 访问和批准请求时要执行的操作。 若要了解如何发布采用 JIT 访问的托管应用程序，请参阅[在 Azure 托管应用程序中请求实时访问](request-just-in-time-access.md)。

> [!NOTE]
> 若要使用实时访问，必须具有 [Azure Active Directory P2 许可证](../../active-directory/privileged-identity-management/subscription-requirements.md)。

## <a name="enable-during-deployment"></a>在部署期间启用

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 查找启用了 JIT 的托管应用程序的市场项。 选择“创建”  。

1. 为新的托管应用程序提供值时，“JIT 配置”  步骤允许你为托管应用程序启用或禁用 JIT 访问。 对于“启用 JIT 访问”，选择“是”。   对于市场中在定义时便启用了 JIT 的托管应用程序，会默认选择此选项。

   ![配置访问权限](./media/approve-just-in-time-access/configure-jit-access.png)

   只能在部署期间启用 JIT 访问。 如果选择“否”  ，则发布者将永久可以访问托管资源组。 无法在以后启用 JIT 访问。

1. 若要更改默认的审批设置，请选择“自定义 JIT 配置”  。

   ![自定义访问权限](./media/approve-just-in-time-access/customize-jit-access.png)

   默认情况下，启用了 JIT 的托管应用程序具有以下设置：

   * 审批模式–自动
   * 最长访问持续时间–8小时
   * 审批者–无

   当审批模式设置为“自动”  时，审批者针对每个请求都会收到通知，但请求会自动批准。 当设置为“手动”  时，审批者针对每个请求都会收到通知，并且必须由某个审批者批准请求。

   “激活最大持续时间”指定发布者可以请求访问托管资源组的最长时间。

   审批者列表是可以批准 JIT 访问请求的 Azure Active Directory 用户。 若要添加审批者，请选择“添加批准者”  并搜索用户。

   更新设置后，选择“保存”  。

## <a name="update-after-deployment"></a>在部署后更新

你可以更改有关如何批准请求的值。 但是，如果在部署期间未启用 JIT 访问，则以后将无法启用。

若要更改已部署的托管应用程序的设置，请执行以下操作：

1. 在门户中，选择“管理应用程序”。

1. 选择“JIT 配置”  并根据需要更改设置。

   ![更改访问设置](./media/approve-just-in-time-access/change-settings.png)

1. 完成后，选择“保存”  。

## <a name="approve-requests"></a>审批请求

当发布者请求访问时，你会收到请求通知。 你可以直接通过托管应用程序或通过 Azure AD Privileged Identity Management 服务在所有托管应用程序中批准 JIT 访问请求。 若要使用实时访问，必须具有 [Azure Active Directory P2 许可证](../../active-directory/privileged-identity-management/subscription-requirements.md)。

若要通过托管应用程序批准请求，请执行以下操作：

1. 为托管应用程序选择“JIT 访问”  ，并选择“批准请求”  。

   ![审批请求](./media/approve-just-in-time-access/approve-requests.png)
 
1. 选择要批准的请求。

   ![选择请求](./media/approve-just-in-time-access/select-request.png)

1. 在窗体中，提供批准原因并选择“批准”  。

若要通过 Azure AD Privileged Identity Management 批准请求，请执行以下操作：

1. 选择“所有服务”  ，然后开始搜索“Azure AD Privileged Identity Management”  。 从可用选项中选择它。

   ![搜索服务](./media/approve-just-in-time-access/search.png)

1. 选择“审批请求”  。

   ![选择“审批请求”](./media/approve-just-in-time-access/select-approve-requests.png)

1. 选择“Azure 托管应用程序”  ，然后选择要批准的请求。

   ![选择请求](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>后续步骤

若要了解如何发布采用 JIT 访问的托管应用程序，请参阅[在 Azure 托管应用程序中请求实时访问](request-just-in-time-access.md)。
