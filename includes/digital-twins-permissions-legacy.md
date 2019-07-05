---
title: include 文件
description: include 文件
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 06/26/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 9a5b3b04287a8b732d01bd8fe4610e073332da0d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478961"
---
1. 在 [Azure 门户](https://portal.azure.com)中，从左窗格打开 **Azure Active Directory**，然后打开“属性”窗格。  将“目录 ID”复制到一个临时文件。  将在下一部分使用此值来配置示例应用程序。

    ![Azure Active Directory 目录 ID](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. 在中[Azure 门户](https://portal.azure.com)，打开**Azure Active Directory**从左窗格中，和然后打开**应用注册 （旧版）** 窗格。 选择**新建应用程序注册**按钮。

1. 在“名称”  框中，为此应用注册提供一个友好名称。 选择“本机”作为“应用程序类型”，然后选择 `https://microsoft.com` 作为“重定向 URI”。    选择“创建”  。

    ![“创建”窗格](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  打开注册的应用，将“应用程序 ID”字段的值复制到一个临时文件中。  该值用于标识 Azure Active Directory 应用。 将在下一部分使用此应用程序 ID 来配置示例应用程序。

    ![Azure Active Directory 应用程序 ID](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. 打开应用注册窗格。 选择“设置”   > “所需权限”  ，然后执行以下操作：

   a. 在左上角选择“添加”  ，打开“添加 API 访问权限”窗格  。

   b. 选择“选择 API”，搜索“Azure 数字孪生”。   如果搜索没有找到该 API，请改为搜索“Azure 智能空间”。 

   c. 选择“Azure 数字孪生(Azure 智能空间服务)”选项，然后选择“选择”。  

   d. 选择“选择权限”。  选中“读/写访问权限”委托权限复选框，然后选择“选择”。  

   e. 在“添加 API 访问权限”窗格中，选择“完成”   。

   f. 在“所需的权限”窗格中选择“授予权限”按钮，接受显示的确认。   如果此 api 不授予权限，请联系您的管理员联系。

      ![“所需的权限”窗格](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 