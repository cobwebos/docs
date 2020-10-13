---
title: 在 Azure Data Box Gateway 系列设备上安装更新 |Microsoft Docs
description: 描述如何使用 Azure Data Box Gateway 系列设备的 Azure 门户和本地 web UI 应用更新
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: alkohli
ms.openlocfilehash: 337c0c4434eb768ee45429d9b2d23536db4c3fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575446"
---
# <a name="update-your-azure-data-box-gateway"></a>更新 Azure Data Box Gateway

本文介绍通过本地 web UI 和 Azure 门户在 Azure Data Box Gateway 上安装更新所需的步骤。 应用软件更新或修补程序，使 Data Box Gateway 设备保持最新状态。

> [!IMPORTANT]
>
> - 更新 **1911** 对应于设备上的 **1.6.1049.786** software 版本。 有关此更新的信息，请参阅 [发行说明](data-box-gateway-1911-release-notes.md)。
>
> - 请记住，安装更新或修补程序会重新启动设备。 假设 Data Box Gateway 是一个单节点设备，任何正在进行的 I/O 都会被中断，并且你的设备将经历长达 30 分钟的设备软件更新停机时间。

后续部分将介绍其中的每个步骤。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

建议通过 Azure 门户安装更新。 设备每天自动扫描一次更新。 更新可用后，门户中会显示一条通知。 然后，你可以下载和安装更新。

> [!NOTE]
> 在继续安装更新之前，请确保设备运行正常并且状态显示为 " **联机** "。

