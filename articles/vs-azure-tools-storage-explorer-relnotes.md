---
title: "Microsoft Azure 存储资源管理器（预览版）发行说明"
description: "Microsoft Azure 存储资源管理器（预览版）发行说明"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.openlocfilehash: 6268cff5f6c87d269f431dcdf5e6a1ee2e2bcf1f
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2018
---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Microsoft Azure 存储资源管理器（预览版）发行说明

本文包含 Azure 存储资源管理器 0.9.3（预览版）及先前版本的发行说明。

[Microsoft Azure 存储资源管理器（预览版）](./vs-azure-tools-storage-manage-with-storage-explorer.md)是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。

## <a name="version-093"></a>版本 0.9.3
12/08/2017

### <a name="download-azure-storage-explorer-093-preview"></a>下载 Azure 存储资源管理器 0.9.3（预览版）
- [适用于 Windows 的 Azure 存储资源管理器 0.9.3（预览版）](https://go.microsoft.com/fwlink/?LinkId=708343)
- [适用于 Mac 的 Azure 存储资源管理器 0.9.3（预览版）](https://go.microsoft.com/fwlink/?LinkId=708342)
- [适用于 Linux 的 Azure 存储资源管理器 0.9.3（预览版）](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>新建
* 在以下情况下，将重新使用现有的存储资源管理器窗口：
    * 打开存储资源管理器中生成的直接链接。
    * 从门户打开存储资源管理器。
    * 从 Azure 存储 VS Code 扩展（即将推出）打开存储资源管理器。
* 增加了从存储资源管理器内部打开新存储资源管理器窗口的功能。
    * 对于 Windows，文件菜单下以及任务栏的上下文菜单中有一个“新建窗口”选项。
    * 对于 Mac，应用菜单下有一个“新建窗口”选项。

### <a name="fixes"></a>修复项
* 以前不会适当地清理旧活动。 这影响了长时间运行的作业的性能。 现在会正确清理旧活动。
* 涉及大量文件和目录的操作有时可能导致存储资源管理器冻结。 现在限制了对 Azure 的文件共享请求，以限制系统资源的使用。

### <a name="known-issues"></a>已知问题
* 存储资源管理器不支持 ADFS 帐户。
* “查看资源管理器”和“查看帐户管理”的快捷键应当分别为 Ctrl/Cmd+Shift+E 和 Ctrl/Cmd+Shift+A。
* 当以 Azure Stack 为目标时，将某些文件作为追加 blob 进行上传可能会失败。
* 对任务单击“取消”后，可能需要一段时间才能取消该任务。 这是因为我们使用的是此处介绍的“取消筛选”解决办法。
* 如果选择错误的 PIN/智能卡证书，需要重启存储资源管理器使其忘记该选择。
* 帐户设置面板可能显示需重新输入凭据以筛选订阅。
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据。
* 尽管 Azure Stack 当前不支持文件共享，但附加 Azure Stack 存储帐户下仍会显示“文件共享”节点。
* 存储资源管理器使用的 Electron shell 在进行某项 GPU（图形处理单元）硬件加速时出现问题。 如果存储资源管理器显示了一个空白（空的）主窗口，则可以尝试从命令行启动存储资源管理器，并通过添加 `--disable-gpu` 开关禁用 GPU 加速。
```
./StorageExplorer --disable-gpu
```
* 对于 Ubuntu 14.04 用户，需确保 GCC 是最新版本 - 为此，可运行以下命令并重启计算机：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 对于 Ubuntu 17.04 用户，需要安装 GConf - 通过运行以下命令，然后重启计算机即可完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>版本 0.9.2
2017/11/01

### <a name="download-azure-storage-explorer-092-preview"></a>下载 Azure 存储资源管理器 0.9.2（预览版）
* [下载适用于 Windows 的 Azure 存储资源管理器 0.9.2（预览版）](https://go.microsoft.com/fwlink/?LinkId=809306)
* [下载适用于 Mac 的 Azure 存储资源管理器 0.9.2（预览版）](https://go.microsoft.com/fwlink/?LinkId=809307)
* [下载适用于 Linux 的 Azure 存储资源管理器 0.9.2（预览版）](https://go.microsoft.com/fwlink/?LinkId=809308)



### <a name="hotfixes"></a>修补程序
* 根据本地时区编辑表实体的 Edm.DateTime 值时，可能发生意外的数据更改。 编辑器现使用纯文本文本框，以便始终如一地精确控制 Edm.DateTime 值。
* 在附有名称和密钥时无法启动一组 blob 的上传/下载。 此问题已解决。
* 如果选择了帐户的一个或多个订阅，以前的存储资源管理器仅会提示重新验证过时帐户。 现在，即使完全筛选掉该帐户，存储资源管理器仍会提示。
* 适用于 Azure 美国政府版的终结点域不正确。 此问题已解决。
* 有时很难单击“管理帐户”面板上的“应用”按钮。 不会再发生此问题。

### <a name="new"></a>新建
* 预览版对 Azure Cosmos DB 的支持：
    * [联机文档](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)
    * 创建数据库和集合
    * 操作数据
    * 查询、创建或删除文档
    * 更新存储过程、用户定义的函数或触发器
    * 使用连接字符串连接到并管理数据库
* 改善了上传/下载许多小型 blob 时的性能。
* 当 blob 上传组或 blob 下载组中出现故障时增加了“全部重试”操作。
* 现在，在 blob 上传/下载期间，如果检测到网络连接断开，则存储资源管理器会暂停迭代。 然后，在重新建立网络连接后，你可以恢复迭代。
* 增加了通过上下文菜单“全部关闭”、“关闭其他”和“关闭”选项卡的能力。
* 存储资源管理器现在使用本机对话框和本机上下文菜单。
* 存储资源管理器现在更易于访问。 改进包括：
    * 针对 Windows 上的 NVDA 和 Mac 上的 VoiceOver，改进了屏幕阅读器支持
    * 改进了高对比度主题
    * 键盘 Tab 键次序和键盘焦点修复

### <a name="fixes"></a>修复项
* 过去，如果尝试下载具有无效 Windows 文件名的 blob，则操作将失败。 现在，存储资源管理器将检测 blob 名称是否有效，并且会询问你是要对其进行编码还是跳过该 blob。 存储资源管理器还会检测文件名看起来是否已编码并且在上传前会询问是否要对其进行解码。
* 在上传 blob 期间，目标 blob 容器的编辑器有时候不能正确刷新。 此问题已解决。
* 支持多种形式的连接字符串和回归的 SAS URI。 我们已解决了所有已知问题，但是如果遇到了进一步的问题，请向我们发送反馈。
* 在 0.9.0 版中，对于某些用户，更新通知损坏。 此问题已修复，并且受此 bug 影响的用户可[从此处](https://azure.microsoft.com/en-us/features/storage-explorer/)手动下载最新版本的存储资源管理器。

### <a name="known-issues"></a>已知问题
* 存储资源管理器不支持 ADFS 帐户。
* “查看资源管理器”和“查看帐户管理”的快捷键应当分别为 Ctrl/Cmd+Shift+E 和 Ctrl/Cmd+Shift+A。
* 当以 Azure Stack 为目标时，将某些文件作为追加 blob 进行上传可能会失败。
* 对任务单击“取消”后，可能需要一段时间才能取消该任务。 这是因为我们使用的是此处介绍的“取消筛选”解决办法。
* 如果选择错误的 PIN/智能卡证书，需要重启存储资源管理器使其忘记该选择。
* 帐户设置面板可能显示需重新输入凭据以筛选订阅。
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据。
* 尽管 Azure Stack 当前不支持文件共享，但附加 Azure Stack 存储帐户下仍会显示“文件共享”节点。
* 存储资源管理器使用的 Electron shell 在进行某项 GPU（图形处理单元）硬件加速时出现问题。 如果存储资源管理器显示了一个空白（空的）主窗口，则可以尝试从命令行启动存储资源管理器，并通过添加 `--disable-gpu` 开关禁用 GPU 加速。
```
./StorageExplorer --disable-gpu
```
* 对于 Ubuntu 14.04 用户，需确保 GCC 是最新版本 - 为此，可运行以下命令并重启计算机：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 对于 Ubuntu 17.04 用户，需要安装 GConf - 通过运行以下命令，然后重启计算机即可完成：

    ```
    sudo apt-get install libgconf-2-4
    ```






## <a name="previous-releases"></a>以前的版本

* [版本 0.9.1/0.9.0](#version-091)
* [版本 0.8.16](#version-0816)
* [版本 0.8.14](#version-0814)
* [版本 0.8.13](#version-0813)
* [版本 0.8.12/0.8.11/0.8.10](#version-0812--0811--0810)
* [版本 0.8.9/0.8.8](#version-089--088)
* [版本 0.8.7](#version-087)
* [版本 0.8.6](#version-086)
* [版本 0.8.5](#version-085)
* [版本 0.8.4](#version-084)
* [版本 0.8.3](#version-083)
* [版本 0.8.2](#version-082)
* [版本 0.8.0](#version-080)
* [版本 0.7.20160509.0](#version-07201605090)
* [版本 0.7.20160325.0](#version-07201603250)
* [版本 0.7.20160129.1](#version-07201601291)
* [版本 0.7.20160105.0](#version-07201601050)
* [版本 0.7.20151116.0](#version-07201511160)

## <a name="version-091--090-preview"></a>版本 0.9.1 / 0.9.0（预览版）
10/20/2017
### <a name="new"></a>新建
* 预览版对 Azure Cosmos DB 的支持：
    * [联机文档](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)
    * 创建数据库和集合
    * 操作数据
    * 查询、创建或删除文档
    * 更新存储过程、用户定义的函数或触发器
    * 使用连接字符串连接到并管理数据库
* 改善了上传/下载许多小型 blob 时的性能。
* 当 blob 上传组或 blob 下载组中出现故障时增加了“全部重试”操作。
* 现在，在 blob 上传/下载期间，如果检测到网络连接断开，则存储资源管理器会暂停迭代。 然后，在重新建立网络连接后，你可以恢复迭代。
* 增加了通过上下文菜单“全部关闭”、“关闭其他”和“关闭”选项卡的能力。
* 存储资源管理器现在使用本机对话框和本机上下文菜单。
* 存储资源管理器现在更易于访问。 改进包括：
    * 针对 Windows 上的 NVDA 和 Mac 上的 VoiceOver，改进了屏幕阅读器支持
    * 改进了高对比度主题
    * 键盘 Tab 键次序和键盘焦点修复

### <a name="fixes"></a>修复项
* 过去，如果尝试下载具有无效 Windows 文件名的 blob，则操作将失败。 现在，存储资源管理器将检测 blob 名称是否有效，并且会询问你是要对其进行编码还是跳过该 blob。 存储资源管理器还会检测文件名看起来是否已编码并且在上传前会询问是否要对其进行解码。
* 在上传 blob 期间，目标 blob 容器的编辑器有时候不能正确刷新。 此问题已解决。
* 支持多种形式的连接字符串和回归的 SAS URI。 我们已解决了所有已知问题，但是如果遇到了进一步的问题，请向我们发送反馈。
* 在 0.9.0 版中，对于某些用户，更新通知损坏。 此问题已修复，对于受此 bug 影响的那些用户，可以手动[从此处](https://azure.microsoft.com/en-us/features/storage-explorer/)下载最新版本的存储资源管理器。

### <a name="known-issues"></a>已知问题
* 存储资源管理器不支持 ADFS 帐户。
* “查看资源管理器”和“查看帐户管理”的快捷键应当分别为 Ctrl/Cmd+Shift+E 和 Ctrl/Cmd+Shift+A。
* 当以 Azure Stack 为目标时，将某些文件作为追加 blob 进行上传可能会失败。
* 对任务单击“取消”后，可能需要一段时间才能取消该任务。 这是因为我们使用的是此处介绍的“取消筛选”解决办法。
* 如果选择错误的 PIN/智能卡证书，需要重启存储资源管理器使其忘记该选择。
* 帐户设置面板可能显示需重新输入凭据以筛选订阅。
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据。
* 尽管 Azure Stack 当前不支持文件共享，但附加 Azure Stack 存储帐户下仍会显示“文件共享”节点。
* 存储资源管理器使用的 Electron shell 在进行某项 GPU（图形处理单元）硬件加速时出现问题。 如果存储资源管理器显示了一个空白（空的）主窗口，则可以尝试从命令行启动存储资源管理器，并通过添加 `--disable-gpu` 开关禁用 GPU 加速。
```
./StorageExplorer --disable-gpu
```
* 对于 Ubuntu 14.04 用户，需确保 GCC 是最新版本 - 为此，可运行以下命令并重启计算机：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 对于 Ubuntu 17.04 用户，需要安装 GConf - 通过运行以下命令，然后重启计算机即可完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>版本 0.8.16
8/21/2017

### <a name="new"></a>新建
* 打开 Blob 时，如果检测到更改，存储资源管理器会提示上传已下载的文件
* 增强的 Azure Stack 登录体验
* 改善了同时上传/下载多个小型文件时的性能


### <a name="fixes"></a>修复项
* 对于某些 Blob 类型，在发生上传冲突期间选择“替换”有时会导致重新开始上传。
* 在版本 0.8.15 中，上传有时会停滞在 99%。
* 将文件上传到文件共享时，如果选择上传到某个不存在的目录，上传将会失败。
* 存储资源管理器为共享访问签名和表查询生成错误的时间戳。


### <a name="known-issues"></a>已知问题
* 目前无法正常使用名称和密钥连接字符串。 下一个版本会修复此问题。 在此之前，可以使用附加名称和密钥的功能。
* 如果尝试打开具有无效 Windows 文件名的文件，下载操作会导致“找不到文件”错误。
* 对任务单击“取消”后，可能需要一段时间才能取消该任务。 这是 Azure 存储节点库的局限性。
* 完成 blob 上传后，系统会刷新启动上传的选项卡。 这是对以前行为的更改，也将导致回转到所在容器的根目录中。
* 如果选择错误的 PIN/智能卡证书，需要重启存储资源管理器使其忘记该选择。
* 帐户设置面板可能显示需重新输入凭据以筛选订阅。
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据。
* 尽管 Azure Stack 当前不支持文件共享，但附加 Azure Stack 存储帐户下仍会显示“文件共享”节点。
* 对于 Ubuntu 14.04 用户，需确保 GCC 是最新版本 - 为此，可运行以下命令并重启计算机：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 对于 Ubuntu 17.04 用户，需要安装 GConf - 通过运行以下命令，然后重启计算机即可完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>版本 0.8.14
2017/06/22

### <a name="new"></a>新建

* 已将 Electron 版本更新为 1.7.2，以便利用若干关键安全更新
* 现可从帮助菜单快速访问联机疑难解答指南
* 存储资源管理器疑难解答[指南][2]
* 连接到 Azure Stack 订阅相关[说明][3]

### <a name="known-issues"></a>已知问题

* 用鼠标单击 Linux 中删除文件夹确认对话框上的按钮无反应。 解决方法是使用 Enter 键
* 如果选择错误的 PIN/智能卡证书，需要重启存储资源管理器使其忘记该选择
* 同时上传 3 组以上的 blob 或文件可能导致错误
* 帐户设置面板可能显示需重新输入凭据才能筛选订阅
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据。
* 尽管 Azure Stack 当前不支持文件共享，但附加 Azure Stack 存储帐户下仍会显示“文件共享”节点。
* Ubuntu 14.04 安装需要更新或升级 gcc 版本 - 升级步骤如下：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>版本 0.8.13
05/12/2017

#### <a name="new"></a>新建

* 存储资源管理器疑难解答[指南][2]
* 连接到 Azure Stack 订阅相关[说明][3]

#### <a name="fixes"></a>修复项

* 已修复：文件上传很可能导致内存不足错误
* 已修复：现可使用 PIN/智能卡登录
* 已修复：在门户中打开现适用于 Azure China、Azure Germany、Azure US Government 和 Azure Stack
* 已修复：将文件夹上传到 blob 容器中时，“非法操作”错误时有发生
* 已修复：管理快照时禁用“选择全部”
* 已修复：查看基础 blob 快照的属性后，可能会覆盖其元数据

#### <a name="known-issues"></a>已知问题

* 如果选择错误的 PIN/智能卡证书，需要重启存储资源管理器使其忘记该选择
* 放大或缩小时，缩放级别可能暂时重置为默认级别
* 同时上传 3 组以上的 blob 或文件可能导致错误
* 帐户设置面板可能显示需重新输入凭据才能筛选订阅
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据。
* 尽管 Azure Stack 当前不支持文件共享，但附加 Azure Stack 存储帐户下仍会显示“文件共享”节点。
* Ubuntu 14.04 安装需要更新或升级 gcc 版本 - 升级步骤如下：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812--0811--0810"></a>版本 0.8.12/0.8.11/0.8.10
2017/04/07

#### <a name="new"></a>新建

* 如今，如果从更新通知安装更新，存储资源管理器会自动关闭
* 对使用经常访问的资源而言，就地快速访问提供增强的体验
* 在 Blob 容器编辑器中，现可查看租用 blob 所属的虚拟机
* 现可折叠左侧面板
* 发现现与下载同时运行
* 在 Blob 容器、文件共享和表编辑器中使用统计信息来查看资源或所选内容的大小
* 现可登录到基于 Azure Active Directory (AAD) 的 Azure Stack 帐户。
* 现可将超过 32 MB 的存档文件上传到高级存储帐户
* 改进的辅助功能支持
* 现可通过转到“编辑”-&gt;“SSL 证书”-&gt;“导入证书”来添加受信任的 Base-64 编码 X.509 SSL 证书

#### <a name="fixes"></a>修复项

* 已修复：刷新帐户凭据后，树状视图有时不会自动刷新
* 已修复：为仿真器队列和表生成 SAS 会导致无效的 URL
* 已修复：现可在启用代理时扩展高级存储帐户
* 已修复：如果选择了 1 个或 0 个帐户，帐户管理页上的应用按钮无效
* 已修复：上传需要解决冲突的 blob 可能失败 - 已在 0.8.11 中修复
* 已修复：0.8.11 中正在发送的反馈损坏 - 已在 0.8.12 中修复

#### <a name="known-issues"></a>已知问题

* 升级到 0.8.10 后，需要刷新所有凭据。
* 放大或缩小时，缩放级别可能暂时重置为默认级别。
* 同时上传 3 组以上的 blob 或文件可能导致错误。
* 帐户设置面板可能显示需重新输入凭据才能筛选订阅。
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据。
* 尽管 Azure Stack 当前不支持文件共享，但附加 Azure Stack 存储帐户下仍会显示“文件共享”节点。
* Ubuntu 14.04 安装需要更新或升级 gcc 版本 - 升级步骤如下：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089--088"></a>版本 0.8.9/0.8.8
2017/02/23

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>新建

* 存储资源管理器 0.8.9 自动下载最新版本进行更新。
* 修补程序：使用门户生成的 SAS URI 附加存储帐户会导致错误。
* 现可创建、管理和提升 blob 快照。
* 现可登录 Azure China、Azure Germany、Azure US Government 帐户。
* 现可更改缩放级别。 使用“视图”菜单中的选项放大、缩小和重置缩放。
* 现支持在 blob 和文件的用户元数据中使用 Unicode 字符。
* 辅助功能改进。
* 可从更新通知中查看下一版本的发行说明。 还可从“帮助”菜单查看当前发行说明。

#### <a name="fixes"></a>修复项

* 已修复：版本号现正确显示在 Windows“控制面板”中
* 已修复：搜索不再限制为 50,000 个节点
* 已修复：如果目标目录不存在，上传到文件共享始终处于旋转状态
* 已修复：提升了长时间上传和下载的稳定性

#### <a name="known-issues"></a>已知问题

* 放大或缩小时，缩放级别可能暂时重置为默认级别。
* 快速访问仅适用于基于订阅的项。 此版本不支持本地资源或通过密钥或 SAS 令牌附加的资源。
* 快速访问可能需要几秒钟时间导航至目标资源，具体取决于所拥有的资源数。
* 同时上传 3 组以上的 blob 或文件可能导致错误。

2016/12/16
### <a name="version-087"></a>版本 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>新建

* 在“活动”窗口中，可选择如何在更新、下载或复制会话开始时解决冲突
* 将鼠标悬停在选项卡上，以查看存储资源的完整路径
* 单击选项卡时，会在左侧导航窗格中同步其位置

#### <a name="fixes"></a>修复项

* 已修复：存储资源管理器在 Mac 上现已是受信任的应用
* 已修复：再次支持 Ubuntu 14.04
* 已修复：加载订阅时“添加帐户”UI 有时会闪烁
* 已修复：左侧导航窗格中有时不会列出所有的存储资源
* 已修复：操作窗格有时显示空白操作
* 已修复：现在会保留最后关闭的会话窗口大小
* 已修复：现可使用上下文菜单打开同一资源的多个选项卡

#### <a name="known-issues"></a>已知问题

* 快速访问仅适用于基于订阅的项。 此版本不支持本地资源或通过密钥或 SAS 令牌附加的资源
* 快速访问可能需要几秒钟时间导航至目标资源，具体取决于所拥有的资源数
* 同时上传 3 组以上的 blob 或文件可能导致错误
* 搜索功能会处理作用范围约 50,000 个节点的搜索操作，之后，如超过此范围，则性能可能会受影响，或导致未经处理的异常
* 第一次在 macOS 上使用存储资源管理器时，可能会出现多个请求用户提供密钥链访问权限的提示。 建议选择“始终允许”，使提示不再显示

2016/11/18
### <a name="version-086"></a>版本 0.8.6

#### <a name="new"></a>新建

* 现可将最常用服务固定到快速访问，从而实现轻松导航
* 现可在不同选项卡中打开多个编辑器。 单击可打开临时选项卡；双击可打开永久选项卡。也可通过单击临时选项卡，将其变为永久选项卡
* 已显著提升上传和下载的性能及稳定性，尤其是对于快速计算机上的大文件
* 现可在 blob 容器中创建空白“虚拟”文件夹
* 已重新引入通过新的增强子字符串搜索进行的限定范围搜索，因此现可通过两种方式进行搜索：
    * 全局搜索 - 在搜索文本框中输入搜索词即可
    * 限定范围的搜索 - 单击节点旁边的放大镜图标，然后在路径末尾添加一个搜索词，或右键单击并选择“从此处搜索”
* 我们已添加多个主题：浅色（默认）、深色、高对比度黑色和高对比度白色。 转到“编辑”-&gt;“主题”可更改主题首选项
* 可修改 Blob 和文件属性
* 现在支持编码 (base64) 和未编码的队列消息
* 对于 Linux，现要求 64 位操作系统。 对于此版本，仅支持 64 位 Ubuntu 16.04.1 LTS
* 已更新徽标！

#### <a name="fixes"></a>修复项

* 已修复：屏幕死机问题
* 已修复：安全性已增强
* 已修复：有时可能会出现重复的附加帐户
* 已修复：具有未定义内容类型的 blob 可能产生异常
* 已修复：不能在空表上打开“查询面板”
* 已修复：“搜索”中的各种 bug
* 已修复：单击“加载更多”时，加载的资源数从 50 增加到了 100
* 已修复：首次运行时，如已登录帐户，现默认选择该帐户的所有订阅

#### <a name="known-issues"></a>已知问题

* 此版本的存储资源管理器不在 Ubuntu 14.04 上运行
* 若要为同一资源打开多个选项卡，请勿连续单击同一资源。 单击另一资源，然后返回，然后单击原始资源以在另一选项卡中再次打开
* 快速访问仅适用于基于订阅的项。 此版本不支持本地资源或通过密钥或 SAS 令牌附加的资源
* 快速访问可能需要几秒钟时间导航至目标资源，具体取决于所拥有的资源数
* 同时上传 3 组以上的 blob 或文件可能导致错误
* 搜索功能会处理作用范围约 50,000 个节点的搜索操作，之后，如超过此范围，则性能可能会受影响，或导致未经处理的异常

2016/10/03
### <a name="version-085"></a>版本 0.8.5

#### <a name="new"></a>新建

* 现可使用门户生成的 SAS 密钥附加到存储帐户和资源

#### <a name="fixes"></a>修复项

* 已修复：搜索期间的争用条件有时导致节点无法展开
* 已修复：使用帐户名和密钥连接到存储帐户时，“使用 HTTP”无效
* 已修复：SAS 密钥（特别是门户生成的密钥）返回“尾部反斜杠”错误
* 已修复：表格导入问题
    * 有时分区键和行键会反转
    * 无法读取“null”分区键

#### <a name="known-issues"></a>已知问题

* 搜索功能会处理作用范围约 50,000 个节点的搜索操作，之后，如超过此范围，则性能可能会受影响
* Azure Stack 目前不支持文件，因此尝试展开“文件”会显示错误

2016/09/12
### <a name="version-084"></a>版本 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>新建

* 生成指向存储帐户、容器、队列、表或文件共享的直接链接，以便共享和轻松访问资源 - Windows 和 Mac 操作系统支持
* 从搜索框搜索 blob 容器、表、队列、文件共享或存储帐户
* 现可在表查询生成器中对子句进行分组
* 从 SAS 附加帐户和容器中重命名并复制/粘贴 blob 容器、文件共享、表、blob、blob 文件夹、文件和目录
* 重命名和复制 blob 容器和文件共享现可保留属性和元数据

#### <a name="fixes"></a>修复项

* 已修复：表实体包含布尔或二进制属性时无法编辑

#### <a name="known-issues"></a>已知问题

* 搜索功能会处理作用范围约 50,000 个节点的搜索操作，之后，如超过此范围，则性能可能会受影响

2016/08/03
### <a name="version-083"></a>版本 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>新建

* 重命名容器、表和文件共享
* 提升了查询生成器体验
* 能够保存和加载查询
* 存储帐户或容器、队列、表或文件共享的直接链接，以便共享和轻松访问资源（即将推出仅 Windows 和 macOS 支持！）
* 能够管理和配置 CORS 规则

#### <a name="fixes"></a>修复项

* 已修复：Microsoft 帐户每 8-12 小时需要重新进行身份验证

#### <a name="known-issues"></a>已知问题

* 有时 UI 可能出现冻结 - 窗口最大化可帮助解决此问题
* macOS 安装可能需要提升的权限
* 帐户设置面板可能显示需重新输入凭据才能筛选订阅
* 重命名文件共享、blob 容器和表不会保留容器中的元数据或其他属性，例如文件共享配额、公共访问级别或访问策略
* 重命名 blob（单独地或在已重命名的 blob 容器中）不保留快照。 重命名期间保留 blob、文件和实体的所有其他属性和元数据
* 在 SAS 附加帐户中，复制或重命名资源无法正常工作

2016/07/07
### <a name="version-082"></a>版本 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>新建

* 存储帐户按订阅分组；通过密钥或 SAS 附加的开发存储和资源显示在（本地和附加）节点下
* 从“Azure 帐户设置”面板注销帐户
* 配置代理设置以启用和管理登录
* 创建和中断 blob 租用
* 通过单击打开 blob 容器、队列、表和文件

#### <a name="fixes"></a>修复项

* 已修复：使用 .NET 或 Java 库插入的队列消息未从 base64 正确解码
* 已修复：不向 Blob 存储帐户显示 $metrics 表
* 已修复：表节点不适用于本地（开发）存储

#### <a name="known-issues"></a>已知问题

* macOS 安装可能需要提升的权限

2016/06/15
### <a name="version-080"></a>版本 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>新建

* 文件共享支持：查看、上传、下载、复制文件和目录以及 SAS URI（创建和连接）
* 改进了使用 SAS URI 或帐户密钥连接到存储的用户体验
* 导出表查询结果
* 表列重新排序和自定义
* 查看含已启用指标的存储帐户 $logs blob 容器和 $metrics 表
* 改进了导出和导入行为，现包括属性值类型

#### <a name="fixes"></a>修复项

* 已修复：上传或下载大型 blob 可能导致上传/下载不完整
* 已修复：编辑、添加或导入具有数值字符串值（“1”）的实体会将其转换为双精度
* 已修复：无法在本地开发环境中展开表节点

#### <a name="known-issues"></a>已知问题

* Blob 存储帐户不可查看 $metrics 表
* 如果使用 Base64 编码对消息编码，则以编程方式添加的队列消息可能无法正确显示

2016/05/17
### <a name="version-07201605090"></a>版本 0.7.20160509.0

#### <a name="new"></a>新建

* 更好的应用崩溃错误处理

#### <a name="fixes"></a>修复项

* 修复了需要登录凭据时信息栏消息有时不显示的 bug

#### <a name="known-issues"></a>已知问题

* 表：添加、编辑或导入具有不明确数值（如“1”或“1.0”）的属性的实体，并且用户尝试将其作为 `Edm.String` 发送，该值将通过客户端 API 作为 Edm.Double 返回

2016/03/31

### <a name="version-07201603250"></a>版本 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>新建

* 表支持：实体的查看、查询、导出、导入和 CRUD 操作
* 队列支持：查看、添加消息，以及取消消息排队
* 生成存储帐户的 SAS URI
* 使用 SAS URI 连接到存储帐户
* 存储资源管理器未来更新的更新通知
* 更新的外观

#### <a name="fixes"></a>修复项

* 性能和可靠性提升

### <a name="known-issues-amp-mitigations"></a>已知问题 &amp; 缓解措施

* 大型 blob 文件无法正常下载 - 建议在我们处理此问题期间使用 AzCopy
* 如果无法找到或无法写入主文件夹，则不会检索或缓存帐户凭据
* 如果正在添加、编辑或导入具有不明确数值（如“1”或“1.0”）的属性的实体，并且用户尝试将其作为 `Edm.String` 发送，则该值将通过客户端 API 作为 Edm.Double 返回
* 导入包含多行记录的 CSV 文件时，数据可能被截或加密

2016/02/03

### <a name="version-07201601291"></a>版本 0.7.20160129.1

#### <a name="fixes"></a>修复项

* 上传、下载和复制 blob 时整体性能提升

2016/01/14

### <a name="version-07201601050"></a>版本 0.7.20160105.0

#### <a name="new"></a>新建

* Linux 支持（OSX 的奇偶校验功能）
* 添加具有共享访问签名 (SAS) 密钥的 blob 容器
* 添加 Azure China 的存储帐户
* 添加具有自定义终结点的存储帐户
* 打开并查看内容文本和图片 blob
* 查看和编辑 blob 属性和元数据

#### <a name="fixes"></a>修复项

* 已修复：上传或下载大量 blob (500+) 有时可能导致应用白屏
* 已修复：设置 blob 容器公共访问级别时，新值在重置容器焦点前不会更新。 此外，对话框始终默认为“无公共访问”，而不是实际的当前值。
* 总体更佳的键盘/辅助功能和 UI 支持
* 痕迹导航历史记录文本在出现较长空格时换行
* SAS 对话框支持输入验证
* 即使用户凭据已过期，本地存储仍然可用
* 删除打开的 blob 容器后，右侧的 blob 资源管理器关闭

#### <a name="known-issues"></a>已知问题

* Linux 安装需要更新或升级 gcc 版本 - 升级步骤如下：
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>版本 0.7.20151116.0

#### <a name="new"></a>新建

* macOS 和 Windows 版本
* 登录以查看存储帐户 - 使用组织帐户、Microsoft 帐户、2FA 等。
* 本地开发存储（使用存储模拟器，仅限 Windows）
* Azure 资源管理器和经典资源支持
* 创建和删除 blob、队列或表
* 搜索特定 blob、队列或表
* 浏览 blob 容器的内容
* 查看和浏览目录
* 上传、下载和删除 blob 和文件夹
* 查看和编辑 blob 属性和元数据
* 生成 SAS 密钥
* 管理和创建存储访问策略 (SAP)
* 按前缀搜索 blob
* 拖放文件进行上传或下载

#### <a name="known-issues"></a>已知问题

* 设置 blob 容器公共访问级别时，新值在重置容器焦点前不会更新
* 打开设置公共访问级别的对话框时，始终显示“无公共访问”作为默认值，而不是实际的当前值
* 无法重命名已下载 blob
* 出现错误且不显示该错误时，活动日志条目有时会“停滞”在正在进行状态
* 尝试上传或下载大量 blob 时，系统有时会崩溃或完全白屏
* 取消复制操作有时无效
* 在创建容器（blob/队列/表）期间，如果输入无效名称并继续创建不同容器类型的另一容器，则不能对新类型设置焦点
* 无法创建新文件夹或重命名文件夹




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
