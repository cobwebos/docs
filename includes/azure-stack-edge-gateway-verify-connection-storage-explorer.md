---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 3474a97f4b05fd1f944e18abebcef927232453e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89080150"
---
如果这是你首次使用存储资源管理器，则需要执行以下步骤。

1. 从顶部的命令栏中，转到“编辑”>“目标 Azure Stack API”。

    ![配置存储资源管理器](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. 重启存储资源管理器以使更改生效。


按照以下步骤连接到存储帐户并验证连接。

1. 在存储资源管理器中，选择存储帐户。 右键单击并选择“连接到 Azure 存储”选项。 

    ![配置存储资源管理器](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. 在“连接到 Azure 存储”对话框中，选择“使用存储帐户名称和密钥”。********

    ![配置存储资源管理器](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. 在“使用名称和密钥连接”对话框中，执行以下步骤：

    1. 输入 Edge 存储帐户的显示名称。 
    2. 提供 Edge 存储帐户名称。
    3. 粘贴通过 Azure 资源管理器从设备本地 API 获取的访问密钥。
    4. 选择“其他(在下面输入)”作为存储域，然后以 `<appliance name>.<DNSdomain>` 格式提供 blob 服务终结点的后缀。 
    5. 选中“使用 HTTP”选项，因为通过 http 进行传输。 
    6. 选择“**下一页**”。

    ![配置存储资源管理器](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. 在“连接摘要”**** 对话框中，复查所提供的信息。 选择“连接”。

    ![配置存储资源管理器](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. 成功添加的帐户显示在存储资源管理器的左窗格中，并且其名称的末尾追加有 (外部, 其他)。 选择“Blob 容器”，查看容器。

    ![查看 blob 容器](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

下一步要验证的是，通过此连接的数据传输实际上是否正常工作。

执行以下步骤，将数据加载到设备上的 Edge 存储帐户中，它应自动分层到映射的 Azure 存储帐户。

1. 在 Edge 存储帐户中选择要将数据加载到其中的容器。 选择“上传”，然后选择“上传文件” 。

    ![验证数据传输](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. 在“上传文件”对话框中，导航到并选择要上传的文件。 选择“**下一页**”。

    ![验证数据传输](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. 验证文件是否已上传。 上传的文件会显示在容器中。

    ![验证数据传输](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. 接下来，将连接到已映射到该 Edge 存储帐户的 Azure 存储帐户。 上传到 Edge 存储帐户的所有数据应自动分层到 Azure 存储帐户。 
    
    若要获取 Azure 存储帐户的连接字符串，请转到“Azure 存储帐户”>“访问密钥”并复制连接字符串。

    ![验证数据传输](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    使用连接字符串连接到 Azure 存储帐户。  

    ![验证数据传输](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. 在“连接摘要”**** 对话框中，复查所提供的信息。 选择“连接”。

    ![验证数据传输](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. 你将看到在 Edge 存储帐户中上传的文件已传输到 Azure 存储帐户。

    ![验证数据传输](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)