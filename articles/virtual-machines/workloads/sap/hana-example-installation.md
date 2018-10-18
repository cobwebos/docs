---
title: 如何在 Azure SAP HANA（大型实例）上安装 HANA | Microsoft Docs
description: 如何在 Azure SAP HANA（大型实例）上安装 HANA。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167589"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>HANA 大型实例上的 SAP HANA 安装示例

此部分介绍了如何在 HANA 大型实例单元上安装 SAP HANA。 初始状态如下：

- 向 Microsoft 提供了部署 SAP HANA 大型实例所需的全部数据。
- 从 Microsoft 收到了 SAP HANA 大型实例。
- 创建了连接到本地网络的 Azure VNet。
- 将 HANA 大型实例的 ExpressRotue 线路与同一 Azure VNet 相连。
- 安装了用作 HANA 大型实例的跳转盒的 Azure VM。
- 确保可以从跳转盒连接到 HANA 大型实例单元，反之亦然。
- 已检查是否安装了所有必要的程序包和修补程序。
- 阅读了有关在要使用的 OS 上安装 HANA 的 SAP 说明和文档，并确保 OS 版本支持选择的 HANA 版本。

接下来需要完成的步骤是，将 HANA 安装程序包下载到跳转盒 VM（在此示例中，是在 Windows OS 上运行），将程序包复制到 HANA 大型实例单元，并执行安装程序。

## <a name="download-of-the-sap-hana-installation-bits"></a>下载 SAP HANA 安装位包
由于 HANA 大型实例单元不直接连接 Internet，因此无法直接将安装程序包从 SAP 下载到 HANA 大型实例 VM。 若要解决不直接连接 Internet 的问题，需要使用跳转盒。 将程序包下载到跳转盒 VM。

若要下载 HANA 安装程序包，需要以 SAP S 用户或其他用户身份下载，以便可以访问 SAP市场。 登录后，请依次转到下列屏幕：

转到 [SAP Service Marketplace](https://support.sap.com/en/index.html) &gt; 单击“下载软件”&gt;“安装和升级”&gt; 使用按字母顺序排列的索引 &gt; 在“H”下选择“SAP HANA 平台版本”&gt;“SAP HANA 平台版 2.0”&gt;“安装”&gt; 下载以下文件

![下载 HANA 安装程序包](./media/hana-installation/image16_download_hana.PNG)

在此示例中，我们下载了 SAP HANA 2.0 安装程序包。 在 Azure 跳转盒 VM 中，将自解压存档展开到目录中，如下所示。

![解压缩 HANA 安装程序包](./media/hana-installation/image17_extract_hana.PNG)

解压缩存档期间，将解压缩创建的目录（在此示例中为 51052030）复制到 HANA 大型实例单元的 /hana/shared 卷中的已创建目录。

> [!Important]
> 请勿将安装程序包复制到根或启动 LUN 中，这是因为空间有限，并且其他进程也需要使用安装程序包。


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>在 HANA 大型实例单元上安装 SAP HANA
若要安装 SAP HANA，需要以用户根身份登录。 只有当根拥有足够权限时，才能安装 SAP HANA。
首先，需要设置对复制到 /hana/shared 的目录的权限。 需要设置如下权限

```
chmod –R 744 <Installation bits folder>
```

若要使用图形安装程序安装 SAP HANA，需要在 HANA 大型实例上安装 gtk2 包。 运行以下命令，检查是否已安装此包

```
rpm –qa | grep gtk2
```

在后续步骤中，我们将展示 SAP HANA 安装程序的图形用户界面。 接下来，依次转到安装目录和子目录 HDB_LCM_LINUX_X86_64。 开始

```
./hdblcmgui 
```
从此目录入手。 现在将通过一系列屏幕逐步介绍如何安装，需要在这些屏幕中提供安装数据。 在此示例中，我们将安装 SAP HANA 数据库服务器和 SAP HANA 客户端组件。 因此，我们选择的是“SAP HANA 数据库”，如下所示

![选择安装 HANA](./media/hana-installation/image18_hana_selection.PNG)

在下一屏幕中，选择“安装新系统”选项

![选择新安装 HANA](./media/hana-installation/image19_select_new.PNG)

执行这一步后，需要在可以另外安装到 SAP HANA 数据库服务器的其他几个组件之间进行选择。

![选择其他 HANA 组件](./media/hana-installation/image20_select_components.PNG)

在本文档中，我们选择了 SAP HANA 客户端和 SAP HANA Studio。 我们还安装了纵向扩展实例。 因此，在下一屏幕中，需要选中“单主机系统” 

![选择安装纵向扩展实例](./media/hana-installation/image21_single_host.PNG)

在下一屏幕中，需要提供一些数据

![提供 SAP HANA SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> 提供的 HANA 系统 ID (SID) 必须与在订购 HANA 大型实例部署时向 Microsoft 提供的数据一致。 选择不同的 SID 会导致安装失败，原因是不同卷带来的访问权限问题

使用 /hana/shared 目录作为安装目录。 在下一步中，需要提供 HANA 数据文件和 HANA 日志文件的位置


![提供 HANA 日志位置](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> 应将装入点（其中包含在此屏幕的上一选择屏幕中选择的 SID）随附的卷定义为数据和日志文件。 如果 SID 与在前面的屏幕中键入的内容不一致，请返回并将 SID 调整为装入点上的值。

在下一步中，检查主机名并进行最终更正。 

![检查主机名](./media/hana-installation/image24_review_host_name.PNG)

在下一步中，还需要检索在订购 HANA 大型实例部署时向 Microsoft 提供的数据。 

![提供系统用户 UID 和 GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> 提供的系统用户 ID 和用户组 ID 必须与在订购单元部署时向 Microsoft 提供的数据一致。 如果提供的 ID 不完全相同，将无法在 HANA 大型实例单元上安装 SAP HANA。

在接下来的两个屏幕（本文档未显示）中，需要提供 SAP HANA 数据库的系统用户密码和 sapadm 用户密码（用于作为 SAP HANA 数据库实例的一部分安装的 SAP 主机代理）。

定义密码后，便会看到确认屏幕。 检查列出的所有数据，并继续安装。 此时，系统会显示安装进度屏幕，如下所示

![查看安装进度](./media/hana-installation/image27_show_progress.PNG)

安装完成后，应该会看到下图

![安装完成](./media/hana-installation/image28_install_finished.PNG)

此时，SAP HANA 实例应该已创建完成，可以开始使用了。 应该能够从 SAP HANA Studio 连接到它。 此外，还请务必检查最新 SAP HANA 修补程序，并应用这些修补程序。


**后续步骤**

- 请参阅 [Azure 上的 SAP HANA 大型实例高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。

