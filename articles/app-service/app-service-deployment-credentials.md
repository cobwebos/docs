---
title: "Azure 应用服务部署凭据 | Microsoft Docs"
description: "了解如何使用 Azure 应用服务部署凭据。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 2f8691b0aadbd97e87996c2683849a6db3b74618
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>为 Azure 应用服务配置部署凭据
[Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)支持两种类型的凭据，这些凭据适用于[本地 GIT 部署](app-service-deploy-local-git.md)和 [FTP/S 部署](app-service-deploy-ftp.md)。 这与 Azure Active Directory 凭据不同。

* **用户级凭据**：一组适用于整个 Azure 帐户的凭据。 需要部署到任何订阅（Azure 帐户有权对其进行访问）中的任何应用的应用服务时，可以使用这组凭据。 这些是默认的凭据组，可以在“应用服务” > **&lt;app_name>** > “部署凭据”中对其进行配置。 这也是在门户 GUI（例如应用的[资源边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)的“概览”和“属性”）中呈现的默认组。

    > [!NOTE]
    > 通过基于角色的访问控制 (RBAC) 或共同管理员权限委派对 Azure 资源的访问权限时，每个接收应用访问权限的 Azure 用户都可以使用其个人用户级凭证，直到撤消访问权限。 不应与其他 Azure 用户共享这些部署凭据。
    >
    >

* **应用级凭据**：一组适用于每个应用的凭据。 若要只部署到该应用，则可使用这组凭据。 每个应用的凭据在创建应用时自动生成，可以在应用的发布配置文件中找到。 不能手动配置这些凭据，但可以随时针对某个应用重置它们。

    > [!NOTE]
    > 若要通过基于角色的访问控制 (RBAC) 向某用户授予这些凭据的访问权限，需要在 Web 应用上将其设置为参与者或更高级别。 读取器不可发布，因此无法访问这些凭据。
    >
    >

## <a name="userscope"></a>设置和重置用户级凭据

可以在应用的[资源边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)中配置用户级凭据。 不管是在哪个应用中配置这些凭据，该凭据适用于所有应用以及 Azure 帐户中的所有订阅。 

配置用户级别凭据的步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，单击“应用服务”> **&lt;any_app>** > “部署凭据”。

    > [!NOTE]
    > 在门户中必须至少有一个应用，才能访问部署凭据边栏选项卡。 但在使用 [Azure CLI](/cli/azure/webapp/deployment/user#set) 时，却可在没有应用的情况下配置用户级凭据。

2. 配置用户名和密码，并单击“保存”。

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

设置部署凭据以后，即可在应用的“概述”中查找 *Git* 部署用户名，

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

以及在应用的“属性”中查找 *FTP* 部署用户名。

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure 不会显示用户级部署密码。 如果忘记了密码，则无法检索它。 但是，可以按照本部分的步骤重置凭据。
>
>  

## <a name="appscope"></a>获取和重置应用级凭据
就应用服务中的每个应用来说，其应用级凭据存储在 XML 发布配置文件中。

获取应用级凭据：

1. 在 [Azure 门户](https://portal.azure.com)中，单击“应用服务”> **&lt;any_app>** > “概览”。

2. 单击“...更多” > “获取发布配置文件”，然后就会开始下载 .PublishSettings 文件。

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. 打开 .PublishSettings 文件，找到属性为 `publishMethod="FTP"` 的 `<publishProfile>` 标记。 然后，获取其 `userName` 和 `password` 属性。
这些是应用级凭据。

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    与用户级凭据类似，FTP 部署用户名采用 `<app_name>\<username>` 格式，Git 部署用户名则为 `<username>`，没有前面的 `<app_name>\`。

重置应用级凭据：

1. 在 [Azure 门户](https://portal.azure.com)中，单击“应用服务”> **&lt;any_app>** > “概览”。

2. 单击“...更多” > “重置发布配置文件”。 单击“是”确认重置。

    重置操作会使以前下载的 .PublishSettings 文件无效。

## <a name="next-steps"></a>后续步骤

了解如何使用这些凭据通过[本地 Git](app-service-deploy-local-git.md) 或 [FTP/S](app-service-deploy-ftp.md) 部署应用。

