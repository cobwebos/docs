---
title: 允许后端服务器所需的证书
titleSuffix: Azure Application Gateway
description: 本文提供了一些示例，说明如何将 SSL 证书转换为允许 Azure 应用程序网关上的后端实例所需的身份验证证书和受信任的根证书
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 48944c513bd075e3859503fdadc4001261dc8c4a
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048173"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>创建证书以允许 Azure 应用程序网关中的后端

若要执行端到端 SSL，应用程序网关要求通过上传身份验证证书/受信任的根证书来允许后端实例。 允许使用证书时，对于 v1 SKU，身份验证证书是必需的，但对于 v2 SKU，受信任的根证书是必需的。

在本文中，学习如何：

> [!div class="checklist"]
>
> - 从后端证书中导出身份验证证书（对于 v1 SKU）
> - 从后端证书中导出受信任的根证书（适用于 v2 SKU）

## <a name="prerequisites"></a>先决条件

若要允许应用程序网关中的后端实例，需要使用现有的后端证书来生成身份验证证书或受信任的根证书。 后端证书可与 SSL 证书相同，为了提高安全性，两者也可以不同。 应用程序网关不会提供任何机制用于创建或购买 SSL 证书。 对于测试，可以创建自签名证书，但不应将其用于生产工作负荷。 

## <a name="export-authentication-certificate-for-v1-sku"></a>导出身份验证证书（对于 v1 SKU）

需要使用一个身份验证证书来允许应用程序网关 v1 SKU 中的后端实例。 该身份验证证书是后端服务器证书的公钥，采用 Base-64 编码的 X.509(.CER) 格式。 此示例使用 SSL 证书作为后端证书，并导出其公钥用于身份验证。 另外，此示例使用 Windows 证书管理器工具导出所需的证书。 你可以选择使用任何其他便利的工具。

从 SSL 证书中导出公钥 .cer 文件（不是私钥）。 以下步骤可帮助你导出证书的 .cer 文件，其格式为 Base-64 编码的 X.509(.CER)：

1. 若要获取证书 .cer 文件，请打开“管理用户证书”。 找到该证书（通常位于“证书 - 当前用户”>“个人”>“证书”中），并单击右键。 单击“所有任务”，并单击“导出”。 此操作将打开“证书导出向导”。 如果在 Current User\Personal\Certificates 下找不到证书，可能会意外地打开“Certificates - Local Computer”而不是“Certificates - Current User”）。 如果想要使用 PowerShell 在当前用户范围内打开证书管理程序，请在控制台窗口中键入“certmgr”。

   ![导出](./media/certificates-for-backend-authentication/export.png)

2. 在向导中，单击“下一步”。

   ![导出证书](./media/certificates-for-backend-authentication/exportwizard.png)

3. 选择“否，不导出私钥”，并单击“下一步”。

   ![不要导出私钥](./media/certificates-for-backend-authentication/notprivatekey.png)

4. 在“导出文件格式”页上，选择“Base-64 编码的 X.509 (.CER)”，并单击“下一步”。

   ![Base-64 编码](./media/certificates-for-backend-authentication/base64.png)

5. 对于“要导出的文件”，“浏览”到要将证书导出的目标位置。 在“文件名”中，为证书文件命名。 然后单击“下一步”。

   ![浏览](./media/certificates-for-backend-authentication/browse.png)

6. 单击“完成”导出证书。

   ![完成](./media/certificates-for-backend-authentication/finish.png)

7. 证书已成功导出。

   ![成功](./media/certificates-for-backend-authentication/success.png)

   导出的证书类似于以下内容：

   ![已导出](./media/certificates-for-backend-authentication/exported.png)

8. 如果使用记事本打开导出的证书，则会看到类似于此示例的一些内容。 蓝色部分包含已上传到应用程序网关的信息。 如果使用记事本打开证书，并且内容不与此类似，则这通常意味着你没有使用 Base-64 编码的 X.509(.CER) 格式将其导出。 此外，如果希望使用其他文本编辑器，请注意，某些编辑器可能会在后台引入意外的格式设置。 将此证书中的文本上传到 Azure 时，这可能会产生问题。

   ![使用记事本打开](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>导出受信任的根证书（适用于 v2 SKU）

必须使用受信任的根证书将应用程序网关 v2 SKU 中的后端实例加入允许列表。 此根证书是来自后端服务器证书的 Base-64 编码的 X.509(.CER) 格式根证书。 在此示例中，我们将使用 SSL 证书作为后端证书，并导出其公钥，然后从 base64 编码格式的公钥中导出受信任 CA 的根证书，以便获取受信任的根证书。 中间证书应该与服务器证书捆绑并安装在后端服务器上。

以下步骤用于导出证书的 .cer 文件：

1. 使用上面的“从后端证书中导出身份验证证书（适用于 v1 SKU）”部分提到的步骤 1-9，从后端证书导出公钥。

2. 导出公钥后，打开该文件。

   ![开放授权证书](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![关于证书](./media/certificates-for-backend-authentication/general.png)

3. 转到“证书路径”视图即可查看证书颁发机构。

   ![证书详细信息](./media/certificates-for-backend-authentication/certdetails.png)

4. 选择根证书，然后单击“查看证书”。

   ![证书路径](./media/certificates-for-backend-authentication/rootcert.png)

   应该会看到根证书详细信息。

   ![证书信息](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. 转到“详细信息”视图，然后单击“复制到文件...”。

   ![复制根证书](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 目前，你已从后端证书提取根证书的详细信息。 此时会看到“证书导出向导”。 现在，使用上面的“从后端证书中导出身份验证证书（适用于 v1 SKU）”部分提到的步骤 2-9，导出 Base-64 编码的 X.509(.CER) 格式的受信任根证书。

## <a name="next-steps"></a>后续步骤

现已创建采用 Base-64 编码的 X.509(.CER) 格式身份验证证书/受信任的根证书。 可将此证书添加到应用程序网关，以将后端服务器加入允许列表进行端到端的 SSL 加密。 参阅[如何配置端到端 SSL 加密](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)。

