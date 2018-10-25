---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: b26b88d0e089217fa9915bdbdcb8f913731bcc67
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988171"
---
## <a name="register-your-application"></a>注册应用程序

可以通过以下两种方式之一注册应用程序。

### <a name="option-1-express-mode"></a>选项 1：快速模式

可以通过执行以下操作快速注册应用程序：
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)。

2. 选择“添加应用”。

3. 在“应用程序名称”框中输入应用程序的名称。

4. 确保选中了“指导式设置”复选框，然后选择“创建”。

5. 按照说明获取应用程序 ID，并将其粘贴到代码中。

### <a name="option-2-advanced-mode"></a>选项 2：高级模式

若要注册应用程序并将应用程序注册信息添加到解决方案，请执行以下操作：
1. 如果还没有注册应用程序，请转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)。

2. 选择“添加应用”。

3. 在“应用程序名称”框中输入应用程序的名称。

4. 确保未选中“指导式设置”复选框，然后选择“创建”。

5. 依次选择“添加平台”、“本机应用程序”和“保存”。

6. 在“应用程序 ID”框中，复制 GUID。

7. 转到 Visual Studio，打开 *App.xaml.cs* 文件，然后将 `your_client_id_here` 替换为刚注册并复制的应用程序 ID。

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
