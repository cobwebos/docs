---
title: 设置实例和身份验证（已编写脚本）
titleSuffix: Azure Digital Twins
description: 请参阅如何通过运行自动部署脚本来设置 Azure 数字孪生服务的实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4e8e26f73aef155e4194ca7a4178827b6a7366fb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047414"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>设置 Azure 数字孪生实例和身份验证 (脚本) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍 **设置新的 Azure 数字孪生实例**的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本通过运行可简化过程的 [**自动化部署脚本** 示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 来完成这些步骤。 
* 若要查看脚本在幕后运行的手动 CLI 步骤，请参阅本文的 CLI 版本： how [*to： Set a instance and authentication (CLI) *](how-to-set-up-instance-cli.md)。
* 若要根据 Azure 门户查看手动步骤，请参阅本文的门户版本： [*如何：) 门户 (设置实例和身份验证 *](how-to-set-up-instance-portal.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="prerequisites-download-the-script"></a>必备组件：下载脚本

示例脚本用 PowerShell 编写。 这是 [**Azure 数字孪生示例**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)的一部分，可通过导航到此示例链接并选择标题下面的 " *下载 ZIP* " 按钮，将其下载到计算机。

这会将示例项目下载到你的计算机上 _**Azure_Digital_Twins_samples.zip**_。 导航到计算机上的文件夹并将其解压缩以提取文件。

在解压缩文件夹中，部署脚本位于_Azure_Digital_Twins_samples > 脚本 > **deploy.ps1** _。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>运行部署脚本

本文使用 Azure 数字孪生代码示例来完全自动部署 Azure 数字孪生实例和所需的身份验证。 它还可用作编写您自己的脚本交互的起点。

下面是在 Cloud Shell 中运行部署脚本的步骤。
1. 在浏览器中转到 [Azure Cloud Shell](https://shell.azure.com/) 窗口。 使用以下命令登录：
    ```azurecli
    az login
    ```
    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。 否则，请在浏览器中打开 *https://aka.ms/devicelogin*，然后输入终端中显示的授权代码。
 
2. 在 Cloud Shell 图标栏中，确保 Cloud Shell 设置为运行 PowerShell 版本。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="显示 PowerShell 版本选择的 Cloud Shell 窗口&quot;:::

1. 选择 &quot;上传/下载文件" 图标，然后选择 "上传"。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="显示 PowerShell 版本选择的 Cloud Shell 窗口&quot;:::

1. 选择 &quot;上传/下载文件" 窗口中发送命令来运行该脚本。  (回忆要粘贴到 Cloud Shell 中，可以在 Windows 和 Linux 上使用 **Ctrl + shift + v** ，或在 macOS 上使用 **Cmd + shift + v** 。 你还可以使用右键单击菜单。 ) 

    ```azurecli
    ./deploy.ps1
    ```

    当脚本通过自动安装步骤运行时，系统会要求您传递以下值：
    * 对于实例：要使用的 Azure 订阅的*订阅 ID*
    * 对于实例：要在其中部署实例的 *位置* 。 若要查看哪些区域支持 Azure 数字孪生，请访问 [*按区域提供的 azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
    * 对于实例： *资源组* 名称。 你可以使用现有资源组，或者输入一个新名称来创建。
    * 对于实例： Azure 数字孪生实例的 *名称* 。 新实例的名称在你的订阅的区域内必须是唯一的 (这意味着，如果你的订阅在已使用所选名称的区域中有另一个 Azure 数字孪生实例，则系统将要求你选择不同的名称) 。
    * 对于应用注册：要与注册关联的 *Azure AD 应用程序显示名称* 。 通过此应用注册，你可以配置对 [Azure 数字孪生 api](how-to-use-apis-sdks.md)的访问权限。 以后，客户端应用程序将对应用程序注册进行身份验证，因此，会将配置的访问权限授予 Api。
    * 对于应用注册： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 请使用 `http://localhost`。 此脚本将为其设置 *公共客户端/本机 (移动 & 桌面) * URI。

此脚本将创建一个 Azure 数字孪生实例，为你的 Azure 用户分配 azure *数字孪生所有者 (预览 * 该实例上的) 角色，并设置 Azure AD 应用注册，以便客户端应用程序使用。

>[!NOTE]
>目前有一个 **已知** 的脚本化安装问题，其中某些用户 (特别是个人 Microsoft 帐户上的用户 [ (msa) ](https://account.microsoft.com/account)) 可能会发现 **未创建 _Azure 数字孪生所有者 (预览版) _ 的角色分配**。
>
>你可以在本文后面的 " [*验证用户角色分配*](#verify-user-role-assignment) " 部分中验证角色分配，并且如果需要，还可以使用 [Azure 门户](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)手动设置角色分配。
>
>有关此问题的更多详细信息，请参阅 [*故障排除： Azure 数字孪生中的已知问题*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)。

下面是该脚本的输出日志摘录：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="显示 PowerShell 版本选择的 Cloud Shell 窗口&quot;:::

1. 选择 &quot;上传/下载文件" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

如果脚本成功完成，最终打印输出将显示 `Deployment completed successfully` 。 否则，请解决错误消息，并重新运行该脚本。 它将跳过已完成的步骤，并在你离开的点再次开始请求输入。

脚本完成后，现在可以使用 Azure 数字孪生实例来管理它，并为客户端应用程序设置了权限来访问它。

> [!NOTE]
> 脚本当前将 Azure 数字 (孪生中所需的管理角色分配给 azure 数字 *孪生所有者 (预览版) *) 到从 Cloud Shell 运行脚本的同一用户。 如果需要将此角色分配给将管理实例的其他人，可以通过 Azure 门户 ([指令](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 或 CLI ([说明](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) 来完成此操作。

## <a name="collect-important-values"></a>收集重要值

当你继续使用 Azure 数字孪生实例时，可能需要由脚本设置的资源中的几个重要值。 在本部分中，你将使用 [Azure 门户](https://portal.azure.com) 来收集这些值。 应将它们保存在安全的位置，或返回此部分，稍后在需要时进行查找。

如果其他用户将对该实例进行编程，还应与这些用户共享这些值。

### <a name="collect-instance-values"></a>收集实例值

在 [Azure 门户](https://portal.azure.com)中，通过在门户搜索栏中搜索实例的名称来查找 Azure 数字孪生实例。

选择它将打开实例的 " *概述* " 页。 注意其 *名称*、 *资源组*和 *主机名*。 稍后可能需要用到它们来标识并连接到实例。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="显示 PowerShell 版本选择的 Cloud Shell 窗口&quot;:::

1. 选择 &quot;上传/下载文件":::

### <a name="collect-app-registration-values"></a>收集应用注册值 

应用注册中有两个重要值，稍后需要用来 [编写 Azure 数字孪生 api 的客户端应用身份验证代码](how-to-authenticate-client.md)。 

若要查找它们，请单击下面的 [链接](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 以导航到 Azure 门户中的 Azure AD 应用注册概述页。 此页显示已在你的订阅中创建的所有应用程序注册。

你应该会看到刚才在此列表中创建的应用注册。 选择它以打开其详细信息：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="显示 PowerShell 版本选择的 Cloud Shell 窗口&quot;:::

1. 选择 &quot;上传/下载文件":::

记下**在页面上显示的***应用程序 (客户端) id*和*目录 (租户) id* 。 如果您不是将为客户端应用程序编写代码的人员，则需要与将要进行共享的人员共享这些值。

## <a name="verify-success"></a>验证是否成功

如果要验证由脚本设置的资源和权限，可以在 [Azure 门户](https://portal.azure.com)中查看这些资源和权限。

如果无法验证任何步骤是否成功，请重试此步骤。 您可以使用 [Azure 门户](how-to-set-up-instance-portal.md) 或 [CLI](how-to-set-up-instance-cli.md) 说明单独执行这些步骤。

### <a name="verify-instance"></a>验证实例

若要验证是否已创建实例，请在 Azure 门户中转到 [Azure 数字孪生页面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 。 可以通过在门户搜索栏中搜索 " *Azure 数字孪生* " 自行访问此页。

此页列出了所有 Azure 数字孪生实例。 在列表中查找新创建的实例的名称。

如果验证失败，可以使用 [门户](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) 或 [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)重试创建实例。

### <a name="verify-user-role-assignment"></a>验证用户角色分配

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 回忆一下，该脚本当前将此必需角色分配给运行脚本的同一用户 Cloud Shell。 如果需要将此角色分配给将管理实例的其他人，可以通过 Azure 门户 ([指令](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 或 CLI ([说明](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) 来完成此操作。

如果验证不成功，还可以使用 [门户](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)重做自己的角色分配。

### <a name="verify-app-registration"></a>验证应用注册

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

接下来，验证是否已正确设置注册上的 Azure 数字孪生权限设置。 为此，请从菜单栏中选择 " *清单* "，以查看应用注册的清单代码。 滚动到代码窗口的底部，在下查找这些字段 `requiredResourceAccess` 。 值应与以下屏幕截图中的值匹配：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

如果其中一个或两个验证步骤不成功，请使用 [门户](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) 或 [CLI](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) 说明重试创建应用注册。

## <a name="other-possible-steps-for-your-organization"></a>组织的其他可能步骤

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令测试实例上的单个 REST API 调用： 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*操作说明：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)

或者，请参阅如何通过编写客户端应用的身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)