---
title: "使用原生 Mobile Engagement Android SDK 桥接 Android WebView"
description: "介绍如何在运行 Javascript 的 WebView 和原生 Mobile Engagement Android SDK 之间创建网桥"
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
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f


---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>使用原生 Mobile Engagement Android SDK 桥接 Android WebView
> [!div class="op_single_selector"]
> * [Android 网桥](mobile-engagement-bridge-webview-native-android.md)
> * [iOS 网桥](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

一些移动应用被设计为混合应用，其中应用本身是使用原生 Android 开发所开发的，但一些或者甚至所有的屏幕都会在 Android WebView 中呈现。 仍然可以在这些应用中使用 Mobile Engagement Android SDK，本教程将介绍如何进行此操作。 以下代码示例基于[此处](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)的 Android 文档。 本文档介绍了如何使用这种文档化的方法来实现 Mobile Engagement Android SDK 的常用方法，以便在通过 Android SDK 发送事件、作业、错误和应用信息时，混合应用的 Webview 也可以发起跟踪它们的请求。 

1. 首先，需要确保已完成我们的[入门教程](mobile-engagement-android-get-started.md)，以便在混合应用中集成 Mobile Engagement Android SDK。 完成入门教程后，`OnCreate` 方法将如下所示。  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. 现在请确保你的混合应用有一个包含 Web 视图的屏幕。 其代码将类似于以下内容，我们将在屏幕的 `onCreate` 方法的 Webview 中加载本地 HTML 文件 **Sample.html**。 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. 现在创建一个名为 **WebAppInterface** 的网桥文件，它使用 [Android 文档](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)中所述的 `@JavascriptInterface` 方法在一些常用的 Mobile Engagement Android SDK 方法之间创建了一个包装器：
   
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
4. 在创建上述网桥文件后，我们需要确保它与我们的 Webview 关联。 为了实现关联，需要编辑 `SetWebview` 方法，使其看上去如下所示：
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. 在上面的代码段中，我们调用 `addJavascriptInterface` 以将网桥类与 Webview 相关联，还创建了一个名为 **EngagementJs** 的句柄，以便从网桥文件中调用这些方法。 
6. 现在，在项目中名为 **assets** 的文件夹中创建一个名为 **Sample.html** 的以下文件，该文件将加载到 Webview 中并且我们会从其中调用网桥文件的方法。
   
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
7. 关于上述 HTML 文件，请注意以下几点：
   
   * 它包含一组输入框，你可以在其中提供要用作事件、作业、错误和应用信息的名称的数据。 在单击它旁边的按钮时，将调用 Javascript，最终调用桥接文件中的方法将此调用传递到 Mobile Engagement Android SDK。 
   * 我们对事件、作业和甚至是错误标记某些静态额外信息，以演示这是如何完成的。 如果查看 `WebAppInterface` 文件，此额外信息会以 JSON 字符串形式发送，JSON 字符串会经过解析并放入 Android `Bundle`中，并在发送事件、作业和错误时一起传递。 
   * 启动一个 Mobile Engagement 作业，名称为你在输入框中指定的名称，运行 10 秒并关闭。 
   * 系统会传递 Mobile Engagement 应用信息或标记，以“customer name”作为静态密钥，并将你在输入框中输入的值作为标记的值。 
8. 运行该应用，你将看到以下内容。 现在为测试事件提供一些名称，如下所示，然后单击其下方的“**发送**”。 
   
    ![][1]
9. 如果你现在转到应用的“**监视器**”选项卡，查看“**事件 -> 详细信息**”，你将看到此事件以及我们发送的静态应用信息。 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png



<!--HONumber=Nov16_HO3-->


