---
title: "使用 C# 创建 Azure IoT Edge 模块 | Microsoft Docs"
description: "本教程演示如何使用最新的 Azure IoT Edge NuGet 包、Visual Studio Code 和 C# 编写 BLE 数据转换器模块。"
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "azure, iot, 教程, 模块, nuget, vscode, csharp, edge"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>使用 C&#x23; 创建 Azure IoT Edge 模块

本教程演示如何使用 `Visual Studio Code` 和 `C#` 为 `Azure IoT Edge` 创建模块。

本教程将演练环境设置，以及如何使用最新的 `Azure IoT Edge NuGet` 包编写 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 数据转换器模块。 

>[!NOTE]
本教程使用支持跨平台兼容性的 `.NET Core SDK`。 以下教程是使用 `Windows 10` 操作系统编写的。 根据所用的 `development environment`，本教程中的某些命令可能不同。 

## <a name="prerequisites"></a>先决条件

本部分设置 `Azure IoT Edge` 模块开发环境。 内容适用于 64 位 Windows 和 64 位 Linux (Ubuntu/Debian 8) 操作系统。

需要以下软件：

- [Git 客户端](https://git-scm.com/downloads)
- [.NET Core SDK](https://www.microsoft.com/net/core#windowscmd)
- [Contact.java](https://code.visualstudio.com/)

不需要克隆本示例的存储库，但是，以下存储库中已提供本教程所述的所有示例代码：

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`。
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>入门

1. 安装 `.NET Core SDK`。
2. 从 Visual Studio Code Marketplace 安装 `Visual Studio Code` 和 `C# extension`。

观看此[快速教程视频](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows)，了解如何开始使用 `Visual Studio Code` 和 `.NET Core SDK`。

## <a name="creating-the-azure-iot-edge-converter-module"></a>创建 Azure IoT Edge 转换器模块

1. 初始化新的 `.NET Core` 类库 C# 项目：
    - 打开命令提示符 (`Windows + R` -> `cmd` -> `enter`)。
    - 导航到要在其中创建 `C#` 项目的文件夹。
    - 键入 **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**。 
    - 此命令在 projects 目录中创建名为 `Class1.cs` 的空类。
2. 键入 **cd IoTEdgeConverterModule**，导航到在其中创建了类库项目的文件夹。
3. 键入 **code** 在 `Visual Studio Code` 中打开该项目。
4. 在 `Visual Studio Code` 中打开项目后，请单击“IoTEdgeConverterModule.csproj”打开下图所示的文件：

    ![Visual Studio Code 编辑窗口](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. 在 `PropertyGroup` 结束标记与 `Project` 结束标记之间插入以下代码片段中所示的 `XML` Blob（注意上图中的第六行），然后按 `Ctrl` + `S` 保存文件。

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. 保存 `.csproj` 文件后，`Visual Studio Code` 应会通过一个 `unresolved dependencies` 对话框来提示你，如下图所示： 

    ![Visual Studio Code 中的还原依赖项对话框](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) 单击 `Restore` 还原项目 `.csproj` 文件中的所有引用，包括我们添加的 `PackageReferences`。 

    b) `Visual Studio Code` 会自动在项目的 `obj` 文件夹中创建 `project.assets.json` 文件。 此文件包含项目中用于加速后续还原速度的依赖项的相关信息。
 
    >[!NOTE]
    `.NET Core Tools` 现在基于 MSBuild。 这意味着，将会创建 `.csproj` 项目文件而不是 `project.json`。

    - 如果 `Visual Studio Code` 未提示已成功创建该文件，我们可以手动执行此操作。 按 `Ctrl` + `backtick` 键或使用菜单 `View` -> `Integrated Terminal` 打开 `Visual Studio Code` 集成终端窗口。
    - 在 `Integrated Terminal` 窗口中键入 **dotnet restore**。
    
7. 将 `Class1.cs` 文件重命名为 `BleConverterModule.cs`。 

    a) 若要重命名文件，请先单击该文件，然后按 `F2` 键。
    
    b) 键入新名称 **BleConverterModule**，如下图所示：

    ![在 Visual Studio Code 中重命名类](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. 通过将以下代码片段复制并粘贴到 `BleConverterModule.cs` 文件来替换 `BleConverterModule.cs` 文件中的现有代码。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. 按 `Ctrl` + `S` 保存文件。

10. 按 `Ctrl` + `N` 键创建名为 `Untitled-1` 的新文件，如下图所示：

    ![在 Visual Studio Code 中新建文件](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. 若要反序列化从 `BLE` 模拟设备收到的 `JSON` 对象，请将以下代码复制到 `Untitled-1` 文件代码编辑器窗口。 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. 按 `Ctrl` + `Shift` + `S` 键将文件另存为 `BleData.cs`。
    - 在“另存为”对话框中的 `Save as Type` 下拉菜单内，选择 `C# (*.cs;*.csx)`，如下图所示：

    ![Visual Studio Code 中的“另存为”对话框](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. 按 `Ctrl` + `N` 键创建名为 `Untitled-1` 的新文件。

14. 将以下代码片段复制并粘贴到 `Untitled-1` 文件中。 此类是一个 `Azure IoT Edge` 模块，我们将使用它来输出从 `BleConverterModule` 收到的数据。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. 按 `Ctrl` + `Shift` + `S` 将文件另存为 `DotNetPrinterModule.cs`。
    - 在“另存为”对话框中的 `Save as Type` 下拉菜单内，选择 `C# (*.cs;*.csx)`。

16. 按 `Ctrl` + `N` 键创建名为 `Untitled-1` 的新文件。

17. 若要反序列化从 `BleConverterModule` 收到的 `JSON` 对象，请将以下代码片段复制并粘贴到 `Untitled-1` 文件。 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. 按 `Ctrl` + `Shift` + `S` 将文件另存为 `BleConverterData.cs`。
    - 在“另存为”对话框中的 `Save as Type` 下拉菜单内，选择 `C# (*.cs;*.csx)`。

19. 按 `Ctrl` + `N` 键创建名为 `Untitled-1` 的新文件。

20. 将以下代码片段复制并粘贴到 `Untitled-1` 文件中。

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. 按 `Ctrl` + `Shift` + `S` 将文件另存为 `gw-config.json`。
    - 在“另存为”对话框中的 `Save as Type` 下拉菜单内，选择 `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`。

22. 若要启用向输出目录复制配置文件，请使用以下 XML Blob 更新 `IoTEdgeConverterModule.csproj`：

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - 更新的 `IoTEdgeConverterModule.csproj` 应如下图所示：

    ![Visual Studio Code 中已更新的 .csproj 文件](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. 按 `Ctrl` + `N` 键创建名为 `Untitled-1` 的新文件。

24. 将以下代码片段复制并粘贴到 `Untitled-1` 文件中。

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. 按 `Ctrl` + `Shift` + `S` 将文件另存为 `binplace.ps1`。
    - 在“另存为”对话框中的 `Save as Type` 下拉菜单内，选择 `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`。

26. 按 `Ctrl` + `Shift` + `B` 键生成项目。 首次生成项目时，`Visual Studio Code` 会使用 `No build task defined.` 对话框提示你，如下图所示：

    ![Visual Studio Code 中的生成任务对话框](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) 单击 `Configure Build Task` 按钮。

    b) 在 `Select a Task Runner` 对话框下拉菜单中， 选择 `.NET Core`，如下图所示： 

    ![Visual Studio Code 中的选择任务对话框](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) 单击 `.NET Core` 项在 `.vscode` 目录中创建 `tasks.json` 文件，然后在 `code editor` 窗口中打开该文件。 不需要修改此文件，因此请关闭选项卡。

27.  按 `Ctrl` + `backtick` 键或使用菜单 `View` -> `Integrated Terminal` 打开 `Visual Studio Code` 集成终端窗口，然后在 `PowerShell` 命令提示符中键入 **.\binplace.ps1**。 此命令将我们的所有依赖项复制到输出目录。

28. 键入 **cd .\bin\Debug\netstandard1.3**，在 `Integrated Terminal` 窗口中导航到 projects 输出目录。

29. 在 `Integrated Terminal` 窗口提示符中键入 **.\gw.exe gw-config.json** 运行示例项目。 
    - 如果已严格遵照本教程中的步骤，则现在应已运行 `Azure IoT Edge BLE Data Converter Module` 示例项目，如下图所示：
    
        ![在 Visual Studio Code 中运行的模拟设备示例](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - 若要终止应用程序，请按 `<Enter>` 键。

>[!IMPORTANT]
不建议使用 `Ctrl` + `C` 终止 `IoT Edge` 网关应用程序（即 **gw.exe**）， 因为此操作可能会导致该进程异常终止。

