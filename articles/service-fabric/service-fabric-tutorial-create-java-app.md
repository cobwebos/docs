---
title: 创建适用于 Service Fabric 的 Java 应用程序 | Microsoft Docs
description: 本教程介绍如何创建一个包含前端的 Reliable Services Java 应用程序，并创建 Reliable Services 有状态后端，然后将该应用程序部署到群集。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b512ba91d1df7ec0432bdf9048268714e570fe6b
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958670"
---
# <a name="tutorial-create-and-deploy-an-application-with-a-java-web-api-front-end-service-and-a-stateful-back-end-service"></a>教程：使用 Java Web API 前端服务和有状态后端服务创建并部署应用程序
本教程是一个系列中的第一部分。 完成后，将生成一个带 Java Web 前端的 Voting 应用程序，用于将投票结果保存到群集的有状态后端服务中。 本教程系列要求你有一台工作的 Mac OSX 或 Linux 开发人员计算机。 如果不想手动创建投票应用程序，可以[下载已完成应用程序的源代码](https://github.com/Azure-Samples/service-fabric-java-quickstart)，跳到[大致了解投票示例应用程序](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application)。

![本地 Voting 应用](./media/service-fabric-tutorial-create-java-app/votingjavalocal.png)

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建 Java 有状态 Reliable Services
> * 创建 Java 无状态 Web 应用程序服务
> * 使用服务远程处理功能与有状态服务通信
> * 将应用程序部署到本地 Service Fabric 群集

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> *  生成 Java Service Fabric Reliable Services 应用程序
> * [在本地群集上部署和调试应用程序](service-fabric-tutorial-debug-log-local-cluster.md)
> * [将应用程序部署到 Azure 群集](service-fabric-tutorial-java-deploy-azure.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-java-elk.md)
> * [设置 CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 设置适用于 [Mac](service-fabric-get-started-mac.md) 或 [Linux](service-fabric-get-started-linux.md) 的开发环境。 遵照说明安装 Eclipse 插件、Gradle、Service Fabric SDK 和 Service Fabric CLI (sfctl)。

## <a name="create-the-front-end-java-stateless-service"></a>创建前端 Java 无状态服务
首先，请创建 Voting 应用程序的 Web 前端。 此 Java 无状态服务支持的轻型 HTTP 服务器可托管受 AngularJS 支持的 Web UI。 来自用户的请求由这个无状态服务处理，然后作为远程过程调用发送到有状态服务，以便存储投票。 

1. 启动 Eclipse

2. 通过单击“文件”->“新建”->“其他”->“Service Fabric”->“Service Fabric 项目”来创建项目

    ![Eclipse 中的“新建项目”对话框](./media/service-fabric-tutorial-create-java-app/create-sf-proj-wizard.png)

3. 在“ServiceFabric 项目向导”对话框中，将项目命名为 Voting，然后单击“下一步”

    ![在新建服务对话框中选择 Java 无状态服务](./media/service-fabric-tutorial-create-java-app/name-sf-proj-wizard.png) 

4. 在“添加服务”页中，选择“无状态服务”，然后将服务命名为“VotingWeb”。 单击“完成”以创建该项目。
   
    ![创建无状态服务]( ./media/service-fabric-tutorial-create-java-app/createvotingweb.png)

    Eclipse 会创建应用程序和服务项目，并在包资源管理器中显示它们。

    ![创建应用程序之后的 Eclipse 包资源管理器]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

下表对上一屏幕截图中包资源管理器的每个项目进行了简短的说明。 
| **包资源管理器项目** | **说明** |
| --- | --- |
| PublishProfiles | 包含的 JSON 文件介绍了本地和 Azure Service Fabric 群集的配置文件详细信息。 部署此应用程序时，这些文件的内容供插件使用。 |
| 脚本 | 包含帮助程序脚本。可以通过命令行使用这些脚本，以便通过群集快速管理应用程序。 |
| VotingApplication | 包含的 Service Fabric 应用程序可以推送到 Service Fabric 群集。 |
| VotingWeb | 包含前端无状态服务源文件，以及相关的 gradle 生成文件。 |
| build.gradle | 用于管理项目的 Gradle 文件。 |
| settings.gradle | 包含此文件夹中 Gradle 项目的名称。 |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>将 HTML 和 Javascript 添加到 VotingWeb 服务
若要添加可以通过无状态服务来呈现的 UI，请在 *VotingApplication/VotingWebPkg/Code* 中添加 HTML 文件。 然后，通过已嵌入到无状态 Java 服务中的轻型 HTTP 服务器呈现此 HTML 文件。 

1. 展开 *VotingApplication* 目录，以便访问 *VotingApplication/VotingWebPkg/Code* 目录。 

2. 右键单击 *Code* 目录，然后单击“新建”->“其他”

3. 创建名为 *wwwroot* 的文件夹，然后单击“完成”

    ![Eclipse 创建 wwwroot 文件夹](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. 将名为 **index.html** 的文件添加到 **wwwroot**，然后将以下内容粘贴到该文件夹中。

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="http://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']); 
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
          {},
          ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {            
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}        
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });        
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebservicejava-file"></a>更新 VotingWebService.java 文件
在 **VotingWeb** 子项目中，打开 *VotingWeb/src/statelessservice/VotingWebService.java* 文件。 **VotingWebService** 是通往无状态服务的网关，负责设置前端 API 的通信侦听器。 

将文件中 **createServiceInstanceListeners** 方法的内容替换为以下内容，然后保存所做的更改。

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();
    
    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>添加 HTTPCommunicationListener.java 文件
HTTP 通信侦听器充当一个控制器，可设置 HTTP 服务器并公开用于定义投票操作的 API。 右键单击 *VotingWeb/src/statelessservice* 文件夹中的“statelessservice”包，选择“新建”->“其他...”->“常规”->“文件”，然后单击“下一步”。  将文件命名为 *HttpCommunicationListener.java*，然后单击“完成”。  

将文件内容替换为以下内容，然后保存所做更改。  稍后在[更新 HttpCommunicationListener.java 文件](#updatelistener_anchor)时会修改此文件，以便呈现、读取和写入来自后端服务的投票数据。  目前，此侦听器会直接返回 Voting 应用的静态 HTML。

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());
    
    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404; 
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";  
    private static final String ENCODING = "UTF-8";
    
    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0); 
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated 
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }
        
        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();
    
                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {    
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);              
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }
                      
                      fs.close();
                      os.close();
                    }  
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */
        
        server.setExecutor(null);
        server.start();
    }
    
    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>配置侦听端口
