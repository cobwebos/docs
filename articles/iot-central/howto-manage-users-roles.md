---
title: 在 Azure 中管理用户和角色 IoT Central 应用程序 |Microsoft Docs
description: 作为管理员, 如何管理 Azure IoT Central 应用程序中的用户和角色
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729238"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>在 IoT Central 应用程序中管理用户和角色

本文介绍如何以管理员身份在 Azure IoT Central 应用程序中添加、编辑和删除用户, 以及如何在 Azure IoT Central 应用程序中管理角色。

只有 Azure IoT Central 应用程序的“管理员”角色才能访问和使用“管理”部分。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”角色。


## <a name="add-users"></a>添加用户

每个用户必须有一个用户帐户才能登录和访问 Azure IoT Central 应用程序。 Azure IoT Central 支持 Microsoft 帐户 (MSA) 和 Azure Active Directory (Azure AD) 帐户。 Azure IoT Central 目前不支持 Azure Active Directory 组。

有关详细信息，请参阅 [Microsoft 帐户帮助](https://support.microsoft.com/products/microsoft-account?category=manage-account)和[快速入门：将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

1. 若要将用户添加到 IoT Central 应用程序，请转到“用户”页中的“管理”部分。

    ![用户列表](media/howto-administer/image1.png)

1. 若要添加用户，在“用户”页上，选择“+ 添加用户”。

1. 从“角色”下拉菜单中为用户选择一个角色。 在本文的[管理角色](#manage-roles)部分详细了解角色。

    ![角色选择](media/howto-administer/image3.png)

    > [!NOTE]
    >  若要批量添加用户，请输入想要添加的所有用户的用户 ID，并用分号分隔。 从“角色”下拉菜单中选择一个角色。 再选择“保存”。

### <a name="edit-the-roles-that-are-assigned-to-users"></a>编辑分配给用户的角色

角色分配后无法再对其进行更改。 若要更改分配给某个用户的角色，请删除该用户，然后使用不同的角色再次添加该用户。

> [!NOTE]
> 分配的角色特定于 IoT Central 应用程序, 不能从 Azure 门户进行管理。

## <a name="delete-users"></a>删除用户

若要删除用户，选择“用户”页上的一个或多个复选框。 然后选择“删除”。

## <a name="manage-roles"></a>管理角色

使用角色可以控制组织中的哪些人可以在 IoT Central 中执行各种任务。 可将三种角色分配给应用程序的用户。

### <a name="administrator"></a>管理员

充当“管理员”角色的用户有权访问应用程序中的所有功能。

创建应用程序的用户会自动分配到“管理员”角色。 必须始终有一个用户充当“管理员”角色。

### <a name="application-builder"></a>应用程序生成器

充当“应用程序构建人员”角色的用户可以在应用程序中执行任何操作，但无法管理应用程序。 构建者可以创建、编辑和删除设备模板和设备, 管理设备集, 以及运行分析和作业。 构建人员无权访问应用程序的“管理”部分。

### <a name="application-operator"></a>应用程序操作员

充当“应用程序操作员”角色的用户无法对设备模板进行更改，也无法管理应用程序。 操作员可以添加和删除设备、管理设备集以及运行分析和作业。 操作员无权访问“应用程序构建人员”和“管理”页。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何管理 Azure IoT Central 中的用户和角色, 接下来是了解如何在 Azure IoT Central 中[查看帐单](howto-view-bill.md)。
