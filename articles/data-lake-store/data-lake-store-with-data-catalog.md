---
title: "在 Azure 数据目录中从 Data Lake Store 注册数据 | Microsoft Docs"
description: "在 Azure 数据目录中从 Data Lake Store 注册数据"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 59f072c7a8272fc04e1d662c0ab17e7ee4500fa6
ms.openlocfilehash: b589742e23ba9cca919a845e4d262ccfea4c9aca
ms.contentlocale: zh-cn
ms.lasthandoff: 02/07/2017


---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>在 Azure 数据目录中从 Data Lake Store 注册数据
在本文中，将了解如何将 Azure Data Lake Store 与 Azure 数据目录集成，以便通过将其与数据目录集成，使数据在组织内可发现。 了解对数据进行分类的详细信息，请参阅 [Azure 数据目录](../data-catalog/data-catalog-what-is-data-catalog.md)。 若要了解可以在其中使用数据目录的方案，请参阅 [Azure 数据目录常见方案](../data-catalog/data-catalog-common-scenarios.md)。

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 为 Data Lake Store 公共预览**启用 Azure 订阅**。 请参阅[说明](data-lake-store-get-started-portal.md)。
* **Azure Data Lake Store 帐户**。 遵循[使用 Azure 门户，实现 Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)中的说明。 在本教程中，需创建一个名为 **datacatalogstore** 的 Data Lake Store 帐户。

    创建帐户后，将示例数据集上传到其中。 在本教程中，上传 [Azure Data Lake Git存储库](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/)中 **AmbulanceDota** 文件夹下的所有.csv文件。 可以使用各种客户端（如 [Azure 存储资源管理器](http://storageexplorer.com/)将数据上传到 blob 容器。
* **Azure 数据目录**。 组织必须已具有为组织创建的 Azure数据目录。 每个组织只允许有一个目录。

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>将 Data Lake Store 注册为数据目录的源

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. 转到 `https://azure.microsoft.com/services/data-catalog`，然后单击“开始使用”。
2. 登录到 Azure 数据目录门户，然后单击“发布数据”。

    ![注册数据源](./media/data-lake-store-with-data-catalog/register-data-source.png "注册数据源")
3. 在下一页上，单击“启动应用程序”。 这将在计算机上下载应用程序清单文件。 双击此清单文件以启动应用程序。
4. 在“欢迎”页上，单击“登录”并输入凭据。

    ![欢迎屏幕](./media/data-lake-store-with-data-catalog/welcome.screen.png "欢迎屏幕")
5. 在“选择数据源”页上，选择“Azure Data Lake”，然后单击“下一步”。

    ![选择数据源](./media/data-lake-store-with-data-catalog/select-source.png "选择数据源")
6. 在下一页上，提供要在数据目录中注册的 Data Lake Store 帐户名称。 其他选项保留为默认值，然后单击“连接”。

    ![连接到数据源](./media/data-lake-store-with-data-catalog/connect-to-source.png "连接到数据源")
7. 下一个页面可以分为以下几部分。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **服务器层次结构**框表示 Data Lake Store 帐户文件夹结构。 **$Root** 表示 Data Lake Store 帐户的根，而 **AmbulanceData** 表示 Data Lake Store 帐户的根中创建的文件夹。

    b. **可用对象**框中列出 **AmbulanceData** 文件夹下的文件和文件夹。

    c. **要注册的对象**框中列出要在 Azure 数据目录中注册的文件和文件夹。

    ![查看数据结构](./media/data-lake-store-with-data-catalog/view-data-structure.png "查看数据结构")
8. 在本教程中，应在目录中注册所有文件。 为此，请单击（![移动对象](./media/data-lake-store-with-data-catalog/move-objects.png "移动对象")）按钮将所有文件移到“要注册的对象”框。

    由于数据将在组织范围内的数据目录中注册，因此建议添加一些元数据，稍后可以使用这些元数据快速查找数据。 例如，可以为数据所有者添加电子邮件地址（例如，上传数据的用户）或添加标签以标识数据。 下面的屏幕截图显示了添加到数据的标记。

    ![查看数据结构](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "查看数据结构")

    单击“注册”。
9. 以下屏幕截图表示数据已成功注册到数据目录中。

    ![注册完成](./media/data-lake-store-with-data-catalog/registration-complete.png "查看数据结构")
10. 单击“查看门户”返回到数据目录门户并验证现在是否可以从门户访问注册的数据。 若要搜索数据，可以使用注册数据时使用的标记。

     ![在目录中搜索数据](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "在目录中搜索数据")
11. 现在可以执行向数据添加批注和文档等操作。 有关详细信息，请参阅以下链接。

    * [在数据目录中批注数据源](../data-catalog/data-catalog-how-to-annotate.md)
    * [在数据目录中阐述数据源](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>另请参阅
* [在数据目录中批注数据源](../data-catalog/data-catalog-how-to-annotate.md)
* [在数据目录中阐述数据源](../data-catalog/data-catalog-how-to-documentation.md)
* [集成 Data Lake Store 和其他 Azure 服务](data-lake-store-integrate-with-other-services.md)

