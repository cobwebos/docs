---
title: "通过 CLI 登录到 Azure | Microsoft Docs"
description: "从适用于 Mac、Linux 和 Windows 的 Azure 命令行界面 (Azure CLI) 连接到 Azure 订阅"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
translationtype: Human Translation
ms.sourcegitcommit: 03d93ac0416a947d43dca66297588c4c5b656a1d
ms.openlocfilehash: 18ea4ab3ab6b82fd43ee290ece790d61cabf16a4


---
# <a name="log-in-to-azure-from-the-azure-cli"></a>通过 Azure CLI 登录到 Azure
Azure CLI 是一组开源且跨平台的命令，用于处理 Azure 资源。 本文介绍了提供 Azure 帐户凭据以将 Azure CLI 连接到 Azure 订阅的各种方式。

* 运行 `azure login` CLI 命令以通过 Azure Active Directory 进行身份验证。 此方法允许你以两种[命令模式](#cli-command-modes)访问 CLI 命令。 在不采用附加选项的情况下运行命令时，`azure login` 会通过 web 门户以交互方式提示你继续登录。 有关附加的 `azure login` 命令选项，请参阅本文中的方案或者键入 `azure login --help`。
* 如果只需要使用 Azure 服务管理模式 CLI 命令（对于大多数新部署，不推荐使用），可以下载并在计算机上安装发布设置。

如果尚未安装 CLI，请参阅[安装 Azure CLI](xplat-cli-install.md)。 如果没有 Azure 订阅，只需几分钟即可创建一个[免费帐户](http://azure.microsoft.com/free/)。

有关不同的帐户标识和 Azure 订阅的背景信息，请参阅 [Azure 订阅如何与 Azure Active Directory 相关联](active-directory/active-directory-how-subscriptions-associated-directory.md)。

## <a name="scenario-1-azure-login-with-interactive-login"></a>方案 1：使用交互式登录的 azure 登录
使用某些帐户时，CLI 会要求你运行 `azure login` 然后使用 web 浏览器通过某个 web 门户继续执行登录过程，此过程称为*交互式登录*。 一个常见原因是你具有设置为要求进行多重身份验证的工作或学校帐户（也称为*组织帐户*）。 希望使用资源管理器模式的命令时，也请使用 Microsoft 帐户进行交互式登录。

交互式登录很简单：键入 `azure login` -- 不带任何选项 -- 如下例中所示：

```
azure login
```                                                                                             

输出如下所示：

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
复制在命令输出中提供的代码，并将浏览器打开到 http://aka.ms/devicelogin 或其他页面（如果已指定）。 （可以在同一计算机上打开浏览器，也可以在其他计算机或设备上打开浏览器。）输入代码，然后系统会提示你为要使用的标识输入用户名和密码。 该过程完成后，命令 shell 将完成登录。 它的外观可能如下：

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> 使用交互式登录时，将使用 Azure Active Directory 进行身份验证和授权。 如果你使用 Microsoft 帐户标识，登录过程将访问你的 Azure Active Directory 默认域。 （如果注册的是免费 Azure 帐户，Azure Active Directory 已自动为你的帐户创建了默认域。）
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>方案 2：使用用户名和密码的 azure 登录
如果希望使用不需要多重身份验证的工作或学校帐户，请使用带 (`-u`) 参数的 `azure login` 命令进行身份验证。 将在命令行上提示你输入密码（你还可以将密码传递为 `azure login` 命令的附加参数）。 以下示例将传递组织帐户的用户名：

    azure login -u myUserName@contoso.onmicrosoft.com

然后，会提示你输入密码：

    info:    Executing command login
    Password: *********

然后，登录过程完成。

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

如果这是你首次使用这些凭据登录，系统将要求你确认是否希望缓存身份验证令牌。 如果你以前使用了 `azure logout` 命令，则也会出现此提示（如本文稍后所述）。 若要在自动化应用场景中绕过此提示，请使用 `-q` 参数运行 `azure login`。

## <a name="scenario-3-azure-login-with-a-service-principal"></a>方案 3：使用服务主体的 azure 登录
如果已创建 Active Directory 应用程序的服务主体，并且服务主体拥有针对订阅的权限，就可以使用 `azure login` 命令来对服务主体进行身份验证。 根据你的方案，可以提供服务主体的凭据作为 `azure login` 命令的显式参数。 例如，以下命令传递服务主体名称和 Active Directory 租户 ID：

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

然后，会提示你提供密码。 对于自动化方案，还可以通过 CLI 脚本或应用程序代码提供凭据，或者使用证书以非交互方式对服务主体进行身份验证。 有关详细信息与示例，请参阅[使用 Azure Resource Manager 对服务主体进行身份验证](resource-group-authenticate-service-principal-cli.md)。

## <a name="scenario-4-use-a-publish-settings-file"></a>方案 4：使用发布设置文件
如果你只需要使用 Azure 服务管理模式 CLI 命令（例如，在经典部署模型中部署 Azure VM），则可以使用发布设置文件进行连接。 此方法将在本地计算机上安装证书，只要订阅和证书有效，你就可以执行管理任务。

* **若要为你的帐户下载发布设置文件**，请通过键入 `azure config mode asm` 确保 CLI 处于服务管理模式。 然后，运行以下命令：

        azure account download

这将打开默认浏览器，并提示你登录到 [Azure 经典门户](https://manage.windowsazure.com)。 登录后，将下载 `.publishsettings` 文件。 记下此文件的保存位置。

> [!NOTE]
> 如果你的帐户与多个 Azure Active Directory 租户关联，系统将提示你选择要为哪个 Active Directory 下载发布设置文件。
>
>

使用下载页面进行选择或通过访问 Azure 经典门户进行选择之后，所选的 Active Directory 将成为经典门户和下载页面使用的默认值。 设定默认值之后，可以在下载页面的顶部看到文本“若要返回选择页面，请单击此处”。 使用提供的链接返回选择页面。

* **若要导入发布设置文件**，请运行以下命令：

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> 导入发布设置后，应删除 `.publishsettings` 文件。 因为 Azure CLI 不再需要该文件，并且该文件会产生安全风险，因为它可以用来访问你的订阅。
>
>

## <a name="cli-command-modes"></a>CLI 命令模式
Azure CLI 提供两种命令模式让你使用不同的命令集来处理 Azure 资源：

* **资源管理器模式** - 用于在资源管理器部署模型中处理 Azure 资源。 若要设置此模式，请运行 `azure config mode arm`。
* **服务管理模式** - 用于在经典部署模型中处理 Azure 资源。 若要设置此模式，请运行 `azure config mode asm`。

在首次安装时，CLI 的最新版本采用资源管理器模式。

> [!NOTE]
> 资源管理器模式与服务管理模式互斥。 即在一种模式下创建的资源不能从另一种模式进行管理。
>
>

## <a name="multiple-subscriptions"></a>多个订阅
如果你有多个 Azure 订阅，则连接到 Azure 即有权访问与你的凭据关联的所有订阅。 将选择一个订阅作为默认订阅，由 Azure CLI 在执行操作时使用。 可以使用 `azure account list` 命令查看订阅，包括当前的默认订阅。 此命令将返回如下信息：

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

在上面的列表中，**Current** 列指示当前的默认订阅为 Azure-sub-1。 若要更改默认订阅，请使用 `azure account set` 命令，并指定你想要其成为默认订阅的订阅。 例如：

    azure account set Azure-sub-2

这会将默认订阅更改为 Azure-sub-2。

> [!NOTE]
> 更改默认订阅将会立即生效，并且是全局更改；新的 Azure CLI 命令（无论是从同一命令行实例还是其他实例运行的）将使用新的默认订阅。
>
>

如果要将非默认订阅用于 Azure CLI，但不想更改当前默认订阅，则可以将 `--subscription` 选项用于命令并提供要用于此操作的订阅名称。

一旦连接到你的 Azure 订阅，即可使用 Azure CLI 命令来处理 Azure 资源。

## <a name="storage-of-cli-settings"></a>CLI 设置的存储
无论你是使用 `azure login` 命令登录，还是导入发布设置，CLI 配置文件和日志都存储在 `.azure` 目录（位于你的 `user` 目录中）中。 `user` 目录由操作系统提供保护。 但是，建议你采取其他措施来对 `user` 目录进行加密。 你可通过以下方式完成此操作：

* 在 Windows 上，修改目录属性或使用 BitLocker。
* 在 Mac 上，为目录启用 FileVault。
* 在 Ubuntu 上，使用“加密主目录”功能。 其他 Linux 分发提供了类似功能。

## <a name="logging-out"></a>注销
若要注销，请使用以下命令：

    azure logout -u <username>

如果与帐户关联的订阅只使用 Active Directory 进行身份验证，则注销将从本地配置文件中删除订阅信息。 但是，如果还为订阅导入了发布设置文件，则注销将仅从本地配置文件中删除与 Active Directory 相关的信息。

## <a name="next-steps"></a>后续步骤
* 若要使用 Azure CLI 命令，请参阅[资源管理器模式中的 Azure CLI 命令](virtual-machines/azure-cli-arm-commands.md)和[服务管理模式中的 Azure CLI 命令](virtual-machines-command-line-tools.md)。
* 若要了解有关 Azure CLI、下载源代码、报告问题或贡献项目的详细信息，请访问[适用于 Azure CLI 的 GitHub 存储库](https://github.com/azure/azure-xplat-cli)。
* 如果你在使用 Azure CLI 或 Azure 时遇到问题，请访问 [Azure 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)。



<!--HONumber=Nov16_HO3-->


