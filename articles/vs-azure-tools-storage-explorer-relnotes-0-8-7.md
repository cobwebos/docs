---
title: "Microsoft Azure 存储资源管理器 0.8.7（预览版）| Microsoft Docs"
description: "Microsoft Azure 存储资源管理器 0.8.7（预览版）发行说明"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: bf47274a97342253dfd4ebfa9f1029a7c18b81eb
ms.openlocfilehash: fc3620ca19d4824bc8ffb3bbe89ef47c5127b9d1


---
# <a name="microsoft-azure-storage-explorer-087-preview"></a>Microsoft Azure 存储资源管理器 0.8.7（预览版）
## <a name="overview"></a>概述
本文包含 Microsoft Azure 存储资源管理器 0.8.7 预览版的发行说明。

[Microsoft Azure 存储资源管理器（预览版）](./vs-azure-tools-storage-manage-with-storage-explorer.md)是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。

## <a name="azure-storage-explorer-087-preview"></a>Azure 存储资源管理器 0.8.7（预览版）
### <a name="download-azure-storage-explorer-087-preview"></a>下载 Azure 存储资源管理器 0.8.7（预览版）
- [适用于 Windows 的 Azure 存储资源管理器 0.8.7 预览版](https://go.microsoft.com/fwlink/?LinkId=708343)
- [适用于 Mac 的 Azure 存储资源管理器 0.8.7 预览版](https://go.microsoft.com/fwlink/?LinkId=708342)
- [适用于 Linux 的 Azure 存储资源管理器 0.8.7 预览版](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new-updates"></a>新的更新
* 在“活动”窗口中，可选择如何在更新、下载或复制会话开始时解决冲突。
* 将鼠标悬停在选项卡上，以查看存储资源的完整路径。
* 单击选项卡时，会在左侧导航窗格中同步其位置。

### <a name="fixes"></a>修复项
* 已修复：存储资源管理器在 macOS 上现已是受信任的应用。
* 已修复：再次支持 Ubuntu 14.04。
* 已修复：加载订阅时“添加帐户”UI 有时会闪烁。
* 已修复：左侧导航窗格中有时不会列出所有的存储资源。
* 已修复：操作窗格有时显示空白操作。
* 已修复：现在会保留最后关闭的会话的窗口大小。
* 已修复：现可使用上下文菜单打开同一资源的多个选项卡。

### <a name="known-issues"></a>已知问题
* 快速访问仅适用于基于订阅的项。 此版本不支持本地资源或通过密钥或 SAS 令牌附加的资源。
* 快速访问可能需要几秒钟时间导航至目标资源，具体取决于所拥有的资源数。
* 同时上传&3; 组以上的 blob 或文件可能导致错误。
* 搜索功能会处理作用范围约 50,000 个节点的搜索操作，之后，如超过此范围，则性能可能会受影响，或导致未经处理的异常。
* 第一次在 macOS 上使用存储资源管理器时，可能会出现多个请求密钥链用户访问权限的提示。 建议选择“始终允许”，以消除提示

## <a name="previous-releases"></a>以前的版本
### <a name="features"></a>功能
#### <a name="main-features"></a>主要功能
* macOS、Linux 和 Windows 版本
* 登录以按订阅分组查看存储帐户：
    * 使用组织帐户、Microsoft 帐户、2FA 等。
    * 配置和管理代理设置
    * 通过注销删除帐户
* 存储帐户连接方式：
    * 帐户名和密钥
    * 自定义终结点（包括 Azure 中国）
    * 存储帐户的 SAS URI
* Azure Resource Manager 和经典存储支持
* 生成 blob、blob 容器、队列、表或文件共享的 SAS 密钥
* 使用共享访问签名 (SAS) 密钥连接到 blob 容器、队列、表或文件共享
* 管理 blob 容器、队列、表或文件共享的存储访问策略
* 通过存储模拟器实现的本地开发存储（仅限 Windows）
* 创建和删除 blob 容器、队列或表
* 查看 $logs blob 容器和 $metrics 表
* 搜索特定 blob、队列、表或文件共享
* 直接链接到存储帐户或容器、队列、表或文件共享，以便共享和轻松访问资源
* 重命名 blob 容器、表和文件共享
* 能够管理和配置 CORS 规则
* 轻松复制存储帐户的主密钥和辅助密钥
* MD5 对上传和下载进行数据完整性和一致性检查
* 从搜索框搜索 blob 容器、表、队列、文件共享或存储帐户
* 现可将最常用服务固定到快速访问，从而实现轻松导航
* 现可在不同选项卡中打开多个编辑器。 单击可打开临时选项卡；双击可打开永久选项卡。 也可通过单击临时选项卡，将其变为永久选项卡
* 上传和下载性能及稳定性显著提升，尤其是对于快速计算机上的大文件
* 我们将重新引入增强的限定范围搜索，并添加了范围概念。 通过悬停图标进入节点路径，右键单击“从此处搜索”，或者手动限定节点范围。 限定范围后，可向路径末尾添加搜索词，从该节点开始进行深度搜索
* 我们已添加多个主题：浅色（默认）、深色、高对比度黑色和高对比度白色。
* 转到“编辑”->“主题”可更改主题选项
* 对于 Linux，现要求 64 位操作系统
* 已更新徽标！
#### <a name="blobs"></a>Blob
* 查看 blob 和浏览目录
* 上传、下载、删除和复制 blob 和文件夹
* 打开并查看内容文本和图片 blob
* 查看和编辑 blob 属性和元数据
* 按前缀搜索 blob
* 创建和中断 blob 和 blob 容器的租约
* 拖放文件进行上传
* 重命名 blob 和文件夹
* 现可在 blob 容器中创建空白“虚拟”文件夹
* 可修改 Blob 和文件属性
#### <a name="tables"></a>表
* 通过 ODATA 查看和查询实体，或使用查询生成器创建复杂查询
* 添加、编辑和删除实体
* 以 CSV 格式导入和导出表内容（包括导出查询结果）
* 自定义列顺序
* 可保存查询
#### <a name="queues"></a>队列
* 查看最近 32 条消息
* 添加消息、取消排队（消息）、查看消息
* 清除队列
* 用户可决定是否要对队列消息进行编码/解码
#### <a name="file-shares"></a>文件共享
* 查看文件和浏览目录
* 上传、下载、删除和复制文件和目录
* 查看文件属性
* 重命名文件和目录

### <a name="bug-fixes"></a>Bug 修复
* 已修复：屏幕死机问题
* 已修复：安全性已增强

### <a name="known-issues"></a>已知问题
* 搜索功能会处理作用范围约 50,000 个节点的搜索操作，之后，如超过此范围，则性能可能会受影响，macOS 安装可能要求提升的权限
* 帐户设置面板可能显示需重新输入凭据以筛选订阅
* 重命名 blob（单独地或在已重命名的 blob 容器中）不会保留快照。 重命名期间会保留 blob、文件和实体的所有其他属性和元数据
* Azure Stack 目前不支持文件，因此尝试展开“文件”节点会导致错误
* Linux 14.04 安装需要更新或升级 gcc 版本。 以下步骤演示如何升级：

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

### <a name="previous-versions"></a>以前的版本
#### <a name="october-2016-release-version-085"></a>2016 年 10 月版本（版本 0.8.5）
* [适用于 Windows 的下载项](https://go.microsoft.com/fwlink/?LinkId=809306)
* [适用于 Mac 的下载项](https://go.microsoft.com/fwlink/?LinkId=809307)
* [适用于 Linux 的下载项](https://go.microsoft.com/fwlink/?LinkId=809308)



<!--HONumber=Jan17_HO3-->


