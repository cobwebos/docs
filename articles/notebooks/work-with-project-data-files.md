---
title: 使用 Azure Notebooks 项目导入和导出数据
description: 如何将数据从外部源导入 Azure Notebooks 项目，以及如何从项目中导出数据。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b522b0bd641d0147518843b11be4cd3a1430ae20
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265332"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>使用 Azure Notebooks 项目中的数据文件

数据是许多 Jupyter Notebooks 的命脉，尤其是用于数据科学的笔记本。 使用 Azure Notebooks，可轻松从各种源导入项目，然后在笔记本中使用这些数据。 还可让笔记本生成存储在项目中的数据，然后下载这些数据供其他地方使用。

正在运行的笔记本中的“数据”菜单还提供“上传”和“下载”命令，这些命令处理项目中的文件以及当前笔记本会话的临时文件。

还以使用笔记本中的代码直接访问各种数据源，包括项目中的文件。 也可使用代码单元中的命令访问任意数据。 由于这类数据存储在笔记本会话的变量中，因此除非使用代码专门生成项目文件，否则不会将其保存在项目中。

使用数据中的代码最好是在运行中的笔记本本身中体验：为此，请参阅[在 Azure Notebooks 示例笔记本中获取数据](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)。

本文的其余部分提供了关于项目级文件操作的详细信息。

## <a name="import-data"></a>导入数据

可使用“数据”菜单或诸如 `curl` 之类的命令，从项目仪表板或正在运行的笔记本中将文件导入项目。

### <a name="import-files-from-the-project-dashboard"></a>从项目仪表板导入文件

1. 在项目中，导航到要导入文件的文件夹。

1. 选择“上传”命令，然后“从 URL”或“从计算机”中为要导入的数据设计必要的详细信息：

   - **从 URL**：在“文件 URL”字段输入源地址，并在“文件名”字段输入在项目中分配给笔记本的文件名。 然后选择“+ 添加文件”将 URL 添加到上传列表。 对任何其他 URL 重复此过程，然后选择“完成”。

     ![从 URL 弹出窗口上传](media/quickstarts/upload-from-url-popup.png)

   - **从计算机**：将文件拖放到弹出窗口，或选择“选择文件”，然后浏览并选择要导入的数据文件。 可删除或选择任意数量、类型和格式的文件，因为它是由笔记本中的代码打开文件和解析其数据。

     ![从计算机弹出窗口上传](media/quickstarts/upload-from-computer-popup.png)

1. 导入后，文件将显示在项目仪表板上，并且可以使用包含文件夹的相对路径名在笔记本代码中访问。

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>从笔记本中的“文件”菜单导入文件

1. 在正在运行的笔记本中，选择“文件” > “上传”命令：

    ![笔记本中的文件“上传”菜单命令](media/file-menu-upload.png)

1. 在打开的对话框中，导航到并选择要上传的文件。 可选择任意数量的任何类型的文件。 完成后选择“打开”。

1. 在出现的“上传状态”弹出窗口中，从下拉列表中选择“目标文件夹”：

    - 会话文件夹 (*~/*)：将文件上传到当前的笔记本会话，但不会在项目中创建文件。 会话文件夹是项目文件夹的对等方，但在会话结束后不会持久化。 要访问代码中的会话文件，请在文件名前面加上相对路径“../”。

        使用会话文件夹有助于进行实验，并避免长期将项目中可能需要或不需要的文件弄得杂乱无章。 此外，还可将文件上传到项目中具有相同文件名的会话文件夹中，而不会导致冲突，且无需重命名文件。 例如，假设项目中已有一个版本的“data.csv”，但希望尝试使用不同版本的“data.csv”。 通过将文件上传到会话文件夹，可使用上传文件中的数据（在代码中使用“./data.csv”）而不是项目文件中的数据来运行笔记本。

    - 项目文件夹(“/project”)：将文件上传到项目中，在那里可以使用代码中的相对路径名访问文件。 将文件上传到此文件夹与在项目仪表板上传文件相同。 该文件随项目一起保存，并可在以后的会话中使用。

        如果尝试上传与项目中已存在的文件同名的文件，则上传会失败。 要覆盖文件，请从项目仪表板上传新文件，以此来选择覆盖。

1. 选择“开始上传”，完成该过程。

### <a name="create-or-import-files-using-commands"></a>使用命令创建或导入文件

可使用终端内或 Python 代码单元中的命令在项目和会话文件夹中创建文件。 例如，使用 `curl` 和 `wget` 等命令直接从 Internet 下载文件。

要在终端中下载文件，请在项目仪表板上选择“终端”命令，然后输入合适的命令：

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

在笔记本中使用 Python 代码单元时，请在命令前添加 `!`。

项目文件夹是默认文件夹，因此指定目标文件名（如“oil_price.csv”）会在项目中创建文件。 要创建会话文件，请为名称加上前缀“.../”，如“.../oil_price.csv”。

### <a name="create-files-in-code"></a>使用代码创建文件

使用创建文件的代码（例如 pandas `write_csv` 函数）时，路径名始终相对于项目文件夹。 使用“../”创建一个会话文件，该文件在笔记本暂停和关闭时被弃用。

## <a name="export-files"></a>导出文件

可从项目仪表板或笔记本中导出数据。

## <a name="export-files-from-the-project-dashboard"></a>从项目仪表板导出文件

在项目仪表板上，右键单击文件并选择“下载”：

![在项目项上下文菜单中下载命令](media/download-command.png)

也以选择文件并使用仪表板上的“下载”命令（键盘快捷方式：D）：

![项目仪表板上的“下载”工具栏命令](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>从笔记本中的“数据”菜单导出文件

1. 选择“文件” > “下载”菜单命令：

    ![笔记本中的“数据下载”菜单命令](media/file-menu-download.png)

1. 弹出窗口显示会话中的文件夹；“项目”文件夹包含项目文件：

    ![弹出数据下载命令，在其中选择文件和文件夹](media/file-menu-download-popup.png)

1. 选择要下载的文件和文件夹左侧的框，然后选择“下载选定的内容”。

1. 笔记本将准备一个包含所选文件的单个“.zip”文件，然后按照通常从浏览器保存的方式保存这些文件。 即使下载单个文件，笔记本也会创建“.zip”文件。

## <a name="next-steps"></a>后续步骤

- [在笔记本中访问云数据](access-data-resources-jupyter-notebooks.md)
