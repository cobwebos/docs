---
title: "如何将 Office 365 订阅与 Azure RemoteApp 一起使用 | Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中使用 Office 365 订阅以共享 Office 应用。"
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: f82b6e23-2b71-47be-846d-fd93f2652f3c
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c92eec471cf080a651d6f12e9db9fa391beabd2a
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>如何将 Office 365 订阅与 Azure RemoteApp 一起使用
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

你知道吗？在 Azure RemoteApp 中可以使用你的现有 Office 365 订阅从云中共享 Office 应用？ 继续阅读有关 Office 365 + Azure RemoteApp 选项的信息，包括可以帮助你充分利用订阅的 Office 365 相关文章的链接。

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>如何将 Office 365 帐户用于 Azure RemoteApp？
请查看 Peter 的新文章了解所有信息：[如何通过 Office 365 的用户帐户使用 Azure RemoteApp](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>能否使用我的 Office 365 订阅在 Azure RemoteApp 中运行 Office 应用程序？
能！ 事实上，使用你的 Office 365 订阅是将 Office 应用程序引入到 Azure RemoteApp 中的唯一方法。

（注意：如果你的 Azure RemoteApp 部署是由托管合作伙伴提供的，他们或许能够提供给你基于[服务提供商许可协议](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx)的 Office 许可证）

Office 365 订阅的最大优点是它允许你在许多不同的平台和环境（包括 Azure 云）中使用相同的用户许可证。 在 Azure RemoteApp 中使用 Office 应用程序时，你不需要购买额外的许可证或以任何特殊方式配置现有的许可证。 你只需要一个包括 [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx) 的 Office 365 订阅。

Office 365 ProPlus 启用[共享计算机激活](https://technet.microsoft.com/library/Dn782860.aspx) - 此功能在虚拟环境和云环境（如 Azure RemoteApp）以及远程桌面服务中为 Office 启用基于临时用户的激活。

哪些 Office 365 计划包括 Office 365 ProPlus？ 请查看[每个计划中的服务可用性](https://technet.microsoft.com/library/office-365-plan-options.aspx)表。 请注意，并非所有计划都包括 Office 365 ProPlus（例如，Office 365 商业版计划）。 如果你的计划不包括 Office 365 ProPlus，请考虑升级到包括 Office 365 ProPlus 的计划（例如，Office 365 教育版 E3）。

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>那么，我的 Office 365 ProPlus 许可证如何与 Azure RemoteApp 一起使用？
Office 365 ProPlus 的每个用户许可证都允许单个用户在多达 5 台计算机及平板电脑和手机上激活 Office 应用程序。 每次激活与用户一同注册，直到他们在设备上停用 Office。 （用户可以在 [Office 365 门户](https://portal.office365.com/)中管理其设备。）

借助 Azure RemoteApp，单个用户可能无意中在同一天登录到多台计算机。 这是因为服务会自动管理和扩展云中的资源，而用户看到的只是你已共享的应用和程序。 对于此方案，Office 365 ProPlus 提供了共享计算机激活模式 - 这意味着用户不需要执行任何许可证管理即可访问这些资源，而且各台计算机不计入 5 台计算机激活限制。

只要你（管理员）将 Office 365 ProPlus 许可证分配给你的用户，他们就可以在其个人设备上以及通过你的 Azure RemoteApp 集合使用 Office。

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>可以将哪些 Office 应用程序与 Office 365 和 Azure RemoteApp 结合使用？
你可以使用你的 Office 365 订阅在 Azure RemoteApp 部署中激活和共享 Office 2013。 我们当前不支持其他版本的 Office 与 Azure RemoteApp 结合使用。 这包括 Office 2003、Office 2007、Office 2010 和 Office 2016。

## <a name="what-about-visio-pro-or-project-pro"></a>Visio Pro 或 Project Pro 呢？
你的 RemoteApp 订阅附带的 Office 365 ProPlus 映像包括 Visio Pro 和 Project Pro。 但是，你不能使用 Office 365 ProPlus 订阅激活这些程序 - 它们有各自的许可证。 你可以在 [Office 365 门户](https://portal.office365.com/)中激活它们。 

如果不想使用这些程序，则不需要授予它们许可证。 只需激活你想要使用的程序并跳过其他程序。 你仍然可以在映像中看到它们，但是不能使用。 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>我如何开始使用 Office 365 和 Azure RemoteApp？
既然你已经知道了 Office 365 许可的详细信息，就让我们帮助你在 Azure RemoteApp 中开始使用它吧 - 非常简单：

创建你的 Azure RemoteApp 集合时，请使用 **Office 365 ProPlus（需订阅）**映像。

![带有 Office 365 Pro Plus 的 Azure RemoteApp 映像](./media/remoteapp-officesubscription/remoteapp-officeimage.png)

此映像包含 Windows Server 和 Office 365 ProPlus 的最新版本。 配置你的集合（包括发布应用）后，你的用户只需（通过使用其 RemoteApp 客户端）登录到 Azure RemoteApp 并为任何 Office 应用提供其 Office 365 凭据。 许可证都是自动提供的，无需任何设置或管理。

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>能否使用 Office 365 ProPlus 创建自定义映像？
你可以为包含 Office 365 ProPlus 的集合创建自定义映像。 有 2 个选项 - 使用我们提供的 Azure 库映像，或者你可以创建自己的自定义映像并安装 Office 365 ProPlus。

### <a name="use-the-azure-gallery-image"></a>使用 Azure 库映像
将 Office 365 ProPlus 部署到集合中的最简单方法是[从 Azure 库映像之一入手](remoteapp-image-on-azurevm.md)（这些映像包括在你的 Azure RemoteApp 订阅中）。 请确保你选择**预安装了 Office 365 ProPlus 的 Windows Server 远程桌面会话主机**映像。 然后，在该映像上安装所需的任何其他应用，而且你已准备就绪。

### <a name="use-a-custom-image"></a>使用自定义映像
你始终可以创建自定义映像 - 你可以创建 [Azure VM](remoteapp-image-on-azurevm.md) 或[创建本地映像](remoteapp-create-custom-image.md)并将其上载到 Azure。 在任一情况下，请确保你使用共享计算机激活节点安装 Office 365 ProPlus。 使用 [Office 部署工具](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx)，然后按照[说明](https://technet.microsoft.com/library/Dn782858.aspx)进行安装。  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>在自定义映像中禁用 Office 365 ProPlus 的自动更新 - 重要
自定义映像由 Azure RemoteApp 用作模板，用于随着用户的不断增加添加更多资源。 要阻止延迟和连接问题，请在映像中禁用 Office 的自动更新。 如果不这样做，则使用该模板创建的每个资源都将在启动时自动更新。 相反，使用标准的 Azure RemoteApp 进程更新自定义映像。 这样一来，你在模板映像上更新一次 Office 应用程序，然后让 Azure RemoteApp 负责将更新提供给你的用户。

若要禁用自动更新，请将以下内容添加到 Office 部署工具的配置文件中：

        <Updates Enabled="FALSE" />

因此，现在你的配置文件应包含以下行：

        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>那么，如何使用 Office 365 ProPlus 更新映像？
有很多原因要在你的集合中更新映像。 下面只是其中的几种情况：

* 获取最新的 Windows 更新 
* 获取最新的 Office 365 ProPlus 应用程序更新
* 更新自定义应用
* 更改映像自身的其他配置设置

有关更新你的集合以使用更新的映像的端到端步骤，请转到[此处](remoteapp-update.md)。 但是，有关如何更新映像和 Office 365 ProPlus 的信息，请查看以下信息。

你有两个选项可用于更新你的映像 - 使用一个全新的映像替换你的映像或手动更新现有映像。

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>使用最新的 Azure 库映像替换你的映像 + 添加自定义项
使用此选项，你可以让 Microsoft 负责 Windows Server 和 Office 365 ProPlus 更新。 你将基于最新的库映像创建全新的映像，而不是更新现有映像。 然后，重复前面所执行的所有步骤自定义映像 - 安装自定义应用、修改映像配置等。

库映像会定期更新，因此你可以高枕无忧了，因为你知道 Windows Server 和 Office 365 ProPlus 应用是最新版本。 当然，交换条件是你在每次获得新映像时都需要应用你的自定义项。 你可以创建脚本来自动设置你的自定义项。

### <a name="manually-update-your-existing-image"></a>手动更新现有映像
使用此选项，你可以使用标准的 Windows 工具将更新应用于映像。 对于 Office 365 ProPlus，使用 Office 部署工具下载并安装 Office 365 ProPlus 的最新更新或版本。

> [!IMPORTANT]
> 请记住 - 禁用 Office 365 ProPlus 自动更新。
> 
> 

需要有关使用 Office 部署工具进行更新的更多信息？

* [使用 Office 部署工具部署 Office 365 即点即用产品](https://technet.microsoft.com/library/JJ219423.aspx)
* [使用 Office 部署工具部署和更新 Office 365 ProPlus](https://channel9.msdn.com/Events/Ignite/2015/BRK3168)（视频）
* [为 Office 365 ProPlus 配置更新设置](https://technet.microsoft.com/library/dn761708.aspx)


