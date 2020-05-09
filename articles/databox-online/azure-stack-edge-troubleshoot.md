---
title: 使用 Azure 门户对 Azure Stack Edge 进行故障排除 |Microsoft Docs
description: 介绍如何对 Azure Stack 边缘问题进行故障排除。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/15/2019
ms.author: alkohli
ms.openlocfilehash: 8e3c595720d8900acbc5f7f18dc01d96d71ffac9
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569233"
---
# <a name="troubleshoot-your-azure-stack-edge-issues"></a>排查 Azure Stack 边缘问题 

本文介绍如何解决 Azure Stack 边缘上的问题。 

在本文中，学习如何：

> [!div class="checklist"]
> * 运行诊断
> * 收集支持包
> * 使用日志进行故障排除


## <a name="run-diagnostics"></a>运行诊断

若要诊断和排查任何设备错误，可以运行诊断测试。 在设备的本地 Web UI 中执行以下步骤，以运行诊断测试。

1. 在本地 Web UI 中，转到“故障排除”>“诊断测试”。**** 选择要运行的测试，然后单击“运行测试”。**** 这会运行测试来诊断网络、设备、Web 代理、时间或云设置可能存在的任何问题。 你将收到设备正在运行测试的通知。

    ![选择测试](media/azure-stack-edge-troubleshoot/run-diag-1.png)
 
2. 测试完成后，会显示结果。 

    ![查看测试结果](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    如果未通过某项测试，会显示建议的措施的 URL。 可以单击该 URL 查看建议的措施。
 
    ![查看未通过测试的警告](media/azure-stack-edge-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>收集支持包

日志包由所有可帮助 Microsoft 支持部门排查任何设备问题的相关日志组成。 可以通过本地 Web UI 生成日志包。

执行以下步骤收集支持包。 

1. 在本地 Web UI 中，转到“故障排除”>“支持”。**** 单击“创建支持包”。**** 系统随即开始收集支持包。 收集包可能需要几分钟时间。

    ![单击“添加用户”](media/azure-stack-edge-troubleshoot/collect-logs-1.png)
 
2. 创建支持包后，单击“下载支持包”。**** 压缩包将下载到所选的路径。 可以解压缩该包并查看系统日志文件。

    ![单击“添加用户”](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>使用日志进行故障排除

相关错误文件中包含上传和刷新过程中遇到的任何错误。

1. 若要查看错误文件，请转到共享，然后单击该共享以查看内容。 

      ![连接到共享内容并查看](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. 单击 " _Microsoft Azure Stack 边缘" 文件夹_。 此文件夹包含两个子文件夹：

    - Upload 文件夹：包含上传错误的日志文件。
    - Refresh 文件夹：包含刷新期间发生的错误。

    下面是 refresh 文件夹中的日志文件示例。

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 如果此文件中包含错误（在示例中已突出显示），请记下错误代码（在本例中为 16001）。 根据以下错误参考查找此错误代码的说明。

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>后续步骤

- 详细了解[此版本中的已知问题](data-box-gateway-release-notes.md)。
