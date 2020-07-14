---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060400"
---
### <a name="create-the-react-native-solution"></a>创建 React Native 解决方案

1. 在 `Terminal` 中，使用以下命令更新使用 React Native 所需的环境工具：

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. 如果安装了 `React Native` CLI，请在 `Terminal` 中运行以下命令以卸载它。 使用 `npx` 以自动获取最新可用的 React Native CLI 版本：

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native 具有内置的命令行接口。 与全局安装和管理特定版本的 CLI 相比，我们建议使用 Node.js 随附的 `npx` 在运行时获取当前版本。 通过使用 `npx react-native <command>`，每当此命令运行时，将下载并执行当前稳定版本的 CLI。

1. 导航到要从中创建新应用程序的项目文件夹。 通过指定 `--template` 参数使用基于 Typescript 的模板：

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. 运行 metro server，它将构建 JavaScript 捆绑并监视任何代码更新以实时刷新捆绑：

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. 运行 iOS 应用以验证设置。 执行以下命令之前，请确保启动了 iOS 模拟器或连接了 iOS 设备：

    ```bash
    npx react-native run-ios
    ```

1. 运行 Android 应用以验证设置。 需要执行一些额外的步骤来配置 Android 仿真器或设备，使它能够访问 React Native metro server。 以下命令会生成适用于 Android 的初始 JavaScript 捆绑，并将其放入资产文件夹。

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    此脚本将与应用的初始版本一起预先部署。 部署完成后，你需要指定服务器 IP 地址将仿真器或设备配置为访问 metro server。 执行以下命令来构建并运行 Android 应用程序：

    ```bash
    npx react-native run-android
    ```

    进入应用后，点击 `CMD+M`（模拟器）或晃动设备以填充开发人员设置，接着导航到 `Settings` > `Change Bundle Location`，然后使用默认端口 `<metro-server-ip-address>:8081` 来指定 metro server IP 地址。

1. 在 `App.tsx` 文件中，将任何更改应用于页面布局，然后保存，使更改自动反映在 iOS 和 Android 应用中。

    > [!NOTE]
    > [官方文档](https://reactnative.dev/docs/environment-setup)中提供了详细的开发环境设置指南

### <a name="install-required-packages"></a>安装所需程序包

需要以下三个包才能使此示例正常工作：

1. [React Native Push Notifications iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [Project GitHub](https://github.com/react-native-community/push-notification-ios)

    此包是在 PushNotificationIOS 从 React Native 的核心中被拆分出来时创建的。 此包本机实现 iOS 的推送通知并提供 React Native 接口来访问它。 运行以下命令以安装包：

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [React Native Push Notifications Cross-platform](https://www.npmjs.com/package/react-native-push-notification)

    此包以跨平台的方式在 iOS 和 Android 上实现本地和远程通知。 运行以下命令以安装包：

    ```bash
    yarn add react-native-push-notification
    ```

1. [设备信息包](https://www.npmjs.com/package/react-native-device-info) 此包提供有关运行时设备的信息。 使用它来定义设备标识符，此标识符用于注册推送通知。 运行以下命令以安装包：

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>实现跨平台组件

1. 创建并实现 `DemoNotificationHandler`：

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. 创建并实现 `DemoNotificationService`：

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. 创建并实现 `DemoNotificationRegistrationService`：

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. 配置应用。 打开 `package.json` 并添加以下脚本定义：

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    然后执行此脚本，它会将默认配置复制到 `config` 文件夹中。

    ```bash
    yarn configure
    ```

    最后一步是使用 API 访问信息更新在上一步中复制的配置文件。 指定参数 `apiKey` 和 `apiUrl`：

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>实现跨平台 UI

1. 定义页面布局

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. 应用样式

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. 初始化页面组件

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. 定义按钮单击处理程序

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. 处理收到的令牌注册和推送通知

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```