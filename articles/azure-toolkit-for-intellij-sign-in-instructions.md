---
title: "用于 IntelliJ 的 Azure 工具包的登录说明"
description: "了解如何使用用于 IntelliJ 的 Azure 工具包登录到 Microsoft Azure。"
services: 
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/20/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: def5957b2ed58aee2e03c879ba9fd3d6c714e358
ms.contentlocale: zh-cn
ms.lasthandoff: 09/02/2017

---

# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>用于 IntelliJ 的 Azure 工具包的登录说明

用于 IntelliJ 的 Azure 工具包提供了两种用于登录到 Azure 帐户的方法：

  * **自动**：创建一个可用于自动登录到 Azure 帐户的凭据文件。
  * **交互式**：每次登录到 Azure 帐户时都要输入 Azure 凭据。

以下部分介绍如何使用每种方法。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-automatically"></a>自动登录到 Azure 帐户

本部分逐步引导创建一个包含服务主体数据的凭据文件。 完成此过程后，每次打开项目时，Eclipse 都会使用凭据文件你将自动登录到 Azure。

1. 使用 IntelliJ IDEA 打开项目。

2. 在“工具”菜单中，指向“Azure”，并单击“Azure 登录”。

   ![“IntelliJ Azure 登录”命令][A01]

3. 在“Azure 登录”窗口中选择“自动”，并单击“新建”。

   ![已选择“自动”的“Azure 登录”窗口][A02]

4. 在“Azure 登录”对话框窗口中输入 Azure 凭据，并单击“登录”。

   ![“Azure 登录”对话框窗口][A03]

5. 在“创建身份验证文件”窗口中选择要使用的订阅，选择目标目录，并单击“启动”。

   ![“创建身份验证文件”窗口][A04]

6. 成功创建文件后，请在“服务主体创建状态”对话框中单击“确定”。

   ![“服务主体创建状态”对话框][A05]

7. 在“Azure 登录”窗口中单击“登录”。

   ![“Azure 登录”对话框][A06]

8. 在“选择订阅”对话框中选择要使用的订阅，并单击“确定”。

   ![“选择订阅”对话框][A07]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-automatically"></a>自动登录后从 Azure 帐户注销

使用上述步骤配置帐户后，每次重新启动 IntelliJ IDEA 时，Azure 工具包会你将自动登录到 Azure 帐户。 但是，要注销 Azure 帐户并禁止 Azure 工具包你将自动登录，请执行以下操作：

1. 在 IntelliJ IDEA 的“工具”菜单中指向“Azure”，并单击“Azure 注销”。

   ![“IntelliJ Azure 注销”命令][L01]

2. 在“Azure 注销”确认窗口中，单击“是”。

   ![“Azure 注销”确认窗口][L03]

## <a name="sign-in-to-your-azure-account-automatically-by-using-an-existing-credentials-file"></a>使用现有的凭据文件自动登录到 Azure 帐户

如果使用 IntelliJ IDEA 时从 Azure 帐户注销，必须使用现有的凭据文件才能自动重新登录到该帐户。 要将用于 Eclipse 的 Azure 工具包配置为使用现有的凭据文件，请执行以下操作：

1. 使用 IntelliJ IDEA 打开项目。

2. 在“工具”菜单中，指向“Azure”，并单击“Azure 登录”。

   ![“IntelliJ Azure 登录”命令][A01]

3. 在“Azure 登录”窗口中选择“自动”，并单击“浏览”。

   ![已选择“自动”的“Azure 登录”窗口][A02]

4. 在“选择身份验证文件”对话框中，选择前面创建的凭据文件，并单击“选择”。

   ![“选择身份验证文件”对话框][A08]

5. 在“Azure 登录”窗口中单击“登录”。

   ![已选择“自动”的“Azure 登录”窗口][A06]

6. 在“选择订阅”对话框中选择要使用的订阅，并单击“确定”。

   ![“选择订阅”对话框][A07]

## <a name="sign-in-to-your-azure-account-interactively"></a>以交互方式登录到 Azure 帐户

若要通过手动输入 Azure 凭据登录到 Azure，请执行以下操作：

1. 使用 IntelliJ IDEA 打开项目。

2. 单击“工具”，指向“Azure”，并单击“Azure 登录”。

   ![“IntelliJ Azure 登录”命令][I01]

3. 在“Azure 登录”窗口中选择“交互式”，并单击“登录”。

   ![已选择“交互式”的“Azure 登录”窗口][I02]

4. 在显示的“Azure 登录”对话框中输入 Azure 凭据，并单击“登录”。

   ![“Azure 登录”对话框窗口][I03]

5. 在“选择订阅”对话框中选择要使用的订阅，并单击“确定”。

   ![“选择订阅”对话框][I04]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-interactively"></a>以交互方式登录后从 Azure 帐户注销

使用上述步骤配置帐户后，每次重新启动 IntelliJ IDEA 时，都会自动从 Azure 帐户中注销。 但是，如果要在*不*重新启动 IntelliJ IDEA 的情况下注销 Azure 帐户，请执行以下操作。

1. 在 IntelliJ IDEA 的“工具”菜单中指向“Azure”，并单击“Azure 注销”。

   ![“IntelliJ Azure 注销”命令][L01]

2. 在“Azure 注销”确认窗口中，单击“是”。

   ![“Azure 注销”确认窗口][L02]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World web app for Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Sign-in instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign-in instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[What's new in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's new in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L03.png

