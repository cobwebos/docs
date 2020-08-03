---
author: baanders
description: Azure 数字孪生安装程序中的步骤概述的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407415"
---
>[!NOTE]
>这些操作旨在由在 Azure 订阅上具有*所有者*角色的用户完成。 尽管有些部分可以在没有此提升权限的情况下完成，但需要所有者的合作来完全设置一个可用的实例。 有关详细信息，请查看下面的[*先决条件： Required 权限*](#prerequisites-permission-requirements)部分。

新的 Azure 数字孪生实例的完全安装由三个部分组成：
1. **创建实例**
2. **设置用户访问权限**： azure 用户需要在 Azure 数字孪生实例上拥有*Azure 数字孪生所有者（预览版）* 角色才能管理它及其数据。 在此步骤中，你作为 Azure 订阅的所有者，会将此角色分配给将管理 Azure 数字孪生实例的人员。 这可能是你自己或组织中的其他人。
3. **设置客户端应用程序的访问权限**：通常，编写将用于与实例进行交互的客户端应用程序。 为了使该客户端应用能够访问 Azure 数字孪生，需要在客户端应用程序将用于向实例进行身份验证的[Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)中设置*应用注册*。

若要继续，你将需要一个 Azure 订阅。 你可以在[此处](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免费设置一个。
