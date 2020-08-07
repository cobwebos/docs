---
author: baanders
description: Azure 数字孪生安装程序中的 "访问权限" 步骤的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 18aa039977beeff3f6b16249822fbd98370ca404
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408240"
---
设置 Azure 数字孪生实例后，通常可以通过客户端应用程序与该实例进行交互。 若要创建正常运行的客户端应用程序，你需要确保客户端应用能够对 Azure 数字孪生进行身份验证。 为此，可设置[Azure Active Directory (Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md)客户端应用) **应用注册**以便使用。

通过此应用注册，你可以配置对[Azure 数字孪生 api](../articles/digital-twins/how-to-use-apis-sdks.md)的访问权限。 以后，客户端应用程序将对应用程序注册进行身份验证，因此，会将配置的访问权限授予 Api。

>[!TIP]
> 作为订阅所有者，你可能更倾向于为每个新的 Azure 数字孪生实例设置新的应用注册，*或*只是执行此操作一次，建立单个应用注册，将在订阅中的所有 Azure 数字孪生实例之间共享。

### <a name="create-the-registration"></a>创建注册