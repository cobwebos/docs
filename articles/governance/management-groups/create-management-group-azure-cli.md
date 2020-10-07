---
title: 快速入门：使用 Azure CLI 创建管理组
description: 本快速入门使用 Azure CLI 创建管理组，将资源整理到资源层次结构中。
ms.date: 08/31/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: f07ae46c95f9ab9cc1ad973204ac5c50320fdf46
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89236843"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>快速入门：使用 Azure CLI 创建管理组

管理组是一些容器，可以帮助跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](overview.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。 有关详细信息，请参阅[管理组的初始设置](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- 本快速入门需要运行 Azure CLI 版本 2.0.76 或更高版本，以便在本地安装并使用 CLI。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

- 如果未启用[层次结构保护](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，则租户中的任何 Azure AD 用户即使未分配有管理组写入权限，也可创建管理组。 这个新的管理组将成为根管理组的子级或[默认管理组](./how-to/protect-resource-hierarchy.md#setting---default-management-group)，并将为创建者分配“所有者”角色。 管理组服务允许此功能，因此不需要在根级别分配角色。 创建根管理组时，用户没有访问权限。 为避免在查找 Azure AD 全局管理员以开始使用管理组方面遇到阻碍，我们允许在根级别创建初始管理组。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>在 Azure CLI 中创建

在 Azure CLI 中，使用 [az account management-group create](/cli/azure/account/management-group#az-account-management-group-create) 命令创建新的管理组。 在本例中，管理组名称为 Contoso。

```azurecli-interactive
az account management-group create --name 'Contoso'
```

**name** 是要创建的唯一标识符。 此 ID 由其他命令用来引用此组，并且以后无法更改。

如果希望管理组在 Azure 门户中显示一个不同的名称，请添加 **display-name** 参数。 例如，若要创建 GroupName 为 Contoso 且显示名称为“Contoso Group”的管理组，请使用以下命令：

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

在上述示例中，新的管理组是在根管理组下创建的。 若要指定一个不同的管理组作为父级，请使用 **parent** 参数并提供父组的名称。

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>清理资源

若要删除上面创建的管理组，请使用 [az account management-group delete](/cli/azure/account/management-group#az-account-management-group-delete) 命令：

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个管理组来整理资源层次结构。 管理组可以包含订阅或其他管理组。

要详细了解管理组以及如何管理资源层次结构，请继续执行以下操作：

> [!div class="nextstepaction"]
> [使用管理组管理资源](./manage.md)