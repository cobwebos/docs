---
title: 设置实验室，教授使用 Android Studio 的移动应用程序开发
titleSuffix: Azure Lab Services
description: 了解如何设置实验室来教授使用 Android Studio 的数据移动应用程序开发类。  本文还将讨论在 Azure 中的虚拟机上使用 Android Studio 时要进行的调整。
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849781"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>设置实验室，使用 Android Studio 教授数据移动应用程序开发

本文将介绍如何设置介绍性移动应用程序开发类。  此课程重点介绍可以发布到 [Google Play 商店](https://play.google.com/store/apps)的 Android 移动应用程序。  学生学习如何使用 [Android Studio](https://developer.android.com/studio) 来生成应用程序。  [适用于 Android 的 Visual Studio 模拟器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)用于在本地测试应用程序。

## <a name="lab-configuration"></a>实验室配置

要设置此实验，需要 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅[设置实验室帐户的教程](tutorial-setup-lab-account.md)。  您还可以使用现有的实验室帐户。

按照[设置的课堂实验教程](tutorial-setup-classroom-lab.md)创建新实验室，然后应用以下设置：

| 虚拟机大小 | 图像 |
| -------------------- | ----- |
| 中等（嵌套虚拟化） | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>模板机配置

模板计算机创建完成后，[启动计算机并连接到计算机](how-to-create-manage-template.md#update-a-template-vm)以完成以下任务：

1. 添加超 V 角色
2. 下载并安装 Java。  
3. 下载并安装适用于 Android 的视觉工作室仿真器。
4. 下载并安装安卓工作室。
5. 为 Android 工作室配置可视化工作室仿真器。

## <a name="add-hyper-v-role"></a>添加超 V 角色

为了成功安装适用于 Android 的可视化工作室仿真器，必须启用 Hyper-V。  按照[如何在模板虚拟机文章中启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm.md)的说明进行操作。

## <a name="install-java"></a>安装 Java

安卓工作室需要Java。  按照以下步骤下载最新版本的 Java。

1. 导航到[Java 下载页](https://www.java.com/download/)。 单击**Java 下载**按钮。
2. 在 64 位 Java 网页的 Windows 上，单击标记为"**同意"和"开始免费下载"的**按钮。
3. 当**出现 Java 安装程序**时，单击"**安装**"。
4. 等待安装程序标题更改为 Java**安装程序 = 完成**。  单击"**关闭**"按钮。

## <a name="install-visual-studio-emulator-for-android"></a>安装适用于 Android 的视觉工作室仿真器

要在本地测试 Android 应用程序，它必须使用 Android 设备的虚拟化版本。  有几个Android仿真器，将允许开发人员测试他们的应用程序从他们的机器。  我们使用 Visual Studio 仿真器用于 Android，因为它是支持嵌套虚拟化的仿真器。  由于实验室服务 VM 已经是虚拟机，因此我们需要支持嵌套虚拟化的仿真程序。  Android Studio 的内置仿真程序不支持嵌套虚拟化。  要查看哪些仿真程序支持嵌套虚拟化，请参阅[仿真器性能的硬件加速（Hyper-V & HAXM）。](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)

使用以下说明下载并安装适用于 Android 的可视化工作室仿真器。

1. 导航到 Android 主页[的视觉工作室仿真器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)。
2. 单击 **"下载仿真器"** 按钮。
3. 下载 vs_emulatorsetup.exe 时，请运行可执行文件。
4. 当出现"可视化工作室设置"对话框时，单击"**安装**"按钮。
5. 等待安装程序完成。  单击"**立即重新启动"** 按钮以重新启动计算机并完成安装。

在使用 Android Studio 部署应用程序之前，请先启动仿真程序。  有关 Android 的视觉工作室仿真器的详细信息，请参阅[Android 文档的视觉工作室仿真器](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)。

## <a name="install-android-studio"></a>安装安卓工作室

按照下面的说明下载和安装[Android工作室](https://developer.android.com/studio)。

1. 导航到[Android工作室下载页面](https://developer.android.com/studio#downloads)。  
    > [!NOTE]
    > 此站点不支持 Internet 资源管理器。
2. 单击 Windows （64 位） 可执行的 Android 工作室包。
3. 阅读弹出窗口中写入的法律术语。  准备继续时，请检查**我已阅读并同意上述条款和条件**复选框，然后单击 **"为 Windows 下载 Android 工作室**"按钮。
4. 一旦在 Android Studio 设置上下载可执行文件，请运行可执行文件。
5. 在**Android 工作室安装程序**的 **"欢迎访问 Android 工作室设置"** 页面上，单击 **"下一步**"。
6. 在 **"配置设置"** 页上，单击"**下一步**"。
7. 在 **"选择开始菜单文件夹"** 页上，单击"**安装**"。
8. 等待设置完成。
9. 在 **"安装完成"** 页上，单击"**下一步**"。
10. 在 **"完成 Android 工作室设置**"页面上。  单击“完成”****。
11. Android 工作室将在设置完成后自动启动。
12. 在 **"导入 Android 设置从..."** 对话框中，选择 **"不导入设置**"。 单击“确定”。
13. 在**Android 工作室设置向导****的欢迎**页面上，单击 **"下一步**"。
14. 在 **"安装类型"** 页上，选择 **"标准**"。 单击“下一步”****。
15. 在 **"选择 UI 主题"** 页上，选择所需的主题。 单击“下一步”****。
16. 在"**验证设置"** 页上，单击 **"下一步**"。
17. 在"**下载组件"** 页上，等待下载所有组件。  单击“完成”****。

    > [!IMPORTANT]
    > 预计 HAXM 安装失败。  HAXM 不支持嵌套虚拟化，这就是为什么我们在本文前面为 Android 安装了 Visual Studio 仿真器的原因。

18. 设置向导完成后，将显示"**欢迎使用 Android Studio"** 对话框。

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>为 Android 配置 Android 工作室和可视化工作室仿真器

安卓工作室几乎可以投入使用。  我们仍然需要告诉Visual Studio 仿真器的 Android 安装 Android SDK 的位置。  这将使在 Visual Studio 中运行的任何仿真器都作为 Android 工作室调试的部署目标显示。

我们需要设置一个特定的注册表项，告诉Android Sdk所在的Android视觉工作室仿真器。  要设置所需的注册表项，请参阅下面的脚本。  下面的 PowerShell 脚本假定 Android Sdk 的默认安装位置。  如果您在其他位置安装了 Android Sdk，请在运行脚本之前修改`$androidSdkPath`的值。

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> 重新启动 Android 和 Android 工作室的视觉工作室仿真器，以便使用新设置。

在可视化工作室仿真器中启动所需的版本。  它将在 Android 工作室中作为 Android 应用的部署目标显示。  Android Studio 项目的最低版本必须支持在 Android 的可视化工作室仿真器中运行的版本。  现在，您已准备好使用 Android 工作室和 Visual Studio 仿真器创建和调试项目。  如果您有任何问题，请参阅 Android 仿真器故障排除。

## <a name="cost"></a>成本

如果您想估计本实验的成本，可以按照下面的示例操作。
对于一个25个学生，有20小时的预定上课时间和10小时的家庭作业或作业配额，实验室的价格是  

25\*名学生（20 个计划 + 10 个配额） 小时 = 55 个实验室单位 = 每小时 0.01 USD = 412.5 USD

有关定价的更多详细信息，请参阅[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)
