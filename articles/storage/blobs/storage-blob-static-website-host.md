---
title: 教程：在 Blob 存储上托管静态网站 - Azure 存储
description: 了解如何配置存储帐户，以便进行静态网站托管，以及如何将静态网站部署到 Azure 存储。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 97679ec48ee9120005118b6cacaf37e45657db08
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906628"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>教程：在 Blob 存储上托管静态网站

本教程介绍如何生成静态网站并将其部署到 Azure 存储。 完成本教程后，你会有一个可供用户公开访问的静态网站。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 配置静态网站托管
> * 部署 Hello World 网站

## <a name="prerequisites"></a>必备条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> 请确保创建常规用途 v2 存储帐户。 静态网站在任何其他类型的存储帐户中都不可用。

本教程使用可供编程人员免费使用的工具 [Visual Studio Code](https://code.visualstudio.com/download) 来生成静态网站并将其部署到 Azure 存储帐户。

安装 Visual Studio Code 以后，请安装 Azure 存储预览版扩展。 此扩展将 Azure 存储管理功能与 Visual Studio Code 集成在一起。 可以使用此扩展将静态网站部署到 Azure 存储。 若要安装此扩展，请执行以下操作：

1. 启动 Visual Studio Code。
2. 在工具栏上，请单击“扩展”  。 搜索“Azure 存储”，然后从列表中选择“Azure 存储”扩展   。 然后单击“下载”  按钮，以便安装此扩展。

    ![在 VS Code 中安装 Azure 存储扩展](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

## <a name="configure-static-website-hosting"></a>配置静态网站托管

第一步是配置存储帐户，以便在 Azure 门户中托管静态网站。 配置帐户以便进行静态网站托管时，Azure 存储会自动创建名为 *$web* 的容器。 *$web* 容器会包含静态网站的文件。 

1. 在 Web 浏览器中打开 [Azure 门户](https://portal.azure.com/)。 
1. 找到存储帐户并显示帐户概览。
1. 选择“静态网站”，以显示静态网站的配置页。 
1. 选择“启用”，启用针对存储帐户的静态网站托管功能。 
1. 在“索引文档名称”字段中，指定 *index.html* 的默认索引页。  当用户导航到静态网站的根目录时，会显示默认索引页。  
1. 在“错误文档路径”字段中，  指定默认错误页 *404.html*。 当用户尝试导航到静态网站中不存在的页面时，会显示默认错误页。
1. 单击“ **保存**”。 Azure 门户现在会显示静态网站终结点。 

    ![启用针对存储帐户的静态网站托管功能](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>部署 Hello World 网站

接下来，使用 Visual Studio Code 创建 Hello World 网页，并将其部署到在 Azure 存储帐户中托管的静态网站。

1. 在本地文件系统上创建名为 *mywebsite* 的空文件夹。 
1. 启动 Visual Studio Code，打开刚刚在“资源管理器”面板中创建的文件夹。 

    ![在 Visual Studio Code 中打开文件夹](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. 在 *mywebsite* 文件夹中创建默认索引文件，并将其命名为 *index.html*。

    ![在 Visual Studio Code 中创建默认索引文件](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. 在编辑器中打开 *index.html*，将以下文本粘贴到文件中，然后进行保存：

    ```
    <h1>Hello World!</h1>
    ```

1. 创建默认错误文件，将其命名为 *404.html*。
1. 在编辑器中打开 404.html，将以下文本粘贴到该文件中，然后保存  ：

    ```
    <h1>404</h1>
    ```

1. 在“资源管理器”面板的 *mywebsite* 文件夹下双击，选择“部署到静态网站...”，以便部署网站。   系统会提示你登录到 Azure，以便检索订阅的列表。

1. 选择包含存储帐户的订阅，已经为该帐户启用静态网站托管功能。 接下来，根据提示选择存储帐户。

Visual Studio Code 现在会将文件上传到 Web 终结点，并显示成功状态栏。 启动网站，在 Azure 中查看它。

![在 Azure 中查看静态网站部署](media/storage-blob-static-website-host/view-static-website-endpoint.png)

你已成功完成本教程并将静态网站部署到 Azure。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何为静态网站托管配置 Azure 存储帐户，以及如何创建静态网站并将其部署到 Azure 终结点。

接下来，了解如何为静态网站配置自定义域。

> [!div class="nextstepaction"]
> [将自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)