1. 当更新可用于你的设备时，你会看到一条通知。 选择通知，或从顶部命令栏中选择 " **更新设备**"。 这将允许你应用设备软件更新。

    ![Azure Stack 边缘 Data Box Gateway 主页的屏幕截图，其中包含 "概述" 和 "更新" 设备选项。](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. 在 " **设备更新** " 边栏选项卡中，检查是否已查看与发行说明中的新功能关联的许可条款。

    您可以选择 **下载和安装更新，** 或仅 **下载** 更新。 然后可以选择以后安装这些更新。

    !["设备更新" 对话框的屏幕截图，其中包含 "接受和理解" 选项以及 "下载并安装" 选项。](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    如果要下载和安装更新，请选中 "下载完成后自动安装更新" 选项。

    !["设备更新" 对话框的屏幕截图，其中包含 Accept 选项和称为的下载选项。](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. 更新的下载开始。 你会看到一条通知，指出下载正在进行中。

    ![显示 "下载并安装更新" 的通知屏幕截图。](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    通知横幅也会显示在 Azure 门户中。 这表明下载进度。

    ![Azure Stack 边缘 Data Box Gateway 主页的屏幕截图，其中包含概述选项和下载更新通知横幅（称为 out）。](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    你可以选择此通知或选择 " **更新设备** " 来查看更新的详细状态。

    !["下载并安装更新" 对话框的屏幕截图，状态如下：正在进行的消息和下载更新消息（称为 out）。](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. 下载完成后，通知横幅会更新以指示完成。 如果选择下载并安装更新，则将自动开始安装。

    ![Azure Stack 边缘 Data Box Gateway 主页的屏幕截图，其中包含概述选项和已下载的更新通知横幅（称为 out）。](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    如果选择仅下载更新，则选择该通知即可打开 " **设备更新** " 边栏选项卡。 选择“安装”。
  
    !["设备更新" 对话框的屏幕截图，其中安装选项称为 "out"。](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. 你会看到安装正在进行的通知。

    ![Azure Stack 边缘 Data Box Gateway 主页的屏幕截图，其中包含概述选项和称为 "下载并安装" 的进度消息。](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    该门户还显示信息性警报，指示安装正在进行中。 <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. 由于这是一个1节点设备，因此在安装更新后，设备将重新启动。 重新启动过程中的严重警报将指示设备检测信号丢失。

    ![Azure Stack 边缘 Data Box Gateway 主页的屏幕截图，其中包含 "概述" 选项和 "严重警报通知" 横幅。](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    选择警报以查看相应的设备事件。

    ![设备事件部分的屏幕截图，其中包含来自设备事件的丢失检测信号（称为 out）。](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. 安装更新后，设备状态将更新为 " **联机** "。

    ![Azure Stack 边缘 Data Box Gateway 主页的屏幕截图，其中包含 "概述" 选项和 "联机" 状态。](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    在顶部命令栏中，选择 " **设备更新**"。 验证更新是否已成功安装，以及设备软件版本是否反映了此情况。

    !["设备更新" 对话框的屏幕截图，其中包含已安装的软件版本部分。](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>使用本地 Web UI

通过两个步骤使用本地 Web UI：

- 下载更新或修补程序
- 安装更新或修补程序

以下部分详细介绍了这些步骤。

### <a name="download-the-update-or-the-hotfix"></a>下载更新或修补程序

执行以下步骤以下载更新。 你可以从 Microsoft 提供的位置或从 Microsoft 更新目录中下载更新。

执行以下步骤，从 Microsoft 更新目录中下载更新。

1. 启动浏览器并导航到 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) 。

   ![在 "搜索" 文本框中键入 Data Box Gateway 的浏览器窗口中 Microsoft 更新目录的屏幕截图。 浏览器的地址栏和 "搜索" 文本框均称为 "out"。](./media/data-box-gateway-apply-updates/download-update-1.png)

2. 在 Microsoft 更新目录的搜索框中，输入要下载的修补程序的知识库 (KB) 号。 例如，输入 " **Azure Data Box Gateway**"，然后单击 " **搜索**"。

   更新列表显示为 **Azure Data Box Gateway 1911**。

   ![浏览器窗口中 Microsoft 更新目录的屏幕截图，其中显示并调用 Data Box Gateway 搜索结果。](./media/data-box-gateway-apply-updates/download-update-2.png)

3. 选择“下载”  。 有一个与设备软件更新相对应的名为 *SoftwareUpdatePackage.exe* 下载的文件。 将文件下载到本地系统上的文件夹。 也可以将该文件夹复制到可通过设备访问的网络共享。

   ![下载对话框的屏幕截图，其中包含指向的软件更新包点 E X E 文件的链接。](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>安装更新或修补程序

安装更新或修补程序之前，请确保：

- 更新或修补程序已本地下载到主机上，或可通过网络共享访问。
- 你的设备状态为 "正常"，如本地 web UI 的 " **概述** " 页中所示。

   ![Data Box Gateway 本地 web U I 与 "仪表板" 选项和软件状态： "正常" 消息（称为 out）。](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

此过程大约需要20分钟才能完成。 执行以下步骤，安装更新或修补程序。

1. 在本地 web UI 中，请参阅**维护**  >  **软件更新**。 记下运行的软件版本。

   ![Data Box Gateway 本地 web U I 的屏幕截图，其中包含软件更新选项和被称为 out 的当前软件版本消息。](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. 提供更新文件的路径。 如果放置在网络共享上，也可以浏览到更新安装文件。 选择包含 *SoftwareUpdatePackage.exe* 后缀的软件更新文件。

   ![文件资源管理器的屏幕截图，其中包含名为 E X E 的软件更新包。](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. 选择“应用”。 

   ![使用软件更新选项、"更新文件路径" 文本框和 "应用更新" 选项（称为 "应用更新" 选项） Data Box Gateway 本地 web U I 的屏幕截图。](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. 出现确认提示时，请选择 **"是"** 以继续。 如果设备是单节点设备，则在应用更新后，设备将重新启动，并出现停机时间。
   ![突出显示 "是" 选项的 "软件更新" 对话框的屏幕截图。](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. 更新启动。 成功更新设备后，该设备将重新启动。 本地 UI 在此期间不可访问。

6. 重新启动完成后，会转到“登录”**** 页。 若要验证是否已更新设备软件，请在本地 web UI 中，请参阅**维护**  >  **软件更新**。 本示例中显示的软件版本为 **1.6.1049.786**。

   ![Data Box Gateway 本地 web U I 的屏幕截图，其中包含软件更新选项和已更新的当前软件版本消息（称为 out）。](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>后续步骤

了解有关 [管理 Azure Data Box Gateway](data-box-gateway-manage-users.md)的详细信息。
