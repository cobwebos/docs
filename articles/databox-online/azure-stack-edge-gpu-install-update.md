---
title: 在 Azure Stack Edge Pro GPU 设备上安装更新 |Microsoft Docs
description: 描述如何使用 Azure Stack Edge Pro GPU 设备的 Azure 门户和本地 web UI 以及设备上的 Kubernetes 群集应用更新
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: b0377d7b209da76b03a115dc82831eeb00e1ff95
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047074"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>更新 Azure Stack Edge Pro GPU 

本文介绍通过本地 web UI 和 Azure 门户在 Azure Stack Edge Pro 上安装更新所需的步骤。 应用软件更新或修补程序，使 Azure Stack Edge Pro 设备和设备上关联的 Kubernetes 群集保持最新。 

本文中所述的过程是使用不同版本的软件执行的，但该过程对于当前软件版本保持不变。

> [!IMPORTANT]
> - 更新 **2010** 对应于设备上的 **2.1.1377.2170** software 版本。 有关此更新的信息，请参阅 [发行说明](azure-stack-edge-gpu-2009-release-notes.md)。
>
> - 请记住，安装更新或修补程序会重新启动设备。 此更新要求按顺序应用两个更新。 首先应用设备软件更新，然后 Kubernetes 更新。 假设 Azure Stack Edge Pro 是单节点设备，则正在进行的任何 i/o 都将中断，设备软件更新的设备的停机时间最长为30分钟。

若要在设备上安装更新，首先需要配置更新服务器的位置。 配置更新服务器后，可以通过 Azure 门户 UI 或本地 web UI 应用更新。

后续部分将介绍其中的每个步骤。

## <a name="configure-update-server"></a>配置更新服务器

