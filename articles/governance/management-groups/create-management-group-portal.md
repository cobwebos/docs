---
title: 使用门户创建管理组
description: 本快速入门使用 Azure 门户创建管理组，将资源整理到资源层次结构中。
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: c8cb7b8bd3ad33d1f315670bfbb1782972e6e97f
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661732"
---
# <a name="quickstart-create-a-management-group"></a>快速入门：创建管理组

管理组是一些容器，可以帮助跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](overview.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。 有关详细信息，请参阅[管理组的初始设置](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- 如果未启用[层次结构保护](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，则租户中的任何 Azure AD 用户即使未分配有管理组写入权限，也可创建管理组。 这个新的管理组成为根管理组或[默认管理组](./how-to/protect-resource-hierarchy.md#setting---default-management-group)的子级，并且创建者分配有“所有者”角色。 管理组服务支持此功能，因此不需要在根级别分配角色。 创建根管理组时，用户没有访问权限。 为避免在查找 Azure AD 全局管理员以开始使用管理组方面遇到阻碍，我们允许在根级别创建初始管理组。

### <a name="create-in-portal"></a>在门户中创建

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “管理组”********

1. 连接管理组

1. 选择“添加管理组”。****

   :::image type="content" source="./media/main.png" alt-text="显示子管理组和订阅的“管理组”页的屏幕截图。" border="false":::

1. 选择“新建”，然后填写管理组 ID 字段****。

   - ****“管理组 ID”是用来在此管理组上提交命令的目录唯一标识符。 此标识符一旦创建便无法再编辑，因为它用来在整个 Azure 系统中标识这个组。 [根管理组](./overview.md#root-management-group-for-each-directory)是自动创建的，其 ID 为 Azure Active Directory ID。 对于所有其他管理组，请分配唯一的 ID。
   - 显示名称字段是在 Azure 门户中显示的名称。 创建管理组时，单独的显示名称是一个可选字段，并且可以随时更改。

   :::image type="content" source="./media/create_context_menu.png" alt-text="显示子管理组和订阅的“管理组”页的屏幕截图。":::

1. 选择“保存”。****

## <a name="clean-up-resources"></a>清理资源

若要删除创建的管理组，请执行以下步骤：

1. 选择“所有服务” > “管理组”********

1. 连接管理组

1. 找到前面创建的管理组，选择它，然后选择名称旁边的“详细信息”****。
   然后选择“删除”并确认提示****。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个管理组来整理资源层次结构。 管理组可以包含订阅或其他管理组。

若要详细了解管理组以及如何管理资源层次结构，请继续学习：

> [!div class="nextstepaction"]
> 使用管理组管理资源