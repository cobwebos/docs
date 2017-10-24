## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
本部分的操作：

* 创建一个 Node.js 控制台应用，用于响应通过云调用的直接方法
* 触发模拟固件更新
* 使用报告属性使设备孪生查询能够识别设备及其上次完成固件更新的时间

1. 创建名为 **manageddevice** 的空文件夹。  在 **manageddevice** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```

2. 在 **manageddevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iot-device** 和 **azure-iot-device-mqtt** 设备 SDK 包：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. 在 **manageddevice** 文件夹中，利用文本编辑器创建 **dmpatterns_fwupdate_device.js** 文件。

4. 在 **dmpatterns_fwupdate_device.js** 文件开头添加以下“require”语句：
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. 添加 **connectionString** 变量，并使用它创建一个**客户端**实例。 将 `{yourdeviceconnectionstring}` 占位符替换为此前在“创建设备标识”部分记下的连接字符串：
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. 添加用于更新报告属性的以下函数：
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```

7. 添加用于模拟固件映像的下载和应用的以下函数：
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```

8. 添加通过报告属性将固件更新状态更新为“正在等待”的以下函数。 通常，设备会收到有关可用更新的通知，并且管理员定义的策略会使设备开始下载和应用更新。 此函数是用于启用该策略的逻辑运行的位置。 为简单起见，该示例在开始下载固件映像之前会等待四秒：
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```

9. 添加通过报告属性将固件更新状态更新为“正在下载”的以下函数。 然后，该函数会模拟固件下载，并最终将固件更新状态更新为“downloadFailed”或“downloadComplete”：
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```

10. 添加通过报告属性将固件更新状态更新为“正在应用”的以下函数。 然后，该函数会模拟固件映像应用，并最终将固件更新状态更新为“applyFailed”或“applyComplete”：
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```

11. 添加处理 **firmwareUpdate** 直接方法并启动多阶段固件更新过程的以下函数：
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = request.payload.fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```

12. 最后，添加连接到 IoT 中心的以下代码：
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling](https://msdn.microsoft.com/library/hh675232.aspx)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
> 
> 