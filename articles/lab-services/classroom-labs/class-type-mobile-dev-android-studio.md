---
title: 设置实验室，使用 Android Studio 来讲授移动应用程序开发
titleSuffix: Azure Lab Services
description: 了解如何设置实验室来讲授使用 Android Studio 的数据移动应用程序开发类。  本文还讨论了在 Azure 中的虚拟机上使用 Android Studio 时进行的调整。
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849781"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>设置实验室，使用 Android Studio 来讲授数据移动应用程序的开发

本文将演示如何设置介绍性移动应用程序开发类。  此类侧重于可发布到[Google Play 商店](https://play.google.com/store/apps)的 Android 移动应用程序。  学生了解如何使用[Android Studio](https://developer.android.com/studio)来生成应用程序。  适用于[Android 的 Visual Studio 仿真](https://visualstudio.microsoft.com/vs/msft-android-emulator/)程序用于在本地测试应用程序。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要一个 Azure 订阅和实验室帐户才能开始使用。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户教程](tutorial-setup-lab-account.md)。  你还可以使用现有的实验室帐户。

按照[设置教室实验室教程](tutorial-setup-classroom-lab.md)创建新的实验室，然后应用以下设置：

| 虚拟机大小 | 图像 |
| -------------------- | ----- |
| 中（嵌套虚拟化） | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>模板计算机配置

模板计算机创建完成后，[启动计算机并连接到该](how-to-create-manage-template.md#update-a-template-vm)计算机以完成以下任务：

1. 添加 Hyper-v 角色
2. 下载并安装 Java。  
3. 下载并安装适用于 Android 的 Visual Studio 仿真程序。
4. 下载并安装 Android Studio。
5. 为 Android Studio 配置 Visual Studio 模拟器。

## <a name="add-hyper-v-role"></a>添加 Hyper-v 角色

若要成功安装适用于 Android 的 Visual Studio 仿真程序，必须启用 hyper-v。  按照[如何在模板虚拟机中启用嵌套虚拟化一](how-to-enable-nested-virtualization-template-vm.md)文中的说明进行操作。

## <a name="install-java"></a>安装 Java

Android Studio 需要 Java。  按照以下步骤下载最新版本的 Java。

1. 导航到[Java 下载页](https://www.java.com/download/)。 单击 " **Java 下载**" 按钮。
2. 在 "适用于 Java 的64位 Windows" 网页上，单击标签为 "**同意并开始免费下载**" 的按钮。
3. 当 " **Java 安装**程序安装程序" 出现时，单击 "**安装**"。
4. 等待安装程序标题更改为**Java 设置-完成**。  单击 "**关闭**" 按钮。

## <a name="install-visual-studio-emulator-for-android"></a>安装适用于 Android 的 Visual Studio 仿真程序

若要在本地测试 Android 应用程序，该应用程序必须使用虚拟化版本的 Android 设备。  有一些 Android 模拟器可用，开发人员可从其计算机测试其应用程序。  我们使用的是适用于 Android 的 Visual Studio 仿真程序，因为它是支持嵌套虚拟化的模拟器。  由于实验室服务 VM 已是一个虚拟机，因此我们需要支持嵌套虚拟化的模拟器。  用于 Android Studio 的内置模拟器不支持嵌套虚拟化。  若要查看哪些模拟器支持嵌套虚拟化，请参阅[仿真程序性能的硬件加速（hyper-v & HAXM）](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)。

使用以下说明下载和安装适用于 Android 的 Visual Studio 仿真程序。

1. 导航到[适用于 Android 的 Visual Studio 仿真程序](https://visualstudio.microsoft.com/vs/msft-android-emulator/)主页。
2. 单击 "**下载模拟器"** 按钮。
3. 下载 vs_emulatorsetup 时，运行可执行文件。
4. 出现 Visual Studio 安装程序对话框时，单击 "**安装**" 按钮。
5. 等待安装程序完成。  单击 "**立即重新启动**" 按钮以重新启动计算机并完成安装。

使用 Android Studio 部署应用程序之前，请先启动模拟器。  有关适用于 Android 的 Visual Studio 仿真程序的详细信息，请参阅[适用于 android 的 Visual Studio 模拟器文档](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)。

## <a name="install-android-studio"></a>安装 Android Studio

按照以下说明下载并安装[Android Studio](https://developer.android.com/studio)。

1. 导航到[Android Studio 下载 "页](https://developer.android.com/studio#downloads)。  
    > [!NOTE]
    > 此站点不支持 Internet Explorer。
2. 单击 "Windows （64位）可执行文件 Android Studio 包"。
3. 阅读在弹出窗口中编写的法律条款。  准备好继续时，请选中 "**我已阅读并同意上述条款和条件**" 复选框，然后单击 "**下载 Windows Android Studio** " 按钮。
4. 在下载 Android Studio 安装程序可执行文件后，运行该可执行文件。
5. 在**Android Studio 安装**程序安装程序的 "**欢迎使用 Android Studio 安装程序**" 页上，单击 "**下一步**"。
6. 在 "**配置设置**" 页上，单击 "**下一步**"。
7. 在 "**选择开始菜单文件夹**" 页上，单击 "**安装**"。
8. 等待安装完成。
9. 在 "**安装完成**" 页上，单击 "**下一步**"。
10. 在 "**完成 Android Studio 安装**" 页上。  单击“完成”。
11. 安装完成后，将自动启动 Android Studio。
12. 在 "**从 ... 中导入 Android 设置**" 对话框中，选择 "不**导入设置**"。 单击“确定”。
13. 在**Android Studio 安装向导**"的"**欢迎**"页上，单击"**下一步**"。
14. 在 "**安装类型**" 页上，选择 "**标准**"。 单击“下一步”。
15. 在 "**选择 UI 主题**" 页上，选择 "所需主题"。 单击“下一步”。
16. 在 "**验证设置**" 页上，单击 "**下一步**"。
17. 在 "**下载组件**" 页上，等待下载所有组件。  单击“完成”。

    > [!IMPORTANT]
    > 预期 HAXM 安装失败。  HAXM 不支持嵌套虚拟化，这就是我们在本文前面部分安装了适用于 Android 的 Visual Studio 仿真程序的原因。

18. 安装向导完成后，将显示 "**欢迎使用 Android Studio** " 对话框。

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>为 Android 配置 Android Studio 和 Visual Studio 仿真器

Android Studio 几乎可以使用。  我们仍需要告诉适用于 Android 的 Visual Studio 模拟器，其中安装了 Android SDK。  这会使 Visual Studio for Android 中运行的任何仿真程序显示为 Android Studio 调试的部署目标。

需要设置特定的注册表项，以便告诉适用于 android 的 Visual Studio 仿真程序（Android Sdk 位于该位置）。  若要设置所需的注册表项，请运行以下脚本。  下面的 PowerShell 脚本假定 Android Sdk 的默认安装位置。  如果你的 Android Sdk 安装在其他位置，请在运行该脚本之前修改 `$androidSdkPath` 的值。

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> 重启适用于 Android 的 Visual Studio 仿真程序和 Android Studio，以便使用新设置。

在 Visual Studio 模拟器中启动所需的版本。  在 Android studio 中，它将显示为 Android 应用程序的部署目标。  Android Studio 项目的最低版本必须支持在适用于 Android 的 Visual Studio 模拟器中运行的版本。  现在，你已准备好使用 Android Studio 和适用于 Android 的 Visual Studio 仿真程序创建和调试项目。  如果有任何问题，请参阅 Android 模拟器故障排除。

## <a name="cost"></a>费用

如果你想要估计此实验室的成本，你可以按照以下示例操作。
对于具有20小时的计划类时间和10小时配额（适用于家庭或分配）的25名学生，实验室的价格  

25名学生 \* （20个计划 + 10 配额）小时 * 55 实验室单位 * 0.01 美元/小时 = 412.5 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
