### <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序
* 创建一个名为 `sender.js` 的新 JavaScript 文件。

### <a name="add-the-relay-npm-package"></a>添加中继 NPM 包
* 从项目文件夹中的 Node 命令提示符运行 `npm install hyco-ws`。

### <a name="write-some-code-to-send-messages"></a>编写一些代码来发送消息
1. 在 `sender.js` 文件的顶部，添加以下`constants`。
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. 将以下中继`constants`添加到 `sender.js`，用于保存混合连接的连接详细信息。 将括号中的占位符替换为在创建混合连接时获得的相应值。
   
   1. `const ns` - 中继命名空间（使用 FQDN - 例如 `{namespace}.servicebus.windows.net`）
   2. `const path` - 混合连接的名称
   3. `const keyrule` - SAS 密钥的名称
   4. `const key` - SAS 密钥值
3. 将以下代码添加到 `sender.js` 文件：
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    listener.js 文件的内容如下所示：
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

