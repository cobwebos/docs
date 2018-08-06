---
title: include 文件
description: include 文件
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: a83095b8330ccf08d777e48389c17058c6d29527
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347668"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>通过 Azure 门户安装更新

1. 转到 StorSimple 设备管理器并选择“设备”。 从连接到服务的设备列表中，选择并单击要更新的设备。

2. 在“设置”下，单击“设备更新”。  

3. 如果有可用的软件更新，会显示一条消息。 若要检查更新，还可以单击“扫描”。 记下运行的软件版本。 

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    在扫描开始和成功完成时，系统会发出通知。
 
4. 在扫描更新后，单击“下载更新”。 在“新的更新”下，查看发行说明。 另请注意，在下载更新之后，需确认安装。 单击“确定”。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    在上传开始和成功完成时，系统会发出通知。

5. 在“设备更新”下，单击“安装”。

     ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. 在“新的更新”下，会警告你该更新会造成中断。 因为虚拟阵列是单节点设备，所以设备在更新后会重新启动。 这会中断正在进行的任何 IO。 单击“确定”以安装更新。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    当安装作业开始时，系统会发出通知。

7.  在安装作业成功完成后，单击“查看作业”链接。 此操作将转到“安装更新”边栏选项卡。 可以在此处查看关于该作业的详细信息。 

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. 如果开始使用的是运行软件版本 Update 1 (10.0.10296.0) 的虚拟阵列，则现在运行的是 Update 1.1 且已完成操作。 可以跳过其余步骤。 

    如果开始使用的是运行软件版本 Update 0.6 (10.0.10293.0) 的虚拟阵列，则现已更新到 1.0。 将再显示一条消息，指出更新可用。 重复步骤 4-7 来安装 Update 1.1。

    

