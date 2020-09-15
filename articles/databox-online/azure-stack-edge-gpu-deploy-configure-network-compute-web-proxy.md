---
title: 教程：在 Azure 门户中使用 GPU 配置 Azure Stack Edge 设备的网络设置 | Microsoft Docs
description: 有关部署 Azure Stack Edge GPU 的教程指导你为物理设备配置网络、计算网络和 Web 代理设置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: cc111f0df889efd1d3720e2ec0e4aaa452efd801
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461861"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>教程：使用 GPU 为 Azure Stack Edge 配置网络

本教程介绍如何通过本地 Web UI，使用板载 GPU 为 Azure Stack Edge 设备配置网络。

连接过程可能需要大约 20 分钟才能完成。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 配置网络
> * 启用计算网络
> * 配置 Web 代理


## <a name="prerequisites"></a>先决条件

在使用 GPU 配置和设置 Azure Stack Edge 设备之前，请确保：

* 已根据[安装 Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md) 中详述的内容安装了物理设备。
* 已按照[连接到 Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md) 中所述连接到设备的本地 Web UI


## <a name="configure-network"></a>配置网络

“入门”页显示的各种设置是配置物理设备并将其注册到 Azure Stack Edge 服务所需的。 

按照以下步骤操作，为设备配置网络。

1. 在设备的本地 Web UI 中，转到“入门”页。 

2. 在“网络”磁贴上，选择“配置” 。  
    
    ![本地 Web UI“网络设置”磁贴](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    在物理设备上有六个网络接口。 端口 1 和端口 2 是 1 Gbps 网络接口。 端口 3、端口 4、端口 5 和端口 6 都是 25 Gbps 网络接口，也可用作 10 Gbps 网络接口。 系统自动将端口 1 配置为仅用于管理的端口，而端口 2 到端口 6 都是数据端口。 对于新设备，“网络设置”页如下所示。
    
    ![本地 Web UI“网络设置”页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. 若要更改网络设置，请选择一个端口，然后在显示的右窗格中修改 IP 地址、子网、网关、主 DNS 和辅助 DNS。 

    - 如果选择端口 1，你可以看到它已预配置为静态。 

        ![本地 Web UI“端口 1 网络设置”](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - 如果选择端口 2、端口 3、端口 4或端口 5，默认情况下，所有这些端口都配置为 DHCP。

        ![本地 Web UI“端口 3 网络设置”](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    配置网络设置时，请记住：

   * 如果你的环境中启用了 DHCP，则会自动配置网络接口。 系统会自动分配 IP 地址、子网、网关和 DNS。
   * 如果未启用 DHCP，则可以根据需要分配静态 IP。
   * 可以将网络接口配置为 IPv4。
   * 在 25 Gbps 接口上，可以将 RDMA（远程直接访问内存）模式设置为 iWarp 或 RoCE（通过聚合以太网的 RDMA）。 如果低延迟是主要要求，且可伸缩性不是重点，请使用 RoCE。 当延迟是关键要求，但易用性和可伸缩性也十分重要时，iWARP 是最佳备选方案。
   * 任何端口的序列号都与节点序列号相对应。

    配置设备网络后，页面将更新，如下所示。

    ![本地 Web UI“网络设置”页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * 建议不要将网络接口的本地 IP 地址从静态切换到 DCHP，除非有另一个 IP 地址连接到该设备。 如果使用一个网络接口并切换到 DHCP，则无法确定 DHCP 地址。 如果要更改为 DHCP 地址，请等待设备激活服务后再更改。 然后，可以在 Azure 门户中为你的服务查看“设备属性”中所有适配器的 IP。


    配置并应用网络设置后，请返回“入门”。

## <a name="enable-compute-network"></a>启用计算网络

请按照以下步骤，启用计算并配置计算网络。

1. 转到设备本地 Web UI 中的“入门”页。 在“网络”磁贴上，选择“计算网络” 。  

    ![本地 UI 中的计算页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. 在“计算”页中，选择要为计算启用的网络接口。 

    ![本地 UI 中的计算页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. 在“网络设置”对话框中，选择“启用” 。 启用计算时，会在设备上的该网络接口上创建一个虚拟交换机。 虚拟交换机用于设备上的计算基础结构。 
    
3. 分配“Kubernetes 节点 IP”。 这些静态 IP 地址适用于计算 VM。 

    对于 n 节点设备，使用起始 IP 地址和结束 IP 地址为计算 VM 提供了连续范围内的至少 n + 1 个（或更多）IPv4 地址 。 假设 Azure Stack Edge 是 1 节点设备，则至少提供 2 个连续的 IPv4 地址。

    > [!IMPORTANT]
    > Azure Stack Edge 上的 Kubernetes 对 Pod 使用 172.27.0.0/16 子网，对服务使用 172.28.0.0/16 子网。 请确保这些子网未在网络中使用。 如果这些子网已在网络中使用，则可从设备的 PowerShell 界面运行 `Set-HcsKubeClusterNetworkInfo` cmdlet 来更改这些子网。 有关详细信息，请参阅[更改 Kubernetes Pod 子网和服务子网](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets)。


4. 分配“Kubernetes 外部服务 IP”。 这些地址也是负载均衡 IP 地址。 这些连续的 IP 地址用于要在 Kubernetes 群集之外公开的服务，你可以根据公开的服务数量指定静态 IP 范围。 
    
    > [!IMPORTANT]
    > 强烈建议为 Azure Stack Edge 中心服务指定至少 1 个 IP 地址，以访问计算模块。 然后，可以选择为需要从群集外部访问的其他服务/IoT Edge 模块指定其他 IP 地址（每个服务/模块 1 个地址）。 以后可以更新服务 IP 地址。 
    
5. 选择“应用”。

    ![本地 UI 中的计算页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>配置 Web 代理

这是可选的配置。

> [!IMPORTANT]
> * 如果在 Azure Stack Edge 设备上启用计算和使用 IoT Edge 模块，建议将 Web 代理身份验证设置为“无”。 不支持 NTLM。
>* 不支持代理自动配置 (PAC) 文件。 PAC 文件定义了 Web 浏览器和其他用户代理如何自动选择适当的代理服务器（访问方法）来获取给定 URL。 尝试拦截和读取所有流量（然后用其自己的证书对所有内容进行重新签名）的代理不兼容，因为代理的证书不受信任。 通常，透明代理非常适用于 Azure Stack Edge。 不支持非透明的 Web 代理。

1. 转到设备本地 Web UI 中的“入门”页。
2. 在“网络”磁贴上，配置 Web 代理服务器设置。 尽管这一配置为可选操作，但如果使用 Web 代理，则只能在此页上进行配置。

   ![本地 Web UI“Web 代理设置”页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. 在“Web 代理设置”页上，执行以下步骤：

    1. 在“Web 代理 URL”框中，按以下格式输入 URL：`http://host-IP address or FQDN:Port number`。 不支持 HTTPS URL。

    2. 在“身份验证”下选择“无”或“NTLM”。   如果在 Azure Stack Edge 设备上启用计算和使用 Stack Edge 模块，建议将 Web 代理身份验证设置为“无”。 不支持 NTLM。

    3. 如果使用身份验证，请输入用户名和密码。

    4. 若要验证并应用配置的 Web 代理设置，请选择“应用”  。
    
   ![本地 Web UI“Web 代理设置”页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. 应用设置后，返回到“入门”。


## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 先决条件
> * 配置网络
> * 启用计算网络
> * 配置 Web 代理


若要了解如何设置 Azure Stack Edge 设备，请参阅：

> [!div class="nextstepaction"]
> [配置设备设置](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
