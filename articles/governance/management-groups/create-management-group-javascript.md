---
title: 快速入门：使用 JavaScript 创建管理组
description: 在本快速入门中，你将使用 JavaScript 创建管理组，将资源整理到资源层次结构中。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: 7a234a6619eafd650451ae5d6bce37388c824f33
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604530"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>快速入门：使用 JavaScript 创建管理组

管理组是一些容器，可以帮助跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](overview.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。 有关详细信息，请参阅[管理组的初始设置](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- 在开始之前，请确保至少已安装 [Node.js](https://nodejs.org/) 的版本 12。

- 如果未启用[层次结构保护](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，则租户中的任何 Azure AD 用户即使未分配有管理组写入权限，也可创建管理组。 这个新的管理组将成为根管理组的子级或[默认管理组](./how-to/protect-resource-hierarchy.md#setting---default-management-group)，并将为创建者分配“所有者”角色。 管理组服务允许此功能，因此不需要在根级别分配角色。 创建根管理组时，用户没有访问权限。 为避免在查找 Azure AD 全局管理员以开始使用管理组方面遇到阻碍，我们允许在根级别创建初始管理组。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>应用程序设置

若要启用 JavaScript 以查询 Azure Resource Graph，必须设置环境。 此设置适用于可使用 JavaScript 的任何环境，包括 [Bash on Windows 10](/windows/wsl/install-win10)。

1. 运行以下命令，设置新的 Node.js 项目。

   ```bash
   npm init -y
   ```

1. 添加对 yargs 模块的引用。

   ```bash
   npm install yargs
   ```

1. 添加对 Azure Resource Graph 模块的引用。

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. 添加对 Azure 身份验证库的引用。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > 在 package.json 中验证 `@azure/arm-managementgroups` 是否为版本 1.1.0 或更高版本，并且 `@azure/ms-rest-nodeauth` 是否为版本 3.0.5 或更高版本 。

## <a name="create-the-management-group"></a>创建管理组

1. 创建一个名为 index.js 的文件，并输入以下代码。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. 在终端中输入以下命令：

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   请确保将每个令牌 `<>` 占位符分别替换为管理组 ID 和管理组易记名称 。

   当脚本尝试进行身份验证时，会在终端中显示类似于以下所示的消息：

   > 若要登录，请使用 Web 浏览器打开页面 https://microsoft.com/devicelogin ，然后输入代码 FGB56WJUGK 进行身份验证。

   在浏览器中进行身份验证后，该脚本将继续运行。

创建管理组的结果将输出到控制台。

## <a name="clean-up-resources"></a>清理资源

如果希望从应用程序中删除已安装的库，请运行以下命令。

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个管理组来整理资源层次结构。 管理组可以包含订阅或其他管理组。

要详细了解管理组以及如何管理资源层次结构，请继续执行以下操作：

> [!div class="nextstepaction"]
> [使用管理组管理资源](./manage.md)