创建 VotingWebService 前端服务后，Service Fabric 会选择一个可供服务侦听的端口。  VotingWebService 充当此应用程序的前端并接受外部流量，因此让我们将此服务绑定到已知的固定端口。 在包资源管理器中，打开 *VotingWebService/VotingWebServicePkg/ServiceManifest.xml*。  在“资源”部分找到“终结点”资源，然后将“端口”值更改为 8080 或其他端口。 若要在本地部署和运行应用程序，应用程序侦听端口必须为打开状态且在你的计算机上可用。 将以下代码片段粘贴到 **ServiceManifest** 标记下。

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```  

## <a name="add-a-stateful-back-end-service-to-your-application"></a>向应用程序添加有状态后端服务
Java Web API 服务的框架完成后，即可继续操作，去完成有状态后端服务。

Service Fabric 允许使用 Reliable Collections 直接在服务内以一致、可靠的方式存储数据。 Reliable Collections 是一组高度可用的可靠集合类。 用过 Java 集合的用户都对这些类的使用很熟悉。

1. 在包资源管理器中，右键单击应用程序项目中的“Voting”，然后选择“Service Fabric”>“添加 Service Fabric 服务”。
   
2. 在“添加服务”对话框中，选择“有状态服务”，将服务命名为“VotingData”，然后单击“添加服务”。 

    ![将一个新服务添加到现有应用程序](./media/service-fabric-tutorial-create-java-app/addstatefuljava.png)

    创建服务项目后，应用程序中会有两个服务。 随着继续生成应用程序，可采用相同的方式添加更多服务。 每个服务都可以单独进行版本控制和升级。

3. Eclipse 会创建服务项目，并在包资源管理器中显示该项目。

    ![解决方案资源管理器](./media/service-fabric-tutorial-create-java-app/packageexplorercompletejava.png)

### <a name="add-the-votingdataservicejava-file"></a>添加 VotingDataService.java 文件

*VotingDataService.java* 文件包含多种方法，其中的逻辑可用于在 Reliable Collections 中检索、添加和删除投票。 将以下方法添加到已创建的 *VotingDataService/src/statefulservice/VotingDataService.java* 文件中的 **VotingDataService** 类。

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List; 
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext; 

import rpcmethods.VotingRPC; 

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;
    
    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();
        
        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        
        return listeners;
    }
    
    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {                    

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
                        
            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue()); 
            }
            
            tx.close();                    
            

        } catch (Exception e) {
            e.printStackTrace();
        }  
        
        return CompletableFuture.completedFuture(tempMap);
    }
    
    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1); 

        try {
            
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();                    
            
            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;                            
                    return Integer.toString(numVotes);
                }
            }).get(); 
            
            tx.commitAsync().get();
            tx.close(); 

            status.set(1);                            
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1); 
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
            
            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();                    
            
            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
}
```

