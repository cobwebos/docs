---
title: 教程：在 Azure 门户中使用 GPU 配置 Azure Stack Edge 设备的证书 | Microsoft Docs
description: 有关使用 GPU 部署 Azure Stack Edge 的教程指导你在物理设备上配置证书。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 80a857f80fd2c164637e591fbab43123659cd2f7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268170"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>教程：使用 GPU 为 Azure Stack Edge 配置证书

本教程介绍如何通过本地 Web UI，使用板载 GPU 为 Azure Stack Edge 设备证书。

此步骤所花费的时间取决于你选择的特定选项，以及在环境中建立证书流的方式。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书

## <a name="prerequisites"></a>先决条件

在使用 GPU 配置和设置 Azure Stack Edge 设备之前，请确保：

* 已根据[安装 Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md) 中详述的内容安装了物理设备。
* 如果你计划使用自己的证书：
    - 应该使用适当的格式来准备证书，包括签名链证书。
<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>为设备配置证书


1. 在“安全”磁贴上，为证书选择“配置” 。 

    ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. 根据在“设备设置”磁贴中更改的是设备名称还是 DNS 域，可以为证书选择以下某个选项。

    - 如果在上一步中没有更改设备名称或 DNS 域，并且不想使用自己的证书，则可以跳过此步骤，继续进行下一步。 设备首先已自动生成自签名证书。 

        ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - 如果更改了设备名称或 DNS 域，若要成功激活设备，可以选择以下选项之一： 
    
        - **生成所有设备证书**。 设备证书仅应用于测试，而不能用于生产工作负载。**有关详细信息，请转到[在 Azure Stack Edge 上生成设备证书](#generate-device-certificates)。

        - **使用自己的证书**。 可以使用自己的已签名终结点证书和相应的签名链。 先添加签名链，然后上传终结点证书。 建议始终将自己的证书用于生产工作负载。 有关详细信息，请转到[在 Azure Stack Edge 设备上使用自己的证书](#bring-your-own-certificates)。
    
        - 可以使用一些自己的证书并生成一些设备证书。 “生成证书”选项只重新生成设备证书。

    - 如果更改了设备名称或 DNS 域，而不生成证书或不使用自己的证书，则将阻止激活。


### <a name="generate-device-certificates"></a>生成设备证书

按照以下步骤操作，以生成设备证书。

使用以下步骤重新生成并下载 Azure Stack Edge 设备证书：

1. 在设备的本地 Web UI 中，转到“配置”>“证书”。 选择“生成证书”。

    ![生成并下载证书 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. 在“生成设备证书”中，选择“生成” 。

    ![生成并下载证书 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    现在已生成并应用设备证书。 
    
    > [!IMPORTANT]
    > 正在执行证书生成操作时，请勿使用自己的证书并尝试通过“+ 添加证书”选项添加证书。

    操作成功完成后，系统会发出通知。 若要避免任何潜在的缓存问题，请重启浏览器。 
    
    ![生成并下载证书 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. 在“证书”页上，显示“下载”列已填充，并且提供了可用于下载重新生成的证书的链接 。 

    ![生成并下载证书 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. 选择证书的下载链接，并在系统出现提示时保存证书。 

    ![生成并下载证书 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. 对要下载的所有证书重复此过程。 
    
    ![生成并下载证书 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    设备生成的证书按以下名称格式保存为 DER 证书： 

    `<Device name>_<Endpoint name>.cer`. 这些证书包含设备上安装的相应证书的公钥。 

需要在所用客户端系统上安装这些证书，才能在 ASE 设备上访问终结点。 这些证书在客户端和设备之间建立信任关系。

若要在所用客户端上导入并安装这些证书以访问设备，请按照[在访问 Azure Stack Edge 设备的客户端上导入证书](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步骤操作。 

如果使用 Azure 存储资源管理器，需要在客户端上以 PEM 格式安装证书，并且需要将设备生成的证书转换为 PEM 格式。 

> [!IMPORTANT]
> - 下载链接仅适用于设备生成的证书，并不适用于使用自己的证书。
> - 可以决定搭配使用设备生成的证书和自己的证书，只要满足其他证书要求即可。 有关详细信息，请转到[证书要求](azure-stack-edge-j-series-certificate-requirements.md)。
    

### <a name="bring-your-own-certificates"></a>使用自己的证书

请按照以下步骤添加自己的证书，包括签名链。

1. 若要上传证书，请在“证书”页上选择“+ 添加证书” 。

    ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. 首先上传签名链，然后选择“验证和添加”。

    ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. 现在，可以上传其他证书。 例如，可以上传 Azure 资源管理器和 Blob 存储终结点证书。

    ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    还可以上传本地 Web UI 证书。 上传该证书后，需要启动浏览器并清除缓存。 然后需要连接到设备本地 Web UI。  

    ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    还可以上传节点证书。

    ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    证书页面应会更新，显示新添加的证书。

    ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > 除 Azure 公有云外，需要在激活所有云配置（Azure 政府或 Azure Stack）之前引入签名链证书。


现可激活设备。


## <a name="next-steps"></a>后续步骤

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书

若要了解如何激活 Azure Stack Edge 设备，请参阅：

> [!div class="nextstepaction"]
> [激活 Azure Stack Edge 设备](./azure-stack-edge-gpu-deploy-activate.md)
