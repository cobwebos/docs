---
title: "桥 Android WebView 与本机 Mobile Engagement Android SDK"
description: "描述如何创建 WebView 运行 Javascript 和本机 Mobile Engagement Android SDK 之间的桥梁"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>桥 Android WebView 与本机 Mobile Engagement Android SDK
> [!div class="op_single_selector"]
> * [Android 桥](mobile-engagement-bridge-webview-native-android.md)
> * [iOS 桥](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

某些移动应用程序，旨在用作其中使用本机 Android 开发开发该应用程序自身但 Android WebView 中呈现的部分或甚至全部屏幕的混合应用。 你仍可以在此类应用程序中使用 Mobile Engagement Android SDK 和本教程介绍如何执行此操作。 下面的示例代码基于 Android 文档[此处](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)。 它介绍如何无法使用此有案可稽的方法来实现相同的移动服务 Android SDK 的常用方法，以便从混合应用 Webview 还可以通过我们的 Android SDK 通过管道将它们同时启动到跟踪事件、 作业、 错误、 应用程序信息的请求。 

1. 首先，你需要确保你已经通过我们[入门教程](mobile-engagement-android-get-started.md)集成 Mobile Engagement 混合应用程序中的 Android SDK。 一旦你做，你`OnCreate`方法将如下所示。  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. 现在，请确保你的混合应用程序对其具有与 WebView 屏幕。 它的代码将类似于以下我们要在其中加载本地的 HTML 文件**Sample.html**中 Webview 中`onCreate`屏幕的方法。 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. 现在，创建名为的桥文件**WebAppInterface**这对某些中通常创建包装器使用 Mobile Engagement Android SDK 方法使用`@JavascriptInterface`方法中所述[Android 文档](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. 一旦我们已经创建了上述的桥文件，我们需要确保与我们 Webview 关联。 对于这种情况，你需要编辑你`SetWebview`方法，以便它如下所示：
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. 在上面的段中，我们调用`addJavascriptInterface`要与我们 Webview 关联我们桥的类和还创建名为的句柄**EngagementJs**从桥文件调用方法。 
6. 现在创建以下文件调用**Sample.html**在你的项目文件夹中名为**资产**它将被加载到 Webview 和我们将从桥文件调用的方法。
   
        <!doctype html>
        <html>
            <head>
                <style type='text/css'>
                    html { font-family:Helvetica; color:#222; }
                    h1 { color:steelblue; font-size:22px; margin-top:16px; }
                    h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
                </style>
   
                <script type="text/javascript">
   
                    window.onerror = function(err)
                    {
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. 请注意上面的 HTML 文件有关的以下几点：
   
   * 它包含一组提供作为你事件、 作业、 错误、 应用程序信息的名称使用的数据的位置的输入框。 单击它旁边的按钮时，对 Javascript 的最终要传递到移动服务 Android SDK 此调用的桥文件从调用的方法进行调用。 
   * 我们将标记上对事件、 作业和甚至错误，以演示如何这样做一些静态额外信息。 此额外信息会发送 JSON 字符串，如果你查看`WebAppInterface`文件、 分析并置于 Android`Bundle`和以及发送事件，作业，错误传递。 
   * 移动用户参与策略作业将启动与在输入框中，指定为 10 秒运行和关闭的名称。 
   * Mobile Engagement appinfo 或标记与女士作为静态的密钥，你输入中的值为输入标记的值一起传递。 
8. 运行应用程序，你将看到以下。 现在提供如下所示的测试事件某个名称，然后单击**发送**它下面。 
   
    ![][1]
9. 现在，如果你转到**监视器**的应用和下的外观的选项卡**事件-> 详细信息**，你将看到显示静态应用程序的信息就向您发送以及此事件。 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
