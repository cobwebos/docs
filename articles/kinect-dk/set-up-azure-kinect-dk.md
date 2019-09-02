---
title: 快速入门 - 设置 Azure Kinect DK
description: 本快速入门说明如何设置 Azure Kinect DK 硬件
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 06/26/2019
keywords: azure, kinect, 开发人员工具包, azure dk, 设置, 硬件, 快速, usb, 电源, 查看器, 传感器, 流, 安装, SDK, 固件
ms.openlocfilehash: 0d5af1febfc1ae8dfb7a4ade7f42b70a0bb69ff8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012229"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>快速入门：设置 Azure Kinect DK

本快速入门提供有关如何设置 Azure Kinect DK 的指导。 其中介绍了如何测试传感器流可视化和使用 [Azure Kinect 查看器](azure-kinect-viewer.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="system-requirements"></a>系统要求

查看[系统要求](system-requirements.md)，验证主机电脑配置是否符合所有的 Azure Kinect DK 最低要求。

## <a name="set-up-hardware"></a>设置硬件

> [!NOTE]
> 使用本设备之前，请务必除去相机保护膜。

1. 将电源连接器插入设备背面的电源插孔。 将 USB 电源适配器连接到线缆的另一端，然后将其插入电源插座。
2. 将 USB 数据线的一端连接到设备，将另一端连接到电脑上的 USB 3.0 端口。
   >[!NOTE]
   >使用一根线缆从电脑直接连接到设备背面，这比通过适配器或延长线进行连接的效果更好。

3. 检查 USB 线缆旁边的电源 LED 指示灯是否稳定点亮白色。
4. 设备通电需要几秒钟时间。 当正面的流 LED 指示灯熄灭时，表示设备可供使用。

    ![完整设备功能](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>下载该 SDK

1. 选择[下载 SDK](sensor-sdk-download.md) 的链接。
2. 在电脑上安装 SDK。

## <a name="update-firmware"></a>更新固件

新版本的传感器 SDK 1.2 需要下载附带的固件更新。 可以按照[如何更新固件指南](update-device-firmware.md)中的步骤更新设备固件。

## <a name="verify-that-the-device-streams-data"></a>验证设备是否流式传输数据

1. 启动 [Azure Kinect 查看器](azure-kinect-viewer.md)，可在工具的安装目录中找到它，其文件名为 `k4aviewer.exe`（例如 `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`，其中 `X.Y.Z` 是安装的 SDK 版本）。 可以通过命令行或者双击相应的可执行文件启动查看器。 开始菜单中也提供了其链接。
2. 依次选择“打开设备”、“启动”。  

    ![Azure Kinect 查看器](./media/quickstarts/viewer.png)

3. 在工具中验证是否可视化了每个传感器流。
    - 深度相机
    - 彩色相机
    - 红外相机
    - IMU
    - 麦克风

    ![可视化工具](./media/quickstarts/visualization-tool.png)

4. 现已完成 Azure Kinect DK 的设置。 接下来，可以开始开发应用程序或集成服务。

如果遇到任何问题，请查看[故障排除](troubleshooting.md)。

## <a name="see-also"></a>另请参阅

[Azure Kinect DK 硬件信息](hardware-specification.md)

[更新设备固件](update-device-firmware.md)

详细了解 [Azure Kinect 查看器](azure-kinect-viewer.md)

## <a name="next-steps"></a>后续步骤

Azure Kinect DK 准备就绪并运行后，你还可以了解如何
> [!div class="nextstepaction"]
> [将传感器流录制到文件中](record-sensor-streams-file.md)
