---
title: 在 Azure Marketplace 中查找租户 ID、对象 ID 和合作伙伴关联详细信息
description: 如何查找 Azure Marketplace 中订阅 ID 的租户 ID、对象 ID 和合作伙伴关联详细信息。
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: c35e42aaf5e4bd31a54f807969c3671ecc5668ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91814390"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>查找租户 ID、对象 ID 和合作伙伴关联详细信息

本文介绍了如何查找租户 ID、对象 ID 和合作伙伴关联详细信息以及各自的订阅 Id。

如果需要在 Azure Cloud Shell 中获取这些项的屏幕截图以用于调试帮助，请跳转到 " [查找租户"、"对象" 和 "合作伙伴 ID 关联" 进行调试](#find-ids-for-debugging)。

>[!Note]
> 只有订阅的所有者才有权执行这些步骤。

## <a name="find-tenant-id"></a>查找租户 ID

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 选择“Azure Active Directory” 。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** " 框中。

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

## <a name="find-subscriptions-and-roles"></a>查找订阅和角色

1. 中转到 Azure 门户并选择上述步骤1和步骤2中所述 **Azure Active Directory** 。
2. 选择 **订阅**。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

3. 查看订阅和角色。

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

## <a name="find-partner-id"></a>查找合作伙伴 ID

1. 导航到 "订阅" 页，如前一部分中所述。
2. 选择一个订阅。
3. 在 " **计费**" 下，选择 " **合作伙伴信息**"。

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

## <a name="find-user-object-id"></a>查找用户 (对象 ID) 

1. 使用具有相应管理权限的所需租户中的帐户登录到 [Office 365 管理门户](https://portal.office.com/adminportal/home) 。
2. 在左侧菜单中，展开底部的 " **管理中心** " 部分，然后选择 "Azure Active Directory" 选项以在新的浏览器窗口中启动管理控制台。
3. 选择“用户”。
4. 浏览到或搜索所需的用户，然后选择帐户名称以查看用户帐户的配置文件信息。
5. 对象 ID 位于右侧的标识部分。

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** " 来查找**角色分配**。

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

## <a name="find-ids-for-debugging"></a>查找用于调试的 Id

本部分介绍如何查找租户、对象和合作伙伴 ID 关联以便进行调试。

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 通过选择右上方的 PowerShell 图标来打开 Azure Cloud Shell。

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** " **PowerShell**"。

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** " 框，然后 **创建存储**。 这是一次操作;如果已设置了存储，请转到下一步。

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

5. 创建 (或已) 存储将打开 Azure Cloud Shell "窗口。

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

6. 对于合作伙伴关联详细信息，请在此命令中安装扩展：<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell 将注意是否已安装该扩展：

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

7. 使用以下命令检查合作伙伴详细信息：<br>`az managementpartner show --partner-id xxxxxx`<br>示例：`az managementpartner show --partner-id 4760962`。<br>对齐命令结果的屏幕截图，如下所示：

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Azure 门户中的 Azure Active Directory 图标。&quot;:::

3. 选择“组”。 租户 ID 位于 &quot; **租户信息** ":::

>[!NOTE]
>如果多个订阅需要屏幕截图，请使用以下命令在订阅之间切换：<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>后续步骤

- [支持的国家和地区](sell-from-countries.md)