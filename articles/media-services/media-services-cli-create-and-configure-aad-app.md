---
title: 使用 CLI 2.0 创建 Azure AD 应用程序，并将它配置为访问 Azure 媒体服务 API | Microsoft Docs
description: 本主题展示了如何使用 CLI 2.0 创建 Azure AD 应用程序，并将它配置为访问 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: b640455b151d0e5d4701b8c076ee1a587b92f5b6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>使用 CLI 2.0 创建 Azure AD 应用程序，并将它配置为访问 Azure 媒体服务 API

本主题展示了如何使用 CLI 2.0 创建 Azure Active Directory (Azure AD) 应用程序和服务主体，以便访问 Azure 媒体服务资源。 

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
- 一个媒体服务帐户。 有关详细信息，请参阅[利用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。

## <a name="use-the-azure-cloud-shell"></a>使用 Azure Cloud Shell

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在门户的顶部导航窗格中启动 Cloud Shell。

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

有关详细信息，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)。

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>使用 CLI 2.0 创建 Azure AD 应用程序，并将它配置为访问媒体帐户
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

例如：

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

在此示例中，作用域是媒体服务帐户的完整资源路径。 不过，作用域可以为任意级别。

例如，作用域可以为下列级别之一：
 
* 订阅级别。
* 资源组级别。
* 资源级别（例如，媒体帐户）。

有关详细信息，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

另请参阅[使用 Azure 命令行接口管理基于角色的访问控制](../role-based-access-control/role-assignments-cli.md)。 

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-portal-upload-files.md)。
