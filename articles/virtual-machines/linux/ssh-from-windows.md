---
title: "针对 Linux VM 将 SSH 密钥与 Windows 配合使用 | Microsoft Docs"
description: "了解如何在 Windows 计算机上生成和使用 SSH 密钥连接到 Azure 上的 Linux 虚拟机。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 7f572adf499dd1fcd0db19500c9049af1e31cdea
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>如何在 Azure 上将 SSH 密钥与 Windows 配合使用
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

连接到 Azure 中的 Linux 虚拟机 (VM) 时，应使用[公钥加密](https://wikipedia.org/wiki/Public-key_cryptography)提供更安全的方式登录到 Linux VM。 此过程涉及使用安全外壳 (SSH) 命令进行公钥和私钥交换，对自己（而不是用户名和密码）进行身份验证。 密码易受到强力破解攻击，特别是在面向 Internet 的 VM（如 Web 服务器）上。 本文概述 SSH 密钥以及如何在 Windows 计算机上生成相应的密钥。

## <a name="overview-of-ssh-and-keys"></a>SSH 和密钥概述
可以使用公钥和私钥安全地登录到 Linux VM：

* **公钥**放置在 Linux VM 上或者要对其使用公钥加密的任何其他服务中。
* **私钥**是在登录时要向 Linux VM 提供的内容，用于验证你的身份。 请保护好私钥， 不要透露给其他人。

可以在多个 VM 和服务上使用这些公钥和私钥。 无需对要访问的每个 VM 或服务使用密钥对。 有关更详细的概述，请参阅[公钥加密](https://wikipedia.org/wiki/Public-key_cryptography)。

SSH 是一种加密的连接协议，利用该协议可以通过未受保护的连接进行安全登录。 它是在 Azure 中托管的 Linux VM 的默认连接协议。 虽然 SSH 本身提供加密连接，但是将密码用于 SSH 连接仍使 VM 易受到强力破解攻击或猜测密码。 使用 SSH 连接到 VM 的更安全且首选的方法是使用这些公钥和私钥，也称为 SSH 密钥。

如果不希望使用 SSH 密钥，可以仍使用密码登录到 Linux VM。 如果 VM 不向 Internet 公开，使用密码可能已足够。 但是，仍需要管理每个 Linux VM 的密码和维护正常密码策略和做法（如最小密码长度）并定期进行更新。 使用 SSH 密钥可降低跨多个 VM 管理单个凭据的复杂性。

## <a name="windows-packages-and-ssh-clients"></a>Windows 程序包和 SSH 客户端
可使用 **SSH 客户端**连接到 Azure 中的 Linux VM，并对其进行管理。 Windows 计算机通常未安装 SSH 客户端。 Windows 10 周年更新已添加 Bash for Windows，而最新版 Windows 10 创意者更新提供其他更新。 通过此适用于 Linux 的 Windows 子系统，可在 Bash shell 中以本机方式运行并访问 SSH 客户端等实用工具。 然后可以按照任何 Linux 文档（例如[如何为 Linux 生成 SSH 密钥对](mac-create-ssh-keys.md)）进行操作。 Bash for Windows 仍在开发，当前版本视为 beta 版本。 有关 Bash for Windows 的详细信息，请参阅 [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)（Windows 上的 Bash on Ubuntu）。

如果不希望使用 Bash for Windows，以下包中包含可安装的常见 Windows SSH 客户端：

* [Git For Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>你需要创建哪些密钥文件？
Azure 需要至少 2048 位采用 **ssh-rsa** 格式的公钥和私钥。 如果要使用经典部署模型管理 Azure 资源，还需要生成 PEM（`.pem` 文件）。

以下是部署方案，以及你在每个方案中使用的文件类型：

1. **ssh-rsa** 密钥是使用 [Azure 门户](https://portal.azure.com)进行任何部署所必需的，并且是使用 [Azure CLI](../../cli-install-nodejs.md) 进行 Resource Manager 部署所必需的。
   * 这些密钥通常是大多数人都需要的。
2. 使用经典部署创建 VM 时需要 `.pem` 文件。 使用 [Azure 门户](https://portal.azure.com)或 [Azure CLI](../../cli-install-nodejs.md) 时，经典部署支持这些密钥。
   * 如果要管理使用经典部署模型创建的资源，只需创建这些附加密钥和证书。

## <a name="install-git-for-windows"></a>安装 Git for Windows
前面列出的多个程序包包含适用于 Windows 的 `openssl` 工具。 若要创建公钥和私钥，需要此工具。 以下示例详细说明了如何安装和使用 **Git for Windows**，但你可以选择所喜欢的任何程序包。 使用 **Git for Windows**，可以访问某些其他开放源代码软件 ([OSS](https://en.wikipedia.org/wiki/Open-source_software))，使用 Linux VM 时这些工具和实用程序可能会很有用。

1. 从以下位置下载并安装 **Git for Windows**：[https://git-for-windows.github.io/](https://git-for-windows.github.io/)。
2. 在安装过程中，除非特别需要更改选项，否则请接受默认选项。
3. 从“开始”菜单 > “Git” > “Git Bash”运行“Git Bash”。 控制台与以下示例类似：

    ![Git for Windows Bash shell](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>创建私钥
1. 在“Git Bash”窗口中，使用 `openssl.exe` 创建私钥。 以下示例创建名为 `myPrivateKey` 的密钥和名为 `myCert.pem` 的证书：

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    输出与以下示例类似：

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   如果 bash 报告错误，请尝试使用提升的权限打开一个新 **Git Bash** 窗口。 然后，重新运行 `openssl` 命令。

2. 应答国家/地区名称、位置、组织名称等提示。
3. 将在当前工作目录中创建新的私钥和证书。 为了安全起见，应在私钥上设置权限，以便只有你可以访问它：

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. [下一节](#create-a-private-key-for-putty)详细说明了如何使用 PuTTYgen 同时查看和使用公钥，以及创建专用于使用 PuTTY 通过 SSH 登录 Linux VM 的私钥。 以下命令生成一个名为 `myPublicKey.key` 的公钥文件，你可以立即使用它：

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. 如果还需要管理经典资源，请将 `myCert.pem` 转换为 `myCert.cer`（DER 编码的 X509 证书）。 仅当需要专门管理较旧的经典资源时，才执行此可选步骤。

    使用以下命令转换证书：

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>为 PuTTY 创建私钥
PuTTY 是适用于 Windows 的常见 SSH 客户端。 你可随意使用任何所需的 SSH 客户端。 若要使用 PuTTY，需要创建其他类型的密钥 - PuTTY 私钥 (PPK)。 如果不希望使用 PuTTY，请跳过此部分。

以下示例专为要使用的 PuTTY 创建此附加私钥：

1. 使用 **Git Bash** 将私钥转换为 PuTTYgen 可以识别的 RSA 私钥。 以下示例基于名为 `myPrivateKey` 的现有密钥创建名为 `myPrivateKey_rsa` 的密钥：

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    为了安全起见，应在私钥上设置权限，以便只有你可以访问它：

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. 从以下位置下载并运行 PuTTYgen：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. 单击菜单：“文件” > “加载私钥”
4. 找到你的私钥（前面的示例中的 `myPrivateKey_rsa`）。 启动 **Git Bash** 时的默认目录是 `C:\Users\%username%`。 更改文件筛选器以显示“所有文件 (\*.\*)”：

    ![将现有私钥加载到 PuTTYgen 中](./media/ssh-from-windows/load-private-key.png)
5. 单击“打开”。 一个提示指示已成功导入密钥：

    ![已成功将密钥导入到 PuTTYgen 中](./media/ssh-from-windows/successfully-imported-key.png)
6. 单击“确定”以关闭该提示。
7. 公钥将显示在“PuTTYgen”窗口的顶部。 创建 Linux VM 时，将此公钥复制并粘贴到 Azure 门户或 Azure Resource Manager 模板中。 也可以单击“保存公钥”将副本保存到计算机中：

    ![保存 PuTTY 公钥文件](./media/ssh-from-windows/save-public-key.png)

    以下示例演示在创建 Linux VM 时如何将此公钥复制并粘贴到 Azure 门户。 然后，此公钥通常存储在新 VM 上的 `~/.ssh/authorized_keys` 中。

    ![在 Azure 门户中创建 VM 时使用公钥](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. 返回到 **PuTTYgen** 中，单击“保存私钥”：

    ![保存 PuTTY 私钥文件](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > 会显示一个提示，询问是否想要继续，而不输入密钥的通行短语。 通行短语就像附加到私钥的密码。 即使有人获取了你的私钥，他们仍将不能只使用密钥进行身份验证。 他们还需要通行短语。 没有通行短语，如果有人获取了你的私钥，他们可以登录到使用该密钥的任何 VM 或服务。 建议你创建一个通行短语。 但是，如果你忘记了通行短语，将没有办法恢复它。
   >
   >

    如果要输入通行短语，请单击“否”，在主要的 PuTTYgen 窗口中输入通行短语，然后再次单击“保存私钥”。 否则，请单击“是”以继续，而不提供可选的通行短语。
9. 输入名称和位置以保存 PPK 文件。

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>使用 Putty 通过 SSH 登录到 Linux 计算机
再说一遍，PuTTY 是适用于 Windows 的常见 SSH 客户端。 你可随意使用任何所需的 SSH 客户端。 以下步骤详细说明了如何使用私钥通过 SSH 向 Azure VM 进行身份验证。 这些步骤与其他 SSH 密钥客户端类似，都需要加载私钥以对 SSH 连接进行身份验证。

1. 从以下位置下载并运行 putty：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. 在 Azure 门户中填写 VM 的主机名或 IP 地址：

    ![打开新的 PuTTY 连接](./media/ssh-from-windows/putty-new-connection.png)
3. 在选择“打开”之前，单击“连接” > “SSH” > “身份验证”选项卡。 浏览到你的私钥并选择它：

    ![选择用于身份验证的 PuTTY 私钥文件](./media/ssh-from-windows/putty-auth-dialog.png)
4. 单击“打开”以连接到虚拟机

## <a name="next-steps"></a>后续步骤
还可以[使用 OS X 和 Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 生成公钥和私钥。

有关 Bash for Windows 以及使 OSS 工具在 Windows 计算机上随时可用的好处的详细信息，请参阅 [Windows 上的 Bash on Ubuntu](https://msdn.microsoft.com/commandline/wsl/about)。

如果在使用 SSH 连接到 Linux VM 时遇到问题，请参阅 [Troubleshoot SSH connections to an Azure Linux VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（通过 SSH 连接到 Azure Linux VM 故障排除）。