1. 在本地 web UI 中，请参阅**配置**  >  **更新服务器**。 
   
    ![配置更新1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. 在 " **选择更新服务器类型**" 的下拉列表中，选择 "Microsoft 更新 server (默认) 或 Windows Server Update Services"。  
   
    如果从 Windows Server Update Services 更新，则指定服务器 URI。 位于该 URI 的服务器将在连接到此服务器的所有设备上部署更新。

    ![配置更新2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS 服务器用于通过管理控制台管理和分发更新。 WSUS 服务器还可以作为组织内其他 WSUS 服务器的更新源。 充当更新源的 WSUS 服务器称为上游服务器。 在 WSUS 实现中，网络中必须至少有一台 WSUS 服务器能够连接到 Microsoft 更新来获取可用的更新信息。 作为管理员，你可以根据网络安全和配置来决定多少其他 WSUS 服务器直接连接到 Microsoft 更新。
    
    有关详细信息，请参阅 [Windows Server Update Services (WSUS) ](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

建议通过 Azure 门户安装更新。 设备每天自动扫描一次更新。 更新可用后，门户中会显示一条通知。 然后，你可以下载和安装更新。 

> [!NOTE]
> 在继续安装更新之前，请确保设备运行正常并且状态显示为 " **联机** "。

1. 当更新可用于你的设备时，你会看到一条通知。 选择通知，或从顶部命令栏中选择 " **更新设备**"。 这将允许你应用设备软件更新。

    ![更新后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. 在 " **设备更新** " 边栏选项卡中，检查是否已查看与发行说明中的新功能关联的许可条款。

    您可以选择 **下载和安装更新，** 或仅 **下载** 更新。 然后可以选择以后安装这些更新。

    ![更新2之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-2a.png)    

    如果要下载和安装更新，请选中 "下载完成后自动安装更新" 选项。

    ![Update 3 之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-2b.png)

3. 更新的下载开始。 你会看到一条通知，指出下载正在进行中。

    ![更新4之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    通知横幅也会显示在 Azure 门户中。 这表明下载进度。 

    ![Update 5 之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    你可以选择此通知或选择 " **更新设备** " 来查看更新的详细状态。

    ![Update 6 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)   


4. 下载完成后，通知横幅会更新以指示完成。 如果选择下载并安装更新，则将自动开始安装。

    ![更新7之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    如果选择仅下载更新，则选择该通知即可打开 " **设备更新** " 边栏选项卡。 选择“安装”  。
  
    ![更新8后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. 你会看到安装正在进行的通知。

    ![更新9之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)

    该门户还显示信息性警报，指示安装正在进行中。 设备处于脱机状态并且处于维护模式。
    
    ![更新10之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. 由于这是一个1节点设备，因此在安装更新后，设备将重新启动。 重新启动过程中的严重警报将指示设备检测信号丢失。

    ![Update 11 之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    选择警报以查看相应的设备事件。
    
    ![更新12后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. 重新启动后，设备将再次置于维护模式，并显示一条信息性警报以指示。

    如果从顶部命令栏中选择 **更新设备** ，则可以查看更新的进度。   

8. 安装更新后，设备状态将更新为 " **联机** "。 

    ![Update 13 后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    在顶部命令栏中，选择 " **设备更新**"。 验证更新是否已成功安装，以及设备软件版本是否反映了此情况。

    ![更新14之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

9. 你将再次看到一条通知，告知你有可用的更新。 这些是 Kubernetes 更新。 选择通知或从顶部命令栏中选择 " **更新设备** "。

    ![Update 15 之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. 下载 Kubernetes 更新。 与上一更新包相比，你可以看到包的大小不同。

    ![Update 16 之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    安装过程与设备更新的过程相同。 首先，下载更新。

    ![更新17之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. 下载更新后，可以安装更新。 

    ![更新18之后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    安装更新时，设备将进入维护模式。 Kubernetes 更新不会重新启动设备。 

    成功安装 Kubernetes 更新后，横幅通知会消失，因为不再需要进行更新。 你的设备现在已是设备软件和 Kubernetes 的最新版本。

    ![更新19后的软件版本](./media/azure-stack-edge-gpu-install-update/portal-update-20.png)


## <a name="use-the-local-web-ui"></a>使用本地 Web UI

通过两个步骤使用本地 Web UI：

* 下载更新或修补程序
* 安装更新或修补程序

以下部分详细介绍了这些步骤。


### <a name="download-the-update-or-the-hotfix"></a>下载更新或修补程序

执行以下步骤以下载更新。 你可以从 Microsoft 提供的位置或从 Microsoft 更新目录中下载更新。


执行以下步骤，从 Microsoft 更新目录中下载更新。

1. 启动浏览器并导航到 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) 。

    ![搜索目录](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. 在 Microsoft 更新目录的搜索框中，输入要下载的修补程序的知识库 (KB) 号。 例如，输入 **Azure Stack Edge Pro**"，然后单击" **搜索**"。
   
    更新列表显示为 **Azure Stack Edge 更新 2010**。
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2b.png)-->

4. 选择“下载”  。 有两个文件可供下载，其中 *SoftwareUpdatePackage.exe* 和分别对应于设备软件更新和 Kubernetes 更新的 *Kubernetes_Package.exe* 后缀。 将文件下载到本地系统上的文件夹。 也可以将该文件夹复制到可通过设备访问的网络共享。

### <a name="install-the-update-or-the-hotfix"></a>安装更新或修补程序

安装更新或修补程序之前，请确保：

 - 更新或修补程序已本地下载到主机上，或可通过网络共享访问。
 - 你的设备状态为 "正常"，如本地 web UI 的 " **概述** " 页中所示。

   ![更新设备](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png) 

此过程大约需要20分钟才能完成。 执行以下步骤，安装更新或修补程序。

1. 在本地 web UI 中，请参阅**维护**  >  **软件更新**。 记下运行的软件版本。 
   
   ![更新设备2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. 提供更新文件的路径。 如果放置在网络共享上，也可以浏览到更新安装文件。 选择包含 *SoftwareUpdatePackage.exe* 后缀的软件更新文件。

   ![更新设备3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3a.png)

3. 选择“应用”。 

   ![更新设备4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. 出现确认提示时，请选择 **"是"** 以继续。 如果设备是单节点设备，则在应用更新后，设备将重新启动，并出现停机时间。 
   
   ![更新设备5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. 更新启动。 成功更新设备后，该设备将重新启动。 本地 UI 在此期间不可访问。
   
6. 重新启动完成后，会转到“登录”**** 页。 若要验证是否已更新设备软件，请在本地 web UI 中，请参阅**维护**  >  **软件更新**。 对于当前版本，显示的软件版本应为 **2.1.1377.2170**。

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)--> 

7. 现在，你将更新 Kubernetes 软件版本。 重复上述步骤。 提供包含 *Kubernetes_Package.exe* 后缀的 Kubernetes 更新文件的路径。  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)--> 

8. 选择“应用”。 

   ![更新设备7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. 出现确认提示时，请选择 **"是"** 以继续。 

10. 成功安装 Kubernetes 更新后，对**维护**  >  **软件更新**中显示的软件不做任何更改。 


## <a name="next-steps"></a>后续步骤

了解有关 [管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)的详细信息。
