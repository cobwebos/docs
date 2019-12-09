---
title: Azure 存储资源管理器疑难解答指南 | Microsoft Docs
description: Azure 存储资源管理器调试技术的概述
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3d5b1ab4e72ec759098e9c71515200f89a8dfe82
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931212"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 存储资源管理器疑难解答指南

Microsoft Azure 存储资源管理器是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 应用可连接到托管在 Azure、国家云和 Azure Stack 上的存储帐户。

本指南汇总了存储资源管理器中经常出现的问题的解决方案。

## <a name="rbac-permissions-issues"></a>RBAC 权限问题

基于角色的访问控制[RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)通过将权限集合并为_角色_，实现对 Azure 资源进行高度粒度的访问管理。 下面是一些策略，可让 RBAC 以最佳方式在存储资源管理器中工作。

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>如何实现访问存储资源管理器中的资源？

如果在通过 RBAC 访问存储资源时遇到问题，则可能未向你分配适当的角色。 以下部分介绍了当前访问存储资源所需的权限存储资源管理器。 如果你不确定你具有适当的角色或权限，请与你的 Azure 帐户管理员联系。

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"读取：列出/获取存储帐户" 权限问题

您必须有权列出存储帐户。 若要获取此权限，您必须分配有 "_读取_者" 角色。

#### <a name="list-storage-account-keys"></a>列出存储帐户密钥

存储资源管理器还可以使用帐户密钥对请求进行身份验证。 可以通过功能更强大的角色（如 "_参与者_" 角色）获得对帐户密钥的访问权限。

> [!NOTE]
> 访问密钥向包含它们的任何人授予不受限制的权限。 因此，我们不建议你将这些密钥分发给用户。 如果需要撤消访问密钥，可以从[Azure 门户](https://portal.azure.com/)重新生成它们。

#### <a name="data-roles"></a>数据角色

您必须至少分配有一个授予访问权限的角色，才能从资源中读取数据。 例如，如果想要列出或下载 blob，则至少需要_存储 Blob 数据读取_者角色。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>为什么需要管理层角色才能在存储资源管理器中查看我的资源？

Azure 存储具有两层访问权限：_管理_和_数据_。 可以通过管理层访问订阅和存储帐户。 容器、blob 和其他数据资源通过数据层进行访问。 例如，如果想要从 Azure 中获取存储帐户的列表，则需要将请求发送到管理终结点。 如果需要帐户中的 blob 容器列表，请将请求发送到相应的服务终结点。

RBAC 角色可以包含管理或数据层访问权限。 例如，"读者" 角色向管理层资源授予只读访问权限。

严格地说，"读者" 角色不提供数据层权限，并且不需要访问数据层。

通过收集连接到 Azure 资源所需的信息，存储资源管理器可以轻松地访问资源。 例如，若要显示 blob 容器，存储资源管理器会将 "列表容器" 请求发送到 blob 服务终结点。 若要获取该终结点，请存储资源管理器搜索你有权访问的订阅和存储帐户的列表。 若要查找订阅和存储帐户，存储资源管理器还需要访问管理层。

如果没有授予任何管理层权限的角色，存储资源管理器无法获取连接到数据层所需的信息。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>如果我无法从管理员那里获得所需的管理层权限，该怎么办？

目前没有针对此问题的 RBAC 相关解决方案。 作为一种解决方法，你可以请求将 SAS URI[附加到资源](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri)。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>错误：证书链中的自签名证书（和类似错误）

证书错误通常发生在下列情况之一：

- 该应用通过_透明代理_进行连接，这意味着服务器（如公司服务器）正在截取 HTTPS 流量，对其进行解密，然后使用自签名证书对其进行加密。
- 正在运行的应用程序将自签名的 SSL 证书注入到收到的 HTTPS 消息中。 注入证书的应用程序的示例包括防病毒和网络流量检查软件。

当存储资源管理器看到自签名或不受信任的证书时，它将不再知道收到的 HTTPS 消息是否已被更改。 如果你有自签名证书的副本，则可以通过以下步骤指示存储资源管理器信任它：

1. 获取证书的64编码的 x.509 （.cer）副本。
2. 请参阅**编辑** > **SSL 证书** > **导入证书**，然后使用文件选取器查找、选择和打开 .cer 文件。

如果有多个证书（根证书和中间证书），则也可能出现此问题。 若要修复此错误，必须同时添加这两个证书。

如果你不确定证书的来源，请按照以下步骤进行查找：

1. 安装 OpenSSL。
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html)：任何光线版本都应该足以满足需要。
    * Mac 和 Linux：应包含在操作系统中。
