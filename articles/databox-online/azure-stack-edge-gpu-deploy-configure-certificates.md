---
title: 教程：在 Azure 门户中使用 GPU 配置 Azure Stack Edge Pro 设备的证书 | Microsoft Docs
description: 有关使用 GPU 部署 Azure Stack Edge Pro 的教程指导你在物理设备上配置证书。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 7854aff0b4194efae7c4df653dee18e2676fdd41
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446312"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>教程：使用 GPU 为 Azure Stack Edge Pro 配置证书

本教程介绍如何通过本地 Web UI，使用板载 GPU 为 Azure Stack Edge Pro 设备配置证书。

此步骤所花费的时间取决于你选择的特定选项，以及在环境中建立证书流的方式。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书

## <a name="prerequisites"></a>先决条件

在使用 GPU 配置和设置 Azure Stack Edge Pro 设备之前，请确保：

* 已根据[安装 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中详述的内容安装了物理设备。
* 如果你计划使用自己的证书：
    - 应该使用适当的格式来准备证书，包括签名链证书。 有关证书的详细信息，请参阅[管理证书](azure-stack-edge-j-series-manage-certificates.md)

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>为设备配置证书

1. 在“证书”页中，将配置证书。 根据在“设备”页中更改的是设备名称还是 DNS 域，可以为证书选择以下某个选项。

    - 如果在上一步中没有更改设备名称或 DNS 域，并且不想使用自己的证书，则可以跳过此步骤，继续进行下一步。 设备首先已自动生成自签名证书。 

        ![本地 Web UI“证书”页](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - 如果更改了设备名称或 DNS 域，会看到证书的状态显示为“无效”。 

        ![本地 Web UI“证书”页 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        选择证书查看状态详细信息。

        ![本地 Web UI“证书”页 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        这是因为证书不反映更新的设备名称和 DNS 域（在使用者名称和使用者备用名称中使用）。 若要成功激活设备，请选择下列选项之一： 
    
        - **生成所有设备证书**。 这些设备证书应仅用于测试，不能用于生产工作负载。 有关详细信息，请转到[在 Azure Stack Edge Pro上生成设备证书](#generate-device-certificates)。

        - **使用自己的证书**。 可以使用自己的已签名终结点证书和相应的签名链。 先添加签名链，然后上传终结点证书。 建议始终将自己的证书用于生产工作负载。 有关详细信息，请转到[在 Azure Stack Edge Pro 设备上使用自己的证书](#bring-your-own-certificates)。
    
        - 可以使用一些自己的证书并生成一些设备证书。 “生成证书”选项只重新生成设备证书。

    - 如果更改了设备名称或 DNS 域，而不生成证书或不使用自己的证书，则将阻止激活。


### <a name="generate-device-certificates"></a>生成设备证书

按照以下步骤操作，以生成设备证书。

使用以下步骤重新生成并下载 Azure Stack Edge Pro 设备证书：

1. 在设备的本地 Web UI 中，转到“配置”>“证书”。 选择“生成证书”。

    ![生成并下载证书 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. 在“生成设备证书”中，选择“生成” 。 

    ![生成并下载证书 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    现在已生成并应用设备证书。 生成并应用证书需要几分钟时间。
    
    > [!IMPORTANT]
    > 正在执行证书生成操作时，请勿使用自己的证书并尝试通过“+ 添加证书”选项添加证书。

    操作成功完成后，系统会发出通知。 **若要避免任何潜在的缓存问题，请重启浏览器。**
    
    ![生成并下载证书 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. 生成证书后： 

    - 所有证书的状态会显示为“有效”。 

        ![生成并下载证书 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - 可以选择特定证书名称，并查看证书详细信息。 

        ![生成并下载证书 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - “下载”列现已填充。 此列包含用于下载重新生成的证书的链接。 

        ![生成并下载证书 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. 选择证书的下载链接，并在系统出现提示时保存证书。 

    ![生成并下载证书 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. 对要下载的所有证书重复此过程。 
    
    ![生成并下载证书 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    设备生成的证书按以下名称格式保存为 DER 证书： 

    `<Device name>_<Endpoint name>.cer`. 这些证书包含设备上安装的相应证书的公钥。 

需要在所用客户端系统上安装这些证书，才能在 ASE 设备上访问终结点。 这些证书在客户端和设备之间建立信任关系。

若要在所用客户端上导入并安装这些证书以访问设备，请按照[在访问 Azure Stack Edge Pro 设备的客户端上导入证书](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步骤操作。 

如果使用 Azure 存储资源管理器，需要在客户端上以 PEM 格式安装证书，并且需要将设备生成的证书转换为 PEM 格式。 

> [!IMPORTANT]
> - 下载链接仅适用于设备生成的证书，并不适用于使用自己的证书。
> - 可以决定搭配使用设备生成的证书和自己的证书，只要满足其他证书要求即可。 有关详细信息，请转到[证书要求](azure-stack-edge-j-series-certificate-requirements.md)。
    

### <a name="bring-your-own-certificates"></a>使用自己的证书

请按照以下步骤添加自己的证书，包括签名链。

1. 若要上传证书，请在“证书”页上选择“+ 添加证书” 。

    ![本地 Web UI“证书”页 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. 首先上传签名链，然后选择“验证和添加”。

    ![本地 Web UI“证书”页 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. 现在，可以上传其他证书。 例如，可以上传 Azure 资源管理器和 Blob 存储终结点证书。

    ![本地 Web UI“证书”页 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    还可以上传本地 Web UI 证书。 上传该证书后，需要启动浏览器并清除缓存。 然后需要连接到设备本地 Web UI。  

    ![本地 Web UI“证书”页 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    还可以上传节点证书。

    ![本地 Web UI“证书”页 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    可以随时选择证书并查看详细信息，以确保这些内容与上传的证书匹配。

    ![本地 Web UI“证书”页 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    证书页面应会更新，显示新添加的证书。

    ![本地 Web UI“证书”页 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > 除 Azure 公有云外，需要在激活所有云配置（Azure 政府或 Azure Stack）之前引入签名链证书。


现可激活设备。 选择“< 返回到‘开始使用’”。


## <a name="next-steps"></a>后续步骤

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书

若要了解如何激活 Azure Stack Edge Pro 设备，请参阅：

> [!div class="nextstepaction"]
> [激活 Azure Stack Edge Pro 设备](./azure-stack-edge-gpu-deploy-activate.md)
