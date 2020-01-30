---
title: 配置部署凭据
description: 了解 Azure App Service 以及如何配置和使用部署凭据的类型。
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843780"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>为 Azure 应用服务配置部署凭据
[Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)支持两种类型的凭据，这些凭据适用于[本地 GIT 部署](deploy-local-git.md)和 [FTP/S 部署](deploy-ftp.md)。 这些凭据与 Azure 订阅凭据不同。

* **用户级凭据**：一组适用于整个 Azure 帐户的凭据。 需要部署到任何订阅（Azure 帐户有权对其进行访问）中的任何应用的应用服务时，可以使用这组凭据。 这是在门户 GUI（例如应用的[资源页](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)的“概览”和“属性”）中呈现的默认组。 当通过基于角色的访问控制（RBAC）或 coadmin 权限向用户授予应用访问权限时，该用户可以使用其自己的用户级别的凭据，直到吊销访问权限。 请勿与其他 Azure 用户共享这些凭据。

* **应用级凭据**：一组适用于每个应用的凭据。 若要只部署到该应用，则可使用这组凭据。 每个应用的凭据在其创建时自动生成。 这些凭据不能手动进行配置，但可随时进行重置。 对于通过（RBAC）向其授予应用级凭据访问权限的用户，该用户必须是应用程序的参与者或更高版本（包括网站参与者内置角色）。 读者不可进行发布，因此无法访问这些凭据。

## <a name="userscope"></a>配置用户级凭据

可以在应用的[资源页](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)中配置用户级凭据。 不管是在哪个应用中配置这些凭据，该凭据适用于所有应用以及 Azure 帐户中的所有订阅。 

### <a name="in-the-cloud-shell"></a>在 Cloud Shell

若要在[Cloud Shell](https://shell.azure.com)中配置部署用户，请运行[az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set)命令。 将 \<username> 和 \<password> 替换为部署用户名和密码。 

- 用户名必须在 Azure 中唯一，并且为了本地 Git 推送，不能包含“@”符号。 
- 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 输出会将该密码显示为 `null`。 如果收到 `'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 `'Bad Request'. Details: 400` 错误，请使用更强的密码。 

### <a name="in-the-portal"></a>在门户中

在 Azure 门户中，必须至少有一个应用，才能访问 "部署凭据" 页面。 配置用户级别凭据的步骤：

1. 在[Azure 门户](https://portal.azure.com)中，从左侧菜单中选择 "**应用服务**" >  **\<any_app >** ** >  > **  > 

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    或者，如果已配置了 Git 部署，请选择 "**应用服务**" >  **&lt;any_app >** ** > ** " **FTP/凭据**"。

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. 选择 "**用户凭据**"，配置 "用户名" 和 "密码"，然后选择 "**保存凭据**"。

设置部署凭据后，可以在应用的 "**概述**" 页中找到*Git*部署用户名，

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

如果配置了 Git 部署，则页面将显示**git/部署用户名**;否则为**FTP/部署用户名**。

> [!NOTE]
> Azure 不会显示用户级部署密码。 如果忘记密码，可以按照本部分的步骤重置凭据。
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>通过 FTP/FTPS 使用用户级凭据

使用用户级凭据对 FTP/FTPS 终结点进行身份验证时，请使用以下格式 requirers 用户名： `<app-name>\<user-name>`

由于用户级凭据链接到用户，而不是特定资源，因此用户名必须采用以下格式才能将登录操作定向到正确的应用终结点。

## <a name="appscope"></a>获取和重置应用级凭据
获取应用级凭据：

1. 在[Azure 门户](https://portal.azure.com)中，从左侧菜单中选择 "**应用服务**" >  **&lt;any_app >**  > "**部署中心** > **FTP/凭据**"。

2. 选择 "**应用凭据**"，并选择 "**复制**" 链接来复制用户名或密码。

若要重置应用级凭据，请在同一对话框中选择 "**重置凭据**"。

## <a name="next-steps"></a>后续步骤

了解如何使用这些凭据通过[本地 Git](deploy-local-git.md) 或 [FTP/S](deploy-ftp.md) 部署应用。