2. 运行 OpenSSL。
    * Windows：打开安装目录，选择 " **/bin/** "，然后双击 " **openssl**"。
    * Mac 和 Linux：从终端运行 `openssl`。
3. 运行 `s_client -showcerts -connect microsoft.com:443`。
4. 查找自签名证书。 如果不确定哪些证书是自签名证书，请记下 `("s:")` 和颁发者 `("i:")` 相同的任何位置。
5. 查找自签名证书时，对于每个证书，将所有内容从（和包括） `-----BEGIN CERTIFICATE-----` 通过 `-----END CERTIFICATE-----` 复制并粘贴到新的 .cer 文件中。
6. 打开存储资源管理器并参阅**编辑** > **SSL 证书** > **导入证书**。 然后使用文件选取器查找、选择和打开您创建的 .cer 文件。

如果通过执行这些步骤无法找到任何自签名证书，请通过反馈工具联系我们。 还可以通过使用 `--ignore-certificate-errors` 标志，从命令行打开存储资源管理器。 用此标志打开时，存储资源管理器忽略证书错误。

## <a name="sign-in-issues"></a>登录问题

### <a name="blank-sign-in-dialog-box"></a>"空白登录" 对话框

当 Active Directory 联合身份验证服务（AD FS）提示存储资源管理器执行重定向（Electron 不支持）时，最常出现空白登录对话框。 若要解决此问题，可以尝试使用设备代码流进行登录。 为此，请执行下列步骤：

1. 在左侧垂直工具栏上，打开 "**设置**"。 在 "设置" 面板中，切换到 "**应用程序** > **登录**"。 启用 **"使用设备代码流登录"** 。
2. 打开 "**连接**" 对话框（通过左侧垂直条上的 "插入" 图标或选择 "帐户" 面板上的 "**添加帐户**"）。
3. 选择要登录到的环境。
4. 选择“登录”。
5. 按照下一个面板上的说明进行操作。

如果你无法登录要使用的帐户，因为默认浏览器已登录到不同的帐户，请执行以下操作之一：

- 手动将链接和代码复制到浏览器的私有会话中。
- 手动将链接和代码复制到其他浏览器中。

### <a name="reauthentication-loop-or-upn-change"></a>重新验证循环或 UPN 更改

如果你使用的是重新验证的循环或者更改了某个帐户的 UPN，请按照以下步骤操作：

1. 删除所有帐户，然后关闭存储资源管理器。
2. 从计算机中删除 .IdentityService 文件夹。 在 Windows 中，该文件夹位于 `C:\users\<username>\AppData\Local`。 对于 Mac 和 Linux，可以在用户目录的根目录中找到该文件夹。
3. 如果运行的是 Mac 或 Linux，还需要从操作系统的密钥存储中删除 IdentityService 条目。 在 Mac 上，密钥存储是*Gnome 密钥链*应用程序。 在 Linux 中，应用程序通常称为_Keyring_，但该名称可能会因分发而有所不同。

### <a name="conditional-access"></a>条件访问

由于存储资源管理器使用的 Azure AD 库中的限制，在 Windows 10、Linux 或 macOS 上使用存储资源管理器时不支持条件访问。

## <a name="mac-keychain-errors"></a>Mac 密钥链错误

MacOS 密钥链有时可以输入导致存储资源管理器身份验证库出现问题的状态。 若要使密钥链进入此状态，请执行以下步骤：

