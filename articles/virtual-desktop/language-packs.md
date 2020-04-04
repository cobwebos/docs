---
title: 在 Windows 虚拟桌面中的 Windows 10 VM 上安装语言包 - Azure
description: 如何在 Windows 虚拟桌面中为 Windows 10 多会话 VM 安装语言包。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634030"
---
# <a name="install-language-packs"></a>安装语言包

在国际上设置 Windows 虚拟桌面部署时，最好确保部署支持多种语言。 您可以在 Windows 10 企业多会话虚拟机 （VM） 映像上安装语言包，以支持组织所需的尽可能多的语言。 本文将告诉您如何安装语言包和捕获允许用户选择自己的显示语言的图像。

详细了解如何在 Azure 中使用[Azure 门户在可用性区域创建 Windows 虚拟机时](../virtual-machines/windows/create-portal-availability-zone.md)在 Azure 中部署 VM。

>[!NOTE]
>本文适用于 Windows 10 企业多会话 VM。

## <a name="install-a-language-pack"></a>安装语言包

要使用语言包创建 VM 映像，首先需要将语言包安装到计算机上并捕获其映像。

要安装语言包：

1. 以管理员身份登录。
2. 确保已安装所有最新的 Windows 和 Windows 应用商店更新。
3. 转到**设置** > **时间&语言** > **区域**。
4. 在 **"国家或地区**"下，从下拉菜单中选择您首选的国家或地区。
    在此示例中，我们将选择**法国**，如以下屏幕截图所示：

    ![区域页面的屏幕截图。 当前选定的区域是法国。](media/region-page-france.png)

5. 之后，选择 **"语言**"，然后选择 **"添加语言**"。 从列表中选择要安装的语言，然后选择 **"下一步**"。
6. 打开 **"安装语言功能"** 窗口时，选择标记为 **"安装语言包"的复选框，并将其设置为"Windows 显示语言**"。
7. 选择“安装”  。
8. 要同时添加多种语言，请选择 **"添加语言**"，然后重复此过程以在步骤 5 和 6 中添加语言。 对要安装的每种语言重复此过程。 但是，您一次只能将一种语言设置为显示语言。

    让我们通过快速的可视化演示来运行。 下图演示如何安装法语和荷兰语包，然后将法语设置为显示语言。

    ![过程开头的语言页面的屏幕截图。 所选的 Windows 显示语言为英语。](media/language-page-default.png)

    ![语言选择窗口的屏幕截图。 用户已在搜索栏中输入"法语"以查找法语包。](media/select-language-french.png)

    !["安装语言功能"页的屏幕截图。 法语被选为首选语言。 所选选项包括"设置我的显示语言"、"安装语言包"、"语音识别"和"手写"。](media/install-language-features.png)

    安装语言包后，您应该会看到语言包的名称显示在语言列表中。

    ![安装了新语言包的语言页面的屏幕截图。 法语和荷兰语言包列在"首选语言"下。](media/language-page-complete.png)

9. 如果出现一个窗口，要求您注销会话。 注销，然后再次登录。 您的显示语言现在应该是您选择的语言。

10.  转到**控制面板** > **时钟和地区** > **。**

11.  打开 **"区域"** 窗口时，选择 **"管理**"选项卡，然后选择 **"复制"设置**。

12.  选择标记为 **"欢迎屏幕"和"欢迎"屏幕和系统帐户**以及 **"新用户帐户"的**复选框。

13.  选择“确定”  。

14.  将打开一个窗口，并告诉您重新启动会话。 选择**立即重新启动**。

15.  重新登录后，返回**控制面板** > **时钟和地区** > **区域**。

16.  选择 **"管理"** 选项卡。

17.  选择 **"更改系统区域设置**"。

18. 在 **"当前系统区域设置**"下的下拉菜单中，选择要使用区域设置的语言。 之后，选择 **"确定**"。

19. 选择 **"立即重新启动**"以再次重新启动会话。

恭喜您，您已安装语言包！

在继续之前，请确保系统安装了最新版本的 Windows 和 Windows 应用商店。

## <a name="sysprep"></a>Sysprep

接下来，您需要对机器进行系统准备，以便为图像捕获过程做好准备。

要对机器进行系统准备：

1. 以管理员身份打开 PowerShell。
2. 运行以下 cmdlet 以转到正确的目录：
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. 接下来，运行以下 cmdlet：
    
    ```powershell
    .\sysprep.exe
    ```

4. 打开"系统准备工具"窗口时，选择标记为 **"通用"** 的复选框，然后转到 **"关机选项**"，然后从下拉菜单**中选择"关闭**"。

>[!NOTE]
>syprep 过程需要几分钟才能完成。 当 VM 关闭时，远程会话将断开连接。

### <a name="resolve-sysprep-errors"></a>解决系统准备错误

如果您在 sysprep 过程中看到错误消息，您应该执行以下操作：

1. 打开**驱动器 C**并转到**Windows** > **System32 Sysprep** > **黑豹**，然后打开**设置文件**。

   错误文件中的文本将告诉您需要卸载特定的语言包，如下图所示。 复制下一步的语言包名称。

   ![设置文件截图。 带有包名称的文本以深蓝色突出显示。](media/setuperr-package-name.png)

2. 打开新的 PowerShell 窗口，使用步骤 2 中复制的包名称运行以下 cmdlet 以删除语言包：

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. 检查以确保您再次运行`Remove-AppxPackage`cmdlet 从而删除了包。 如果已成功删除包，则应看到一条消息，指出您尝试删除的包不存在。

4. 再次运行`sysprep.exe`cmdlet。

## <a name="capture-the-image"></a>捕获映像

现在，您的系统已准备就绪，您可以捕获映像，以便其他用户可以基于您的系统开始使用 VM，而无需重复配置过程。

要捕获图像：

1. 转到 Azure 门户，然后选择在[安装语言包](#install-a-language-pack)和[sysprep](#sysprep)中配置的计算机的名称。

2. 选择**捕获**。

3. 在 **"名称"** 字段中输入图像的名称，并使用 **"资源组**下拉菜单"将其分配给资源组，如下图所示。

   ![创建图像窗口的屏幕截图。 用户给此测试映像的名称为"vmwvd-image-fr"，并且已将其分配给"testwvdimagerg"资源组。](media/create-image.png)

4. 选择“创建”  。

5. 等待几分钟，等待捕获过程完成。 当映像准备就绪时，您应该在通知中心看到一条消息，通知您图像已捕获。

现在可以使用新映像部署 VM。 部署 VM 时，请确保[使用 Azure 门户 在可用性区域中执行](../virtual-machines/windows/create-portal-availability-zone.md)创建 Windows 虚拟机中的说明。

### <a name="how-to-change-display-language-for-standard-users"></a>如何更改标准用户的显示语言

标准用户可以更改其 VM 上的显示语言。

要更改显示语言：

1. 转到**语言设置**。 如果您不知道它在哪里，则可以在"开始"菜单的搜索栏中输入**语言**。

2. 在 Windows 显示语言下拉菜单中，选择要用作显示语言的语言。

3. 注销会话，然后重新登录。 显示语言现在应该是您在步骤 2 中选择的语言。