## <a name="create-the-communication-interface-to-your-application"></a>创建应用程序的通信接口 
现在已创建前端无状态服务和后端服务的框架。 下一步是连接这两项服务。 前端和后端服务都利用一个名为 VotingRPC 的接口来定义 Voting 应用程序的操作。 此接口由前端和后端服务来共同实现，用于在这两项服务之间进行远程过程调用 (RPC)。 由于 Eclipse 不支持添加 Gradle 子项目，因此必须手动添加包含此接口的包。 

1. 在包资源管理器中右键单击“Voting”项目，然后单击“新建”->“其他...”。

2. 在向导中单击“常规”->“文件夹”，然后将文件夹命名为 **VotingRPC/src/rpcmethods** 

    ![创建 VotingRPC 包](./media/service-fabric-tutorial-create-java-app/createvotingrpcpackage.png)

3. 在 *Voting/VotingRPC/src/rpcmethods* 下创建名为 *VotingRPC.java* 的文件，然后将以下内容粘贴到该 **VotingRPC.java** 文件中。 

    ```java
    package rpcmethods; 
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ``` 

4. 在 *Voting/VotingRPC* 目录中创建名为 *build.gradle* 的文件，并将以下内容粘贴到其中。 此 gradle 文件用于生成和创建由其他服务导入的 jar 文件。 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar'
    ```

5. 在 *Voting/settings.gradle* 文件中添加一行内容，以便将新创建的项目包括到内部版本中。 

    ```gradle 
    include ':VotingRPC'
    ```

6. 在 *Voting/VotingWebService/src/statelessservice/HttpCommunicationListener.java* 文件中，将注释块替换为以下内容。  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {                    
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
                
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));                   
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();     
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");                    
                
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
                
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");
                
                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. 将适当的 import 语句添加到 *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* 文件顶部。 

    ```java
    import rpcmethods.VotingRPC; 
    ```

在此阶段，前端、后端和 RPC 接口的功能都已完成。 下一阶段是在部署到 Service Fabric 群集之前，先正确配置 Gradle 脚本。 

## <a name="walk-through-the-voting-sample-application"></a>大致了解投票示例应用程序
投票应用程序由以下两个服务组成：
- Web 前端服务 (VotingWeb) - Java Web 前端服务，可提供网页服务，并公开用于与后端服务通信的 API。
- 后端服务 (VotingDataService) - Java Web 服务，其定义的方法可以通过远程过程调用 (RPC) 来调用，以便保存投票结果。

![应用程序关系图](./media/service-fabric-tutorial-create-java-app/walkthroughjavavoting.png)

在应用程序中执行操作（添加项目、投票、删除项目）时，会发生以下事件：
1. JavaScript 将相应的请求作为 HTTP 请求发送给 Web 前端服务中的 Web API。

2. Web 前端服务使用 Service Fabric 的内置服务远程处理功能来查找请求，并将其转发给后端服务。 

3. 后端服务定义的方法用于更新可靠字典中的结果。 此可靠字典的内容复制到群集内的多个节点，并保存在磁盘上。 应用程序的所有数据都存储在群集中。 

## <a name="configure-gradle-scripts"></a>配置 Gradle 脚本 

此部分配置项目的 Gradle 脚本。 

1. 将 *Voting/build.gradle* 文件的内容替换为以下内容。

    ```gradle 
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    } 
        
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. 替换 *Voting/VotingWeb/build.gradle* 文件的内容。

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ``` 

3. 替换 *Voting/VotingData/build.gradle* 文件的内容。 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>将应用程序部署到本地群集
现在可以将应用程序部署到本地 Service Fabric 群集了。

1. 在包资源管理器中右键单击“Voting”项目，然后单击“Service Fabric”->“生成应用程序”以生成应用程序。

2. 运行本地 Service Fabric 群集。 此步骤取决于开发环境（Mac 或 Linux）。

    如果运行的是 Mac，则请通过以下命令运行本地群集：将传入到 **-v** 参数中的命令替换为你自己的工作区的路径。

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox servicefabricoss/service-fabric-onebox
    ``` 

    如果是在 Linux 计算机上运行，请通过以下命令启动本地群集： 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

4. 在适用于 Eclipse 的包资源管理器中右键单击“Voting”项目，然后单击“Service Fabric”->“发布应用程序...” 
5. 在“发布应用程序”窗口的下拉列表中，选择“Local.json”并单击“发布”。
6. 转到 Web 浏览器并访问 **http://localhost:8080**，以查看在本地 Service Fabric 群集上运行的应用程序。 

## <a name="next-steps"></a>后续步骤
本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 以有状态 Reliable Services 形式创建 Java 服务
> * 以无状态 Web 服务形式创建 Java 服务
> * 添加一个 Java 接口，用于处理服务之间的远程过程调用 (RPC)。
> * 配置 Gradle 脚本
> * 生成应用程序并将其部署到本地 Service Fabric 群集

转到下一教程：
> [!div class="nextstepaction"]
> [在本地群集上调试和记录应用程序](service-fabric-tutorial-debug-log-local-cluster.md)
