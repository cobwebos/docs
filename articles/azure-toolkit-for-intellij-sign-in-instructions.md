---
title: "用于 IntelliJ 的 Azure 工具包的登录说明 | Microsoft Docs"
description: "了解如何使用用于 IntelliJ 的 Azure 工具包登录到 Microsoft Azure。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d1be0432a54ed300bc135345b660b90f3efb3b39
ms.lasthandoff: 04/22/2017


---

# <a name="azure-sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>用于 IntelliJ 的 Azure 工具包的 Azure 登录说明

用于 IntelliJ 的 Azure 工具包提供了两种用于登录到 Azure 帐户的方法：

  * **交互式** - 如果使用此方法，每次登录 Azure 帐户时，都需要输入 Azure 凭据。
  * **自动** - 如果使用此方法，你将创建一个包含服务主体数据的凭据文件，之后可以使用该凭据文件自动登录到 Azure 帐户。

以下部分中的步骤将介绍如何使用每个方法。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="signing-into-your-azure-account-interactively"></a>以交互方式登录到 Azure 帐户

以下步骤将说明如何通过手动输入 Azure 凭据登录到 Azure。

1. 使用 IntelliJ IDEA 打开项目。

1. 依次单击“工具”、“Azure”，然后单击“Azure 登录”。

   ![用于 Azure 登录的 IntelliJ 菜单][I01]

1. 显示“Azure 登录”对话框时，选择“交互式”，然后单击“登录”。

   ![“登录”对话框][I02]

1. 显示“Azure 登录”对话框时，输入 Azure 凭据，然后单击“登录”。

   ![“Azure 登录”对话框][I03]

1. 显示“选择订阅”对话框时，选择要使用的订阅，然后单击“确定”。

   ![“选择订阅”对话框][I04]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-interactively"></a>已以交互方式登录时，从 Azure 帐户中注销

按照上一部分中的步骤配置后，每次重新启动 IntelliJ IDEA 时，都将自动从 Azure 帐户中注销。 但是，如果要在不重新启动 IntelliJ IDEA 的情况下注销 Azure 帐户，请使用以下步骤。

1. 在 IntelliJ IDEA 中，依次单击“工具”、“Azure”，然后单击“Azure 注销”。

   ![用于 Azure 注销的 IntelliJ 菜单][L01]

1. 显示“Azure 注销”对话框时，单击“是”。

   ![“注销”对话框][L02]

## <a name="signing-into-your-azure-account-automatically-and-creating-a-credentials-file-to-use-in-the-future"></a>自动登录到 Azure 帐户并创建凭据文件，以便在将来使用

以下步骤将引导你完成创建一个包含服务主体数据的凭据文件。 完成这些步骤后，你每次打开项目时，Eclipse 都会自动使用凭据文件将你自动登录到 Azure。

1. 使用 IntelliJ IDEA 打开项目。

1. 依次单击“工具”、“Azure”，然后单击“Azure 登录”。

   ![用于 Azure 登录的 IntelliJ 菜单][A01]

1. 显示“Azure 登录”对话框时，选择“自动”，然后单击“新建”。

   ![“登录”对话框][A02]

1. 显示“Azure 登录”对话框时，输入 Azure 凭据，然后单击“登录”。

   ![“Azure 登录”对话框][A03]

1. 显示“创建身份验证文件”对话框时，选择要使用的订阅，选择目标目录，然后单击“启动”。

   ![“Azure 登录”对话框][A04]

1. 此时将显示“服务主体创建状态”对话框，成功创建文件后，单击“确定”。

   ![“服务主体创建状态”对话框][A05]

1. 显示“Azure 登录”对话框时，单击“登录”。

   ![“Azure 登录”对话框][A06]

1. 显示“选择订阅”对话框时，选择要使用的订阅，然后单击“确定”。

   ![“选择订阅”对话框][A07]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-automatically"></a>已自动登录时，从 Azure 帐户中注销

按照上一部分中的步骤配置后，每次重新启动 IntelliJ IDEA 时，Azure 工具包都会将你自动登录到 Azure 帐户。 但是，若要注销 Azure 帐户并禁止 Azure 工具包将你自动登录，请使用以下步骤。

1. 在 IntelliJ IDEA 中，依次单击“工具”、“Azure”，然后单击“Azure 注销”。

   ![用于 Azure 注销的 IntelliJ 菜单][L01]

1. 显示“Azure 注销”对话框时，单击“是”。

   ![“注销”对话框][L03]

## <a name="signing-into-your-azure-account-automatically-using-a-credentials-file-which-you-have-already-created"></a>使用已创建的凭据文件自动登录到 Azure 帐户

如果在使用 IntelliJ IDEA 时从 Azure 中注销，则需要重新配置用于 Eclipse 的 Azure 工具包以使用已创建的凭据文件，然后才能自动登录到 Azure 帐户。 以下步骤将引导你完成配置 Azure 工具包以使用现有凭据文件。

1. 使用 IntelliJ IDEA 打开项目。

1. 依次单击“工具”、“Azure”，然后单击“Azure 登录”。

   ![用于 Azure 登录的 IntelliJ 菜单][A01]

1. 显示“Azure 登录”对话框时，选择“自动”，然后单击“浏览”。

   ![“登录”对话框][A02]

1. 显示“选择身份验证文件”对话框时，选择先前创建的凭据文件，然后单击“选择”。

   ![“登录”对话框][A08]

1. 显示“Azure 登录”对话框时，单击“登录”。

   ![“Azure 登录”对话框][A06]

1. 显示“选择订阅”对话框时，选择要使用的订阅，然后单击“确定”。

   ![“选择订阅”对话框][A07]

## <a name="see-also"></a>另请参阅
有关 Azure Toolkits for Java IDE 的详细信息，请参阅以下链接：

* [用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的新增功能]
  * [安装用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的登录说明]
  * [在 Eclipse 中创建 Azure 的 Hello World Web 应用]
* [用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的新增功能]
  * [安装用于 IntelliJ 的 Azure 工具包]
  * *用于 IntelliJ 的 Azure 工具包的登录说明（本文）*
  * [在 IntelliJ 中创建 Azure 的 Hello World Web 应用]

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

<!-- URL List -->

[用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse.md
[用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij.md
[在 Eclipse 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安装用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse-installation.md
[安装用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij-installation.md
[用于 Eclipse 的 Azure 工具包的登录说明]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign In Instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[用于 Eclipse 的 Azure 工具包的新增功能]: ./azure-toolkit-for-eclipse-whats-new.md
[用于 IntelliJ 的 Azure 工具包的新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/

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

