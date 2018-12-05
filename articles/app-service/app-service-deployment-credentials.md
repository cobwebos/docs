---
title: 为 Azure 应用服务配置部署凭据 | Microsoft Docs
description: 了解如何使用 Azure 应用服务部署凭据。
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.openlocfilehash: a04057023308087adbf449b8f91af6a473c11c1e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312089"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>为 Azure 应用服务配置部署凭据
[Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)支持两种类型的凭据，这些凭据适用于[本地 GIT 部署](app-service-deploy-local-git.md)和 [FTP/S 部署](app-service-deploy-ftp.md)。 这与 Azure Active Directory 凭据不同。

* **用户级凭据**：一组适用于整个 Azure 帐户的凭据。 需要部署到任何订阅（Azure 帐户有权对其进行访问）中的任何应用的应用服务时，可以使用这组凭据。 这是在门户 GUI（例如应用的[资源页](../azure-resource-manager/resource-group-portal.md#manage-resources)的“概览”和“属性”）中呈现的默认组。 当通过基于角色的访问控制 (RBAC) 或共同管理员权限授予用户应用访问权限时，该用户便可使用其用户级别的凭据直到被撤销访问权限。 请勿与其他 Azure 用户共享这些凭据。

* **应用级凭据**：一组适用于每个应用的凭据。 若要只部署到该应用，则可使用这组凭据。 每个应用的凭据在其创建时自动生成。 这些凭据不能手动进行配置，但可随时进行重置。 如果要通过 (RBAC) 授予用户访问应用级别凭据的权限，该用户必须是应用的参与者或更高级别身份。 读者不可进行发布，因此无法访问这些凭据。

## <a name="userscope"></a>设置和重置用户级凭据

可以在应用的[资源页](../azure-resource-manager/resource-group-portal.md#manage-resources)中配置用户级凭据。 不管是在哪个应用中配置这些凭据，该凭据适用于所有应用以及 Azure 帐户中的所有订阅。 

配置用户级别凭据的步骤：

1. 在 [Azure](https://portal.azure.com) 门户中，单击左侧菜单中的“应用服务” > &lt;any_app> > “部署中心” > “部署凭据”。

    在门户中必须至少有一个应用，才能访问部署凭据页。 但在使用 [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 时，却可在没有应用的情况下配置用户级凭据。

2. 单击“用户凭据”，配置用户名和密码，然后单击“保存凭据”。

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

设置部署凭据以后，即可在应用的“概述”中查找 *Git* 部署用户名，

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

以及在应用的“属性”中查找 FTP 部署用户名。

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure 不会显示用户级部署密码。 如果忘记密码，可以按照本部分的步骤重置凭据。
>
>  

## <a name="appscope"></a>获取和重置应用级凭据
获取应用级凭据：

1. 在 [Azure](https://portal.azure.com) 门户中，单击左侧菜单中的“应用服务” > &lt;any_app> > “部署中心” > “部署凭据”。

2. 单击“应用凭据”，然后单击“复制”链接以复制用户名或密码。

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

若要重置应用级别凭据，请单击相同对话框中的“重置凭据”。

## <a name="next-steps"></a>后续步骤

了解如何使用这些凭据通过[本地 Git](app-service-deploy-local-git.md) 或 [FTP/S](app-service-deploy-ftp.md) 部署应用。