1. 关闭存储资源管理器。
2. 打开密钥链（按 Command + 空格键，键入**密钥链**，然后按 enter）。
3. 选择 "登录名" 密钥链。
4. 选择挂锁图标锁定密钥链。 （进程完成后，挂锁将显示为 "已锁定"。 可能需要几秒钟的时间，具体取决于已打开的应用程序。

    ![挂锁图标](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. 打开存储资源管理器。
6. 系统将提示 "服务中心想要访问密钥链" 等消息。 输入 Mac 管理员帐户密码，并选择 "**始终允许**" （或 "**允许** **始终允许**不可用"）。
7. 请尝试登录。

### <a name="general-sign-in-troubleshooting-steps"></a>常规登录故障排除步骤

* 如果你在 macOS 上，并且登录窗口永远不会显示在 "**等待身份验证**" 对话框上，请尝试执行[以下步骤](#mac-keychain-errors)。
* 重新启动存储资源管理器。
* 如果身份验证窗口为空，请等待至少一分钟，然后关闭身份验证对话框。
* 请确保已正确配置你的计算机和存储资源管理器的代理和证书设置。
* 如果运行的是 Windows，并有权访问同一台计算机上的 Visual Studio 2019 和登录凭据，请尝试登录到 Visual Studio 2019。 成功登录到 Visual Studio 2019 后，可以在 "帐户" 面板中打开存储资源管理器并查看帐户。

如果这些方法都不起作用，请[打开 GitHub 中的问题](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>缺少订阅和中断的租户

如果成功登录后无法检索订阅，请尝试以下故障排除方法：

* 验证你的帐户是否有权访问所需的订阅。 可以通过登录到要尝试使用的 Azure 环境的门户来验证访问权限。
* 请确保已通过正确的 Azure 环境（Azure、Azure 中国世纪互联、Azure 德国、Azure 美国政府或自定义环境）登录。
* 如果在代理服务器后面，请确保已正确配置存储资源管理器代理。
* 请尝试删除并重新添加该帐户。
* 如果有 "详细信息" 链接，请检查为失败的租户报告了哪些错误消息。 如果你不确定如何响应错误消息，可以随意[在 GitHub 中打开问题](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>无法删除附加的帐户或存储资源

如果无法通过 UI 删除附加的帐户或存储资源，可以通过删除以下文件夹手动删除所有附加的资源：

* Windows：`%AppData%/StorageExplorer`
* macOS：`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux：`~/.config/StorageExplorer`

> [!NOTE]
> 关闭存储资源管理器，然后再删除这些文件夹。

> [!NOTE]
> 如果你曾导入了任何 SSL 证书，请备份 `certs` 目录的内容。 以后，可以使用备份来重新导入 SSL 证书。

## <a name="proxy-issues"></a>代理问题

首先，请确保输入的以下信息正确：

* 代理 URL 和端口号
* 用户名和密码（如果代理需要）

> [!NOTE]
> 存储资源管理器不支持代理自动配置文件来配置代理设置。

### <a name="common-solutions"></a>常见解决方法

如果仍遇到问题，请尝试以下故障排除方法：

* 如果你可以在不使用代理的情况下连接到 internet，请验证存储资源管理器在未启用代理设置的情况下正常工作。 若是，则可能是代理设置出错。 与管理员合作来确定问题。
* 验证使用代理服务器的其他应用程序是否按预期方式工作。
* 验证是否可以连接到要尝试使用的 Azure 环境的门户。
* 验证能否从服务终结点接收响应。 在浏览器中输入一个终结点 URL。 如果可以连接，则应收到 InvalidQueryParameterValue 或类似的 XML 响应。
* 如果其他人也通过你的代理服务器使用存储资源管理器，请验证他们能否连接。 如果可以，则可能需要联系代理服务器管理员。

### <a name="tools-for-diagnosing-issues"></a>诊断问题的工具

如果你有网络工具（如 Fiddler for Windows），则可以按以下方式诊断问题：

* 如果必须通过代理工作，则须将网络工具配置为通过代理连接。
* 检查网络工具使用的端口号。
* 在存储资源管理器中，输入本地主机 URL和网络工具的端口号作为代理设置。 如果正确执行此操作，网络工具将开始记录存储资源管理器发送到管理终结点和服务终结点发出的网络请求。 例如，在浏览器中为 blob 终结点输入 `https://cawablobgrs.blob.core.windows.net/`，并收到类似于下面的响应：

  ![代码示例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  此响应建议资源存在，即使你无法访问该资源。

### <a name="contact-proxy-server-admin"></a>联系代理服务器管理员

如果代理设置正确，则可能需要联系代理服务器管理员：

* 请确保代理不会阻止流量流向 Azure 管理或资源终结点。
* 验证代理服务器使用的身份验证协议。 存储资源管理器当前不支持 NTLM 代理。

## <a name="unable-to-retrieve-children-error-message"></a>“无法检索子级”错误消息

如果通过代理连接到 Azure，请验证代理设置是否正确。 如果授予从订阅或帐户的所有者访问资源的权限，请验证你对该资源具有 "读取" 或 "列出" 权限。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>连接字符串没有完整的配置设置

如果收到此错误消息，则表示你没有必要的权限，无法获取存储帐户的密钥。 若要确认是否属于这种情况，请前往门户并找到存储帐户。 为此，可以右键单击存储帐户的节点，然后选择 "**在门户中打开**"。 然后，请访问 "**访问密钥**" 边栏选项卡。 如果你没有查看密钥的权限，你将看到 "你没有访问权限" 消息。 若要解决此问题，你可以从其他人那里获取帐户密钥，并通过名称和密钥进行附加，或者可以向某人提供存储帐户的 SAS，并使用它来附加存储帐户。

如果你看到帐户密钥，请在 GitHub 中指出一个问题，以便我们可以帮助你解决问题。

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>添加新连接时出错： TypeError：无法读取未定义的属性 "version"

如果你在尝试添加自定义连接时收到此错误消息，则存储在本地凭据管理器中的连接数据可能已损坏。 若要解决此问题，请尝试删除已损坏的本地连接，然后重新添加它们：

1. 开始存储资源管理器。 从菜单中转到 "**帮助**" > **切换开发人员工具**。
2. 在打开的窗口中，在 "**应用程序**" 选项卡上，中转到 "**本地存储**" （左侧） > **file://** "。
3. 根据遇到问题的连接类型，查找其密钥，然后将其值复制到文本编辑器。 该值是自定义连接名称的数组，如下所示：
    * 存储帐户
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blob 容器
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * 文件共享
        * `StorageExplorer_CustomConnections_Files_v1`
    * 队列
        * `StorageExplorer_CustomConnections_Queues_v1`
    * 表
        * `StorageExplorer_CustomConnections_Tables_v1`
4. 保存当前连接名称后，将开发人员工具中的值设置为 `[]`。

如果要保留未损坏的连接，可以使用以下步骤来查找损坏的连接。 如果不介意丢失所有现有连接，则可以跳过这些步骤，并遵循平台特定的说明来清除连接数据。

1. 从文本编辑器中，将每个连接名称重新添加到开发人员工具，然后检查连接是否仍然正常工作。
2. 如果连接正常工作，则它不会损坏，你可以安全地将其保留。 如果连接不起作用，请从开发人员工具中删除其值，并将其记录，以便以后可以将其重新添加。
3. 重复此步骤，直到检查完所有连接。

完成所有连接后，对于所有未添加回的连接名称，必须清除损坏的数据（如果有），并使用存储资源管理器中的标准步骤将其添加回来：

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. 在 "**开始**" 菜单上，搜索 "**凭据管理器**" 并将其打开。
2. 中转到 " **Windows 凭据**"。
3. 在 "**一般凭据**" 下，查找具有 `<connection_type_key>/<corrupted_connection_name>` 密钥的条目（例如，`StorageExplorer_CustomConnections_Accounts_v1/account1`）。
4. 删除这些条目并重新添加连接。

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. 打开聚焦（命令 + 空格键），搜索**密钥链访问**。
2. 查找具有 `<connection_type_key>/<corrupted_connection_name>` 密钥的条目（例如，`StorageExplorer_CustomConnections_Accounts_v1/account1`）。
3. 删除这些条目并重新添加连接。

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

本地凭据管理因 Linux 分发而异。 如果 Linux 发行版未提供用于本地凭据管理的内置 GUI 工具，则可以安装第三方工具来管理本地凭据。 例如，可以使用[Seahorse](https://wiki.gnome.org/Apps/Seahorse/)，它是用于管理 Linux 本地凭据的开源 GUI 工具。

1. 打开本地凭据管理工具，查找已保存的凭据。
2. 查找具有 `<connection_type_key>/<corrupted_connection_name>` 密钥的条目（例如，`StorageExplorer_CustomConnections_Accounts_v1/account1`）。
3. 删除这些条目并重新添加连接。
---

如果在运行这些步骤后仍然遇到此错误，或者如果想要共享你的怀疑已损坏连接的内容，请在 GitHub 页上[打开问题](https://github.com/microsoft/AzureStorageExplorer/issues)。

## <a name="issues-with-sas-url"></a>SAS URL 的问题

如果是通过 SAS URL 连接到服务并遇到错误：

* 验证该 URL 是否提供读取和列出资源的必需权限。
* 验证该 URL 是否未过期。
* 如果 SAS URL建立在访问策略基础上，请验证该访问策略是否被撤销。

如果使用无效的 SAS URL，但现在无法分离，请执行以下步骤：

1. 运行存储资源管理器时，请按 F12 打开 "开发人员工具" 窗口。
2. 在 "**应用程序**" 选项卡上，选择左侧树中的 "**本地存储** > **file://** "。
3. 查找与有问题的 SAS URI 服务类型关联的键。 例如，如果用于 blob 容器的 SAS URI 错误，请查找名为 `StorageExplorer_AddStorageServiceSAS_v1_blob` 的键。
4. 键的值应为 JSON 数组。 查找与错误 URI 关联的对象，然后将其删除。
5. 按 Ctrl+R 重新加载存储资源管理器。

## <a name="linux-dependencies"></a>Linux 依赖项

存储资源管理器1.10.0 和更高版本可从 Snap 店进行管理。 存储资源管理器 snap 会自动安装其所有依赖项，并在新版本的 snap 可用时进行更新。 安装存储资源管理器 snap 是推荐的安装方法。

存储资源管理器要求使用密码管理器，你可能需要手动连接才能正常运行存储资源管理器。 可以通过运行以下命令，将存储资源管理器连接到系统的密码管理器：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

你还可以将应用程序下载为 gz 文件，但你必须手动安装依赖项。

> [!IMPORTANT]
> Gz 下载中提供的存储资源管理器仅支持 Ubuntu 分发版。 尚未验证其他分发版，可能需要其他包。

这些包是在 Linux 上存储资源管理器的最常见要求：

* [.NET Core 2.2 运行时](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` 或 `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> 存储资源管理器版本1.7.0 和更早版本需要 .NET Core 2.0。 如果已安装较新版本的 .NET Core，则需要[修补存储资源管理器](#patching-storage-explorer-for-newer-versions-of-net-core)。 如果正在运行存储资源管理器1.8.0 或更高版本，则应最多使用 .NET Core 2.2。 目前尚未验证版本超过2.2 的版本。

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. 下载存储资源管理器。
2. 安装[.Net Core 运行时](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)。
3. 运行以下命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. 下载存储资源管理器。
2. 安装[.Net Core 运行时](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)。
3. 运行以下命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. 下载存储资源管理器。
2. 安装[.Net Core 运行时](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)。
3. 运行以下命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. 下载存储资源管理器。
2. 安装[.Net Core 运行时](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)。
3. 运行以下命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>修补存储资源管理器较新版本的 .NET Core

对于存储资源管理器1.7.0 或更早版本，可能需要修补存储资源管理器使用的 .NET Core 版本：

1. [从 NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)下载版本 1.5.43 of StreamJsonRpc。 查找页面右侧的 "下载包" 链接。
2. 下载包后，将其文件扩展名从 `.nupkg` 更改为 `.zip`。
3. 解压缩包。
4. 打开 `streamjsonrpc.1.5.43/lib/netstandard1.1/` 文件夹。
5. 将 `StreamJsonRpc.dll` 复制到存储资源管理器文件夹中的以下位置：
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure 门户中的 "从资源管理器打开" 不起作用

如果 Azure 门户上的 "**在资源管理器中打开**" 按钮不起作用，请确保使用的是兼容的浏览器。 以下浏览器已测试兼容性：
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>后续步骤

如果这些解决方案都不适合你，请[打开 GitHub 中的问题](https://github.com/Microsoft/AzureStorageExplorer/issues)。 还可以通过在左下角选择 "将**问题报告给 GitHub** " 按钮来执行此操作。

![反馈](./media/storage-explorer-troubleshooting/feedback-button.PNG)
