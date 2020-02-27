---
title: 如何在 Azure SAP HANA（大型实例）上安装 HANA | Microsoft Docs
description: 如何在 Azure SAP HANA（大型实例）上安装 HANA。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617215"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>在 Azure SAP HANA（大型实例）上安装 HANA

若要在 Azure SAP HANA（大型实例）上安装 HANA，首先必须做好以下准备工作：
- 向 Microsoft 提供要在 SAP HANA 大型实例上部署的所有数据。
- 从 Microsoft 接收 SAP HANA 大型实例。
- 创建连接到本地网络的 Azure 虚拟网络。
- 将 HANA 大型实例的 ExpressRoute 线路与同一 Azure 虚拟网络相连。
- 安装用作 HANA 大型实例跳转盒的 Azure 虚拟机。
- 确保可以从跳转盒连接到 HANA 大型实例单元，反之亦然。
- 检查是否安装了所有必要的包和修补程序。
- 阅读有关在要使用的操作系统上安装 HANA 的 SAP 说明和文档。 确保操作系统版本支持所选的 HANA 版本。

下一部分通过一个示例演示如何将 HANA 安装包下载到跳转盒虚拟机。 在本例中，操作系统是 Windows。

## <a name="download-the-sap-hana-installation-bits"></a>下载 SAP HANA 安装软件
HANA 大型实例单元不会直接连接 Internet。 无法直接将安装包从 SAP 下载到 HANA 大型实例虚拟机。 应该将包下载到跳转盒虚拟机。

需要以 SAP S 用户或其他用户的身份访问 SAP市场。

1. 登录并转到 [SAP 服务市场](https://support.sap.com/en/index.html)。 选择“下载软件” **“安装和升级”** “按字母顺序排列的索引”。 >  >  然后选择“H 下 - SAP HANA 平台版本” **“SAP HANA 平台版 2.0”** “安装”。 >  >  下载以下屏幕截图中所示的文件。

   ![要下载的文件屏幕截图](./media/hana-installation/image16_download_hana.PNG)

2. 在本示例中，我们下载了 SAP HANA 2.0 安装包。 在 Azure 跳转盒虚拟机中，将自解压存档展开到目录中，如下所示。

   ![自解压存档的屏幕截图](./media/hana-installation/image17_extract_hana.PNG)

3. 解压缩存档后，复制解压缩程序创建的目录（在本例中为 51052030）。 将该目录从 HANA 大型实例单元的 /hana/shared 卷复制到创建的目录。

   > [!Important]
   > 请勿将安装包复制到根或启动 LUN 中，这是因为空间有限，并且其他进程也需要使用安装包。


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>在 HANA 大型实例单元上安装 SAP HANA
若要安装 SAP HANA，请以 root 用户身份登录。 只有当根拥有足够权限时，才能安装 SAP HANA。 设置对复制到 /hana/shared 的目录的权限。

```
chmod –R 744 <Installation bits folder>
```

若要使用图形用户界面安装程序安装 SAP HANA，需要在 HANA 大型实例上安装 gtk2 包。 若要检查是否已安装此包，请运行以下命令：

```
rpm –qa | grep gtk2
```

（在后续步骤中，我们将演示如何使用图形用户界面安装 SAP HANA。）

转到安装目录，并导航到子目录 HDB_LCM_LINUX_X86_64。 

从该目录中启动：

```
./hdblcmgui 
```
此时，会依次打开一系列屏幕，可在其中提供安装数据。 在本示例中，我们将安装 SAP HANA 数据库服务器和 SAP HANA 客户端组件。 因此，我们选择了“SAP HANA 数据库”。

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中已选择“SAP HANA 数据库”](./media/hana-installation/image18_hana_selection.PNG)

在下一个屏幕中，选择“安装新系统”。

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中已选择“安装新系统”](./media/hana-installation/image19_select_new.PNG)

接下来，在可以安装的几个附加组件之间进行选择。

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了附加组件的列表](./media/hana-installation/image20_select_components.PNG)

此处，我们选择了“SAP HANA 客户端”和“SAP HANA Studio”。 我们还会安装一个纵向扩展实例。 然后选择“单主机系统”。 

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中已选择“单主机系统”](./media/hana-installation/image21_single_host.PNG)

接下来提供一些数据。

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了要定义的系统属性字段](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> 提供的 HANA 系统 ID (SID) 必须与在订购 HANA 大型实例部署时向 Microsoft 提供的数据一致。 选择不同的 SID 会导致安装失败，原因是不同卷带来的访问权限问题。

对于安装路径，请使用 /hana/shared 目录。 在下一步骤中，提供 HANA 数据文件和 HANA 日志文件的位置。


![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了数据和日志区域字段](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> 定义系统属性（前两个屏幕）时指定的 SID 应与装入点的 SID 相匹配。 如果不匹配，请返回并将 SID 调整为装入点上的值。

在下一步中，检查主机名并进行最终更正。 

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了主机名](./media/hana-installation/image24_review_host_name.PNG)

在下一步中，还需要检索在订购 HANA 大型实例部署时向 Microsoft 提供的数据。 

![“SAP HANA 生命周期管理”的屏幕截图，其中显示了要定义的系统管理员字段](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> 提供你在订购单元部署时向 Microsoft 提供的相同“系统管理员用户 ID”和“用户组 ID”。 否则，无法在 HANA 大型实例单元上安装 SAP HANA。

此处未显示接下来的两个屏幕。 在这两个屏幕中可以提供 SAP HANA 数据库的 SYSTEM 用户密码，以及 sapadm 用户密码。 后一个密码用于作为 SAP HANA 数据库实例的一部分安装的 SAP 主机代理。

定义密码后，会看到确认屏幕。 检查列出的所有数据，并继续安装。 此时，系统会显示安装进度屏幕，如下所示：

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了安装进度指示器](./media/hana-installation/image27_show_progress.PNG)

安装完成后，应会看到如下所示的屏幕：

![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中指示安装已完成](./media/hana-installation/image28_install_finished.PNG)

现在，SAP HANA 实例应已正常运行，并可供使用。 应该能够从 SAP HANA Studio 连接到它。 另请确保检查并应用最新的更新。


## <a name="next-steps"></a>后续步骤

- [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)

