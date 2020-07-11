---
title: 配置部署凭据
description: 了解 Azure 应用服务中有哪些类型的部署凭据，以及如何配置和使用这些凭据。
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 45d2ec6cf4b2a54b899036d932bc310caede3c29
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223850"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>为 Azure 应用服务配置部署凭据
[Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)支持两种类型的凭据，这些凭据适用于[本地 GIT 部署](deploy-local-git.md)和 [FTP/S 部署](deploy-ftp.md)。 这些凭据与 Azure 订阅凭据不同。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>配置用户级别凭据

可以在任何应用的[资源页面](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)中配置用户级凭据。 无论在哪个应用中配置这些凭据，这些凭据都适用于 Azure 帐户中的所有应用和所有订阅。 

### <a name="in-the-cloud-shell"></a>在 Cloud Shell 中

若要在 [Cloud Shell](https://shell.azure.com) 中配置部署用户，请运行 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令。 将 \<username> 和 \<password> 替换为部署用户的用户名和密码。 

- 用户名在 Azure 中必须唯一，并且为了本地Git推送，不能包含“@”符号。 
- 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 输出会将该密码显示为 `null`。 如果收到 `'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 `'Bad Request'. Details: 400` 错误，请使用更强的密码。 

### <a name="in-the-portal"></a>在门户中

在 Azure 门户中，用户必须至少有一个应用，才能访问“部署凭据”页。 若要配置用户级凭据，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，从左侧菜单中选择“应用服务” > “\<any_app>” > “部署中心” > “FTP” > “仪表板”。

    ![演示如何从 Azure 应用 Services 中的部署中心选择 FTP 面板。](./media/app-service-deployment-credentials/access-no-git.png)

    或者，如果已配置了 Git 部署，请选择“应用程序服务” > “&lt;any_app>” > “部署中心” > “FTP/凭据”。

    ![演示如何在已配置的 Git 部署 Azure 应用 Services 中的 "部署中心" 中选择 FTP 面板。](./media/app-service-deployment-credentials/access-with-git.png)

2. 选择“用户凭据”，配置用户名和密码，然后选择“保存凭据” 。

设置部署凭据后，可以在应用的“概述”页中找到 Git 部署用户名，

![演示如何在应用的 "概述" 页上查找 Git 部署用户名。](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

如果配置了 Git 部署，则该页显示 **Git/部署用户名**；否则，显示 **FTP/部署用户名**。

> [!NOTE]
> Azure 不会显示用户级部署密码。 如果忘记密码，可以按照本部分的步骤重置凭据。
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>将用户级凭据用于 FTP/FTPS

使用用户级凭据向 FTP/FTPS 终结点进行身份验证时需要使用以下格式的用户名：`<app-name>\<user-name>`

由于用户级凭据链接到用户而不是特定资源，因此用户名必须采用此格式才能将登录操作定向到正确的应用终结点。

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>设置和重置应用级凭据
若要获取应用级凭据，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，从左侧菜单中选择“应用程序服务” > “&lt;any_app>” > “部署中心” > “FTP/凭据”。

2. 选择“应用凭据”，然后选择“复制”链接以复制用户名或密码 。

若要重置应用级别凭据，请选择相同对话框中的“重置凭据”。

## <a name="next-steps"></a>后续步骤

了解如何使用这些凭据通过[本地 Git](deploy-local-git.md) 或 [FTP/S](deploy-ftp.md) 部署应用。
