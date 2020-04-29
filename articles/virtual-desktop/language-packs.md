---
title: 在 windows 虚拟桌面中的 Windows 10 虚拟机上安装语言包-Azure
description: 如何在 Windows 虚拟桌面中安装适用于 Windows 10 多会话 Vm 的语言包。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80634030"
---
# <a name="install-language-packs"></a>安装语言包

当你在国际上设置 Windows 虚拟桌面部署时，最好确保你的部署支持多种语言。 你可以在 Windows 10 企业多会话虚拟机（VM）映像上安装语言包，以支持你的组织所需的多种语言。 本文将介绍如何安装语言包并捕获图像，使用户能够选择自己的显示语言。

若要详细了解如何在 Azure 中部署 VM，请参阅[使用 Azure 门户在可用性区域中创建 Windows 虚拟机](../virtual-machines/windows/create-portal-availability-zone.md)。

>[!NOTE]
>本文适用于 Windows 10 企业多会话 Vm。

## <a name="install-a-language-pack"></a>安装语言包

若要创建具有语言包的 VM 映像，首先需要将语言包安装到计算机上，并捕获该计算机的映像。

若要安装语言包：

1. 以管理员身份登录。
2. 请确保已安装所有最新的 Windows 和 Windows 应用商店更新。
3. 请参阅**Settings** > **Time & Language** > **Region**。
4. 在 "**国家或地区**" 下，从下拉菜单中选择首选国家或地区。
    在此示例中，我们将选择 "**华北**"，如以下屏幕截图所示：

    ![区域页的屏幕截图。 当前选择的区域为华北。](media/region-page-france.png)

5. 然后选择 "**语言**"，然后选择 "**添加语言**"。 从列表中选择要安装的语言，然后选择 "**下一步**"。
6. 当 "**安装语言功能**" 窗口打开时，选中标记为 **"安装语言包" 的复选框，并将其设置为 Windows 显示语言**。
7. 选择“安装”  。
8. 若要一次添加多个语言，请选择 "**添加语言**"，然后重复此过程以添加步骤5和6中的语言。 对于要安装的每种语言，请重复此过程。 但是，你一次只能设置一种语言作为显示语言。

    让我们通过快速直观的演示来运行。 以下图像显示了如何安装法语和荷兰语包，然后将法语设置为显示语言。

    ![进程开头的语言页的屏幕截图。 所选 Windows 显示语言为英语。](media/language-page-default.png)

    ![语言选择窗口的屏幕截图。 用户已在搜索栏中输入 "法语" 以查找法语包。](media/select-language-french.png)

    !["安装语言功能" 页的屏幕截图。 选择法语作为首选语言。 选择的选项包括 "设置我的显示语言"、"安装语言包"、"语音识别" 和 "手写"。](media/install-language-features.png)

    安装语言包之后，你应该会看到语言包的名称出现在语言列表中。

    ![安装了新语言包的 "语言" 页的屏幕截图。 法语和荷兰语言包在 "首选语言" 下列出。](media/language-page-complete.png)

9. 如果出现窗口，要求您从会话中注销。 注销，然后再次登录。 您的显示语言现在应为您选择的语言。

10.  请参阅 **"控制面板** > **时钟和区域** > **区域**"。

11.  在 "**区域**" 窗口打开时，选择 "**管理**" 选项卡，然后选择 "**复制设置**"。

12.  选中标有 "**欢迎屏幕" 和 "系统帐户**" 和 "**新用户帐户**" 的复选框。

13.  选择“确定”  。

14.  将打开一个窗口，告诉你重新启动会话。 选择 "**立即重新启动**"。

15.  重新登录后，返回到 **"控制面板** > **时钟和区域** > **Region**"。

16.  选择 "**管理**" 选项卡。

17.  选择 "**更改系统区域设置**"。

18. 在 "**当前系统区域设置**" 下的下拉菜单中，选择要使用的区域设置语言。 完成后，选择 **"确定"**。

19. 选择 "**立即重新启动**" 以重新启动你的会话。

恭喜，你已安装语言包！

继续之前，请确保系统已安装最新版本的 Windows 和 Windows 应用商店。

## <a name="sysprep"></a>Sysprep

接下来，需要对计算机执行 sysprep 操作，以便为映像捕获过程做好准备。

Sysprep 计算机：

1. 以管理员身份打开 PowerShell。
2. 运行以下 cmdlet 以切换到正确的目录：
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. 接下来，运行以下 cmdlet：
    
    ```powershell
    .\sysprep.exe
    ```

4. 当 "系统准备工具" 窗口打开时，选中**标记为 "** **通用化**" 的复选框，然后从下拉菜单中选择 "**关闭**"。

>[!NOTE]
>Syprep 过程需要几分钟才能完成。 VM 关闭时，远程会话将断开连接。

### <a name="resolve-sysprep-errors"></a>解决 sysprep 错误

如果在 sysprep 过程中看到错误消息，则应执行以下操作：

1. 打开**驱动器 C**并前往**Windows** > **System32 Sysprep** > **Panther**，并打开**setuperr.log**文件。

   错误文件中的文本会告诉你需要卸载特定语言包，如下图所示。 复制下一步的语言包名称。

   ![Setuperr.log 文件的屏幕快照。 带有包名称的文本将突出显示为深蓝色。](media/setuperr-package-name.png)

2. 打开新的 PowerShell 窗口，并使用在步骤2中复制的包名称运行以下 cmdlet，以删除语言包：

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. 检查以确保已通过再次运行`Remove-AppxPackage` cmdlet 删除了包。 如果已成功删除了包，则应该会看到一条消息，指出你尝试删除的包不存在。

4. 再次运行`sysprep.exe` cmdlet。

## <a name="capture-the-image"></a>捕获映像

你的系统已准备就绪，你可以捕获映像，以便其他用户可以开始使用基于你的系统的 Vm，而无需重复配置过程。

捕获映像：

1. 中转到 Azure 门户并选择在 "[安装语言包](#install-a-language-pack)和[sysprep](#sysprep)" 中配置的计算机的名称。

2. 选择 "**捕获**"。

3. 在 "**名称**" 字段中输入映像的名称，并使用 "**资源组**" 下拉菜单将其分配给资源组，如下图所示。

   !["创建映像" 窗口的屏幕截图。 用户为此测试映像指定的名称为 "vmwvd"，并且已将其分配给 "testwvdimagerg" 资源组。](media/create-image.png)

4. 选择“创建”。 

5. 等待几分钟让捕获过程完成。 映像准备就绪后，你应该会在 "通知" 中心看到一条消息，告知你已捕获该映像。

你现在可以使用新映像部署 VM。 部署 VM 时，请确保按照[使用 Azure 门户在可用性区域中创建 Windows 虚拟机](../virtual-machines/windows/create-portal-availability-zone.md)中的说明进行操作。

### <a name="how-to-change-display-language-for-standard-users"></a>如何更改标准用户的显示语言

标准用户可以更改其 Vm 上的显示语言。

更改显示语言：

1. 请参阅 "**语言设置**"。 如果你不知道该位置，可以在 "开始" 菜单的搜索栏中输入**语言**。

2. 在 Windows "显示语言" 下拉菜单中，选择要用作显示语言的语言。

3. 从会话注销，然后重新登录。 显示语言现在应为在步骤2中选择的语言。
