# YARP

> 前言：此文以<a href="https://github.com/microsoft/reverse-proxy" target="_blank">YARP</a> 1.0.0（目前最新<a href="https://microsoft.github.io/reverse-proxy/articles/getting-started.html" target="_blank">1.1.0</a>）進行繁體中文版翻譯。因譯者只是順手翻譯，並未進行很嚴謹的中文內容校正，如發現誤譯或錯字可發PR過來。有些範例或程式碼的小錯誤，是1.0.0版文件本身就錯誤，嗯…我時間有限，就讓它原汁原味吧，如果你看不下去，一樣發PR過來。提供此文，只是單純想要降低讓各位進入YARP的世界的門檻。如果疑問，請向YARP官方反應，或參考原文文件為主。

* 1. [進入YARP](#YARP)
	* 1.1. [Configuration Files](#ConfigurationFiles)
		* 1.1.1. [Routes](#Routes)
		* 1.1.2. [Clusters](#Clusters)
		* 1.1.3. [完整YARP組態屬性](#YARP-1)
* 2. [擴充：Configuration Providers](#ConfigurationProviders)
	* 2.1. [結構](#)
	* 2.2. [生命週期](#-1)
	* 2.3. [原子性](#-1)
	* 2.4. [重新載入](#-1)
	* 2.5. [範例](#-1)
* 3. [直接轉送](#-1)
	* 3.1. [IHttpForwarder](#IHttpForwarder)
	* 3.2. [範例](#-1)
	* 3.3. [HTTP Client](#HTTPClient)
	* 3.4. [轉換](#-1)
	* 3.5. [錯誤處理](#-1)
* 4. [HTTP Client組態](#HTTPClient-1)
	* 4.1. [IConfiguration](#IConfiguration)
		* 4.1.1. [HttpClient](#HttpClient)
		* 4.1.2. [HttpRequest](#HttpRequest)
	* 4.2. [組態範例](#-1)
	* 4.3. [程式碼組態](#-1)
	* 4.4. [組態HttpClient](#HttpClient-1)
	* 4.5. [自定IForwarderHttpClientFactory](#IForwarderHttpClientFactory)
* 5. [HTTPS & TLS](#HTTPSTLS)
	* 5.1. [TLS Termination](#TLSTermination)
	* 5.2. [TLS tunneling (CONNECT)](#TLStunnelingCONNECT)
	* 5.3. [組態傳入連接](#-1)
	* 5.4. [使用Kestrel的進階TLS過濾器](#KestrelTLS)
	* 5.5. [組態傳出連接](#-1)
* 6. [HTTP Header指南](#HTTPHeader)
	* 6.1. [YARP Header過濾](#YARPHeader)
		* 6.1.1. [Connection, KeepAlive, Close](#ConnectionKeepAliveClose)
		* 6.1.2. [Transfer-Encoding](#Transfer-Encoding)
		* 6.1.3. [TE](#TE)
		* 6.1.4. [Upgrade](#Upgrade)
		* 6.1.5. [Proxy-*](#Proxy-)
		* 6.1.6. [Alt-Svc](#Alt-Svc)
		* 6.1.7. [TraceParent, Request-Id, TraceState, Baggage, Correlation-Context](#TraceParentRequest-IdTraceStateBaggageCorrelation-Context)
	* 6.2. [其他Header指南](#Header)
		* 6.2.1. [Host](#Host)
		* 6.2.2. [X-Forwarded-*, Forwarded](#X-Forwarded-Forwarded)
		* 6.2.3. [X-http-mehtod-override, x-http-method, x-method-override](#X-http-mehtod-overridex-http-methodx-method-override)
		* 6.2.4. [Set-Cookie](#Set-Cookie)
		* 6.2.5. [Location](#Location)
		* 6.2.6. [Server](#Server)
		* 6.2.7. [X-Powered-By](#X-Powered-By)
* 7. [基於Header的Route](#HeaderRoute)
	* 7.1. [優先順序](#-1)
	* 7.2. [組態](#-1)
	* 7.3. [約束（Contract）](#Contract)
		* 7.3.1. [Name](#Name)
		* 7.3.2. [Values](#Values)
		* 7.3.3. [Mode](#Mode)
		* 7.3.4. [IsCaseSensitive](#IsCaseSensitive)
	* 7.4. [範例](#-1)
		* 7.4.1. [情境1 - 精確的Header比對](#1-Header)
		* 7.4.2. [情境2 - 多值](#2-)
		* 7.4.3. [情境3 - 存在](#3-)
		* 7.4.4. [情境4 - 多個Header](#4-Header)
* 8. [驗證和授權](#-1)
	* 8.1. [簡介](#-1)
	* 8.2. [預設](#-1)
	* 8.3. [組態](#-1)
		* 8.3.1. [特殊值](#-1)
	* 8.4. [Flowing Credentials（流動憑證）](#FlowingCredentials)
		* 8.4.1. [Cookie, bearer, API keys](#CookiebearerAPIkeys)
		* 8.4.2. [OAuth2, OpenIdConnect, WsFederation](#OAuth2OpenIdConnectWsFederation)
		* 8.4.3. [Windows, Negotiate, NTLM, Kerbereos](#WindowsNegotiateNTLMKerbereos)
		* 8.4.4. [Client Certificates](#ClientCertificates)
		* 8.4.5. [Swapping authentication types](#Swappingauthenticationtypes)
* 9. [跨來源資源共享 (CORS)](#CORS)
	* 9.1. [簡介](#-1)
	* 9.2. [預設](#-1)
	* 9.3. [組態](#-1)
		* 9.3.1. [DefaultPolicy](#DefaultPolicy)
		* 9.3.2. [取消CORS](#CORS-1)
* 10. [Session關聯性](#Session)
	* 10.1. [概念](#-1)
	* 10.2. [組態](#-1)
		* 10.2.1. [Services和中介軟體註冊](#Services)
		* 10.2.2. [Cluster組態](#Cluster)
		* 10.2.3. [特定Policy組態](#Policy)
	* 10.3. [關聯鍵值](#-1)
	* 10.4. [建立新的關聯性或現有的解決方案](#-1)
	* 10.5. [關聯失敗策略](#-1)
	* 10.6. [請求管道](#-1)
* 11. [負載平衡](#-1)
	* 11.1. [概念](#-1)
	* 11.2. [組態](#-1)
		* 11.2.1. [Services和中介軟體註冊](#Services-1)
		* 11.2.2. [Cluster組態](#Cluster-1)
	* 11.3. [內建策略](#-1)
	* 11.4. [擴展性](#-1)
* 12. [中介軟體](#-1)
	* 12.1. [概念](#-1)
	* 12.2. [預設](#-1)
	* 12.3. [加入中介軟體](#-1)
	* 12.4. [自定義Proxy中介軟體](#Proxy)
	* 12.5. [使用中介軟體](#-1)
		* 12.5.1. [日誌和指標](#-1)
		* 12.5.2. [傳送一則立即的回應](#-1)
		* 12.5.3. [過濾目的地](#-1)
		* 12.5.4. [錯誤處理](#-1)
	* 12.6. [不要使用中介軟體](#-1)
* 13. [請求和回應的轉換](#-1)
	* 13.1. [概念](#-1)
	* 13.2. [預設](#-1)
	* 13.3. [轉換分類](#-1)
	* 13.4. [加入轉換](#-1)
		* 13.4.1. [從組態加入](#-1)
		* 13.4.2. [從程式碼加入](#-1)
	* 13.5. [請求轉換](#-1)
		* 13.5.1. [PathPrefix](#PathPrefix)
		* 13.5.2. [PathRemovePrefix](#PathRemovePrefix)
		* 13.5.3. [PathSet](#PathSet)
		* 13.5.4. [PathPattern](#PathPattern)
		* 13.5.5. [QueryValueParameter](#QueryValueParameter)
		* 13.5.6. [QueryRouteParameter](#QueryRouteParameter)
		* 13.5.7. [QueryRemoveParameter](#QueryRemoveParameter)
		* 13.5.8. [HttpMethodChange](#HttpMethodChange)
		* 13.5.9. [RequestHeadersCopy](#RequestHeadersCopy)
		* 13.5.10. [RequestHeaderOriginalHost](#RequestHeaderOriginalHost)
		* 13.5.11. [RequestHeader](#RequestHeader)
		* 13.5.12. [RequestHeaderRemove](#RequestHeaderRemove)
		* 13.5.13. [RequestHeadersAllowed](#RequestHeadersAllowed)
		* 13.5.14. [X-Forwarded](#X-Forwarded)
		* 13.5.15. [Forwarded](#Forwarded)
		* 13.5.16. [ClientCert](#ClientCert)
	* 13.6. [回應和回應尾端](#-1)
		* 13.6.1. [ResponseHeadersCopy](#ResponseHeadersCopy)
		* 13.6.2. [ResponseHeader](#ResponseHeader)
		* 13.6.3. [ResponseHeaderRemove](#ResponseHeaderRemove)
		* 13.6.4. [ResponseHeadersAllowed](#ResponseHeadersAllowed)
		* 13.6.5. [ResponseTrailersCopy](#ResponseTrailersCopy)
		* 13.6.6. [ResponseTrailer](#ResponseTrailer)
		* 13.6.7. [ResponseTrailerRemove](#ResponseTrailerRemove)
		* 13.6.8. [ResponseTrailersAllowed](#ResponseTrailersAllowed)
	* 13.7. [可擴充性](#-1)
		* 13.7.1. [AddRequestTransform](#AddRequestTransform)
		* 13.7.2. [AddResponseTransform](#AddResponseTransform)
		* 13.7.3. [AddResponseTrailersTransform](#AddResponseTrailersTransform)
		* 13.7.4. [RequestTransform](#RequestTransform)
		* 13.7.5. [ResponseTransform](#ResponseTransform)
		* 13.7.6. [ResponseTrailersTransform](#ResponseTrailersTransform)
		* 13.7.7. [ITransformProvider](#ITransformProvider)
		* 13.7.8. [ITransformFactory](#ITransformFactory)
* 14. [目的地健康檢查](#-1)
	* 14.1. [主動健康檢查](#-1)
		* 14.1.1. [組態檔案範例](#-1)
		* 14.1.2. [程式碼範例](#-1)
		* 14.1.3. [組態](#-1)
		* 14.1.4. [內建策略](#-1)
		* 14.1.5. [設計](#-1)
		* 14.1.6. [可擴充性](#-1)
	* 14.2. [被動健康檢查](#-1)
		* 14.2.1. [組態檔案範例](#-1)
		* 14.2.2. [程式碼範例](#-1)
		* 14.2.3. [組態](#-1)
		* 14.2.4. [內建策略](#-1)
		* 14.2.5. [設計](#-1)
		* 14.2.6. [可擴充性](#-1)
	* 14.3. [Available destination collection](#Availabledestinationcollection)
		* 14.3.1. [組態](#-1)
* 15. [分散式追蹤](#-1)
	* 15.1. [使用自定義追蹤標頭](#-1)
	* 15.2. [傳遞Proxy](#Proxy-1)
	* 15.3. [.NET 5.0之前](#NET5.0)

##  1. <a name='YARP'></a>進入YARP

新增YARP

```dos
dotnet new web -n MyProxy
```

加入套件：

```xml
<ItemGroup> 
 <PackageReference Include="Yarp.ReverseProxy" Version="1.1.0" />
</ItemGroup>
```

加入Service與中介軟體（Middleware）：

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddReverseProxy()
    .LoadFromConfig(builder.Configuration.GetSection("ReverseProxy"));
var app = builder.Build();
app.MapReverseProxy();
app.Run();
```

###  1.1. <a name='ConfigurationFiles'></a>Configuration Files

預設YARP會從組態檔的"`ReverseProxy`"段落載入"**Routes**"與"**Clusters**"來載入設定。

```json
"ReverseProxy": {
    "Routes": {
      "minimumroute": {
        "ClusterId": "minimumcluster",
        "Match": {
          "Path": "{**catch-all}"
        }
      }
    },
    "Clusters": {
      "minimumcluster": {
        "Destinations": {
          "httpbin.org": {
            "Address": "https://httpbin.org/"
          }
        }
      }
    }
  }
```

####  1.1.1. <a name='Routes'></a>Routes

Routes主要組態Route規則，規則是以無序集合（unordered collection）方式執行。當進入的請求符合某條Route規則（`Match`），那麼挑選符合`ClusterId`進行請求轉送的動作。

Routes屬性：

* RouteId：唯一名稱。
* ClusterId：參考至Clusters段落的進入點名稱。
* Match：可以包含`Host`陣列或`Path`模式字串。`Path`可以參考ASP.NET Core route template的<a href="https://docs.microsoft.com/zh-tw/aspnet/core/fundamentals/routing?view=aspnetcore-6.0#route-template-reference" target="_blank">範例</a>。

由上述Routes組態範例來說明：我們建立一組RouteId名稱`minimumroute`的Route規則，不論任何請求（`{**catch-all}`）均轉送至`ClusterId`為`minimumcluster`的目的端去。

其他Header、Authorization、CORS都能在Routes進行組態設定。

####  1.1.2. <a name='Clusters'></a>Clusters

Clusters是無序清單Cluster的集合。Cluster主要包含目的地與地址的集合，其中任何一個都被認為能夠處理指定Route的請求。Proxy將根據Routes和Clusters來處理請求和選擇目的地。

####  1.1.3. <a name='YARP-1'></a>完整YARP組態屬性

```json
{
  // Server基於以下URL進行監聽，必須獨立於以下Route進行組態。
  "Urls": "http://localhost:5000;https://localhost:5001",
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      // 取消以下註解，以隱藏來自runtime和proxy的diagnostic訊息
      // "Microsoft": "Warning",
      // "Yarp" : "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  // ReverseProxy組態
  "ReverseProxy": {
    // Routes告訴proxy要轉送（forward）哪些請求
    "Routes": { 
      "minimumroute" : {
        // 符合任何內容（{**catch-all}）都將Route到minimumcluster目的地去
        "ClusterId": "minimumcluster",
        "Match": {
          "Path": "{**catch-all}"
        }
      },
      "allrouteprops" : {
        // 符合"/something/*"模式Route到allclusterprops目的地去
        "ClusterId": "allclusterprops", // clusters的其中之一的名稱
        "Order" : 100, // 較低數字擁有較高的優先權
        "Authorization Policy" : "Anonymous", // 授權策略名稱或"Default"或"Anonymous"
        "CorsPolicy" : "Default", // 套用於此Route的CorsPolicy名稱，或"Default"或"Disable"
        "Match": {
          "Path": "/something/{**remainder}", // 使用ASP.NET語法來比對的Path
          "Hosts" : [ "www.aaaaa.com", "www.bbbbb.com"], // 要比對的Host名稱，未指定是any
          "Methods" : [ "GET", "PUT" ], // 要比對的HTTP動詞，未指定是all
          "Headers": [ // 要比對的HTTP Header，未指定是any
            {
              "Name": "MyCustomHeader", // Header名稱
              "Values": [ "value1", "value2", "another value" ], // 比對是否為這些值的其中一個
              "Mode": "ExactHeader", // 還有以下模式可選擇："HeaderPrefix"、"Exists"、"Contains"、"NotContains"
              "IsCaseSensitive": true
            }
          ],
          "QueryParameters": [ // 要比對的QueryParameter，未指定是any
            {
              "Name": "MyQueryParameter", // QueryParameters的名稱
              "Values": [ "value1", "value2", "another value" ], // 比對是否為這些值的其中一個
              "Mode": "Exact", // 還有以下模式可選擇："Prefix"、"Exists"、"Contains"、"NotContains"
              "IsCaseSensitive": true
            }
          ]
        },
        "MetaData" : { // 自定擴充，可以使用key-value清單清單
          "MyName" : "MyValue"
        },
        "Transforms" : [ // 轉換清單清單。細節查詢Transforms.html頁面
          {
            "RequestHeader": "MyHeader",
            "Set": "MyValue",
          } 
        ]
      }
    },
    // Clusters告訴proxy在何處及如何轉送請求
    "Clusters": {
      "minimumcluster": {
        "Destinations": {
          "example.com": {
            "Address": "http://www.example.com/"
          }
        }
      },
      "allclusterprops": {
        "Destinations": {
          "first_destination": {
            "Address": "https://contoso.com"
          },
          "another_destination": {
            "Address": "https://10.20.30.40",
            "Health" : "https://10.20.30.40:12345/test" // 覆寫主動的健康檢查
          }
        },
        "LoadBalancingPolicy" : "PowerOfTwoChoices", // 還有以下選項："FirstAlphabetical", "Random", "RoundRobin", "LeastRequests"
        "SessionAffinity": {
          "Enabled": true, // 預設為'false'
          "Policy": "Cookie", // 預設Default，或設定"CustomHeader"
          "FailurePolicy": "Redistribute", // 預設，或設定"Return503"
          "Settings" : {
              "CustomHeaderName": "MySessionHeaderName" // 預設為'X-Yarp-Proxy-Affinity`
          }
        },
        "HealthCheck": {
          "Active": { // 進行API呼叫以驗證健康狀態
            "Enabled": "true",
            "Interval": "00:00:10",
            "Timeout": "00:00:10",
            "Policy": "ConsecutiveFailures",
            "Path": "/api/health" // 查詢健康狀態的API端點
          },
          "Passive": { // 根據HTTP回應碼禁用目的地
            "Enabled": true, // 預設false
            "Policy" : "TransportFailureRateHealthPolicy", // 必須
            "ReactivationPeriod" : "00:00:10" // 10s
          }
        },
        "HttpClient" : { // 組態用於連接目的地的HttpClient執行個體
          "SSLProtocols" : "Tls13",
          "DangerousAcceptAnyServerCertificate" : false,
          "MaxConnectionsPerServer" : 1024,
          "EnableMultipleHttp2Connections" : true,
          "RequestHeaderEncoding" : "Latin1" // 如何解譯Header值中非ASCII字元
        },
        "HttpRequest" : { // 向目的地發送請求的選項
          "ActivityTimeout" : "00:02:00",
          "Version" : "2",
          "VersionPolicy" : "RequestVersionOrLower",
          "AllowResponseBuffering" : "false"
        },
        "MetaData" : { // 自定擴充，可以使用key-value清單清單
          "TransportFailureRateHealthPolicy.RateLimit": "0.5", // 使用在Passive健康策略
          "MyKey" : "MyValue"
        }
      }
    }
  }
}
```

補充`LoadBalancingPolicy`：

* `First`：選擇第一個目標，不考慮負載。這對於雙目標故障轉移系統非常有用。
* `Random`：隨機選擇一個目標。
* `PowerOfTwoChoices`(預設)：選擇兩個隨機的目標，然後從中選擇一個更少請求的目標。這避免了LeastRequests的開銷，也避免了Random選擇繁忙目的地的最壞情況。
* `RoundRobin`：通過順序迴圈選擇一個目標。
* `LeastRequests`：所有目標中選擇分配請求最少的目標。這需要檢查所有目標。

##  2. <a name='ConfigurationProviders'></a>擴充：Configuration Providers

前面是以appsettings.json來載入Proxy組態。當然，你也可以從你選擇的來源，以程式控制的方式來載入。目前提供兩組介面：

* `IProxyConfigProvider`
    * 提供Proxy的Routes和Clusters的資料來源。
* `IProxyConfig`
    * 提供Proxy組態資料的快照。

###  2.1. <a name=''></a>結構

`IProxyConfigProvider`提供單一方法`GetConfig()`並且應該傳回`IProxyConfig`執行個體。`IProxyConfig`是當前Routes與Clusters的清單，以及一個`IChangeToken`屬性，在訊息過期並且應該重新載入時通知Proxy，這種導致重新呼叫`GetConfig()`。

* Routes可以透過<a href="https://microsoft.github.io/reverse-proxy/api/Yarp.ReverseProxy.Configuration.RouteConfig.html" target="_blank">`RouteConfig`類別</a>來來進行組態。
* Clusters可以透過<a href="https://microsoft.github.io/reverse-proxy/api/Yarp.ReverseProxy.Configuration.ClusterConfig.html" target="_blank">`ClusterConfig`類別</a>來組態。

###  2.2. <a name='-1'></a>生命週期

`IProxyConfigProvider`應該透過DI容器註冊為Singleton。啟動時，Proxy將解析此執行個體並且呼叫`GetConfig()`。第一次呼叫時，Provider可以選擇：

* 如果Provider不論何種原因，只要是無法產生有效的Proxy組態，那麼就拋出異常。這會阻止應用程式啟動。
* 在載入組態時同步鎖定（ynchronously block），這會阻止應用程式啟動，直到取得有效的Route資料可用。
* 或者，它可以選擇在背景載入組態時傳回一個空的`IProxyConfig`執行個體。當組態準備好的時候，Provider需要觸發`IChangeToken`。

Proxy會驗證給予的組態是否有效，如果無效，那麼就會引發異常以阻止應用程式啟動。Provider可以透過`IConfigValidator`預先驗證Routes與Clusters，並且採取你認為合適的作業流程，例如，排除無效的組態項目。

```csharp
/// <summary>
/// 擴充IReverseProxyBuilder以支援InMemoryConfigProvider
/// </summary>
public static class InMemoryConfigProviderExtensions
{
    public static IReverseProxyBuilder LoadFromMemory(this IReverseProxyBuilder builder, IReadOnlyList<RouteConfig> routes, ReadOnlyList<ClusterConfig> clusters)
    {
        // 必須是Singleton
        builder.Services.AddSingleton<IProxyConfigProvider>(new InMemoryConfigProvider(routes, clusters));
        return builder;
    }
}
```

###  2.3. <a name='-1'></a>原子性

提供給Proxy的組態物件和集合應該是唯讀的，一旦透過`GetConfig()`傳遞給Proxy就不能修改。

```csharp
public InMemoryConfigProvider(IReadOnlyList<RouteConfig> routes, IReadOnlyList<ClusterConfig> clusters)
{
    _config = new InMemoryConfig(routes, clusters);
}
```

###  2.4. <a name='-1'></a>重新載入

如果`IChangeToken`支援`ActiveChangeCallbacks`，那麼Proxy處理完初始組態集之後，它將使用此Token的回呼（Callback）。請八注意。Proxy不支援輪詢（polling）的異動。

當Provider想要為Proxy提供新的組態的時候，它應該：

* 在背景載入組態
    * Routes和Clusters物件是不可變的（immutable），因此為任何新資料建立新執行個體。
    * 可以重新使用未異動的Routes和Clusters物件，或者建立新執行個體－將透過比較它們來檢測異動。
* 可選擇性的使用`IConfigValidator`驗證組態，然後才向來自先前`IProxyConfig`執行個體的`IChangeToken`發出信號，表明有新資料可用。Proxy將再次呼叫`GetConfig()`以接收新資料。

重新載入組態與首次載入組態兩者存在重要差異。

* 新舊組態不同，並且只會更新修改後的Routes或Clusters。更新將自動套用，並且只影響新請求，不會影響當前正在進行的請求。
* 重新載入過程中任何錯誤都將被記錄與抑制。應用程式將會續繼使用最後一組已知良好組態。
* 如果`GetConfig()`拋出例外，Proxy將無法監聽未來的變化，因為`IChangeToken`是一次性的。

一旦驗證並套用了新組態，Proxy將使用新的`IChangeToken`註冊回呼。請注意，如果有多次重新載入的信號，Proxy可能會跳過其中一些並在準備就緒後立即載入下一個可用組態。

###  2.5. <a name='-1'></a>範例

```csharp
namespace Yarp.Sample
{
    /// <summary>
    /// 擴充IReverseProxyBuilder以支援InMemoryConfigProvider
    /// </summary>
    public static class InMemoryConfigProviderExtensions
    {
        public static IReverseProxyBuilder LoadFromMemory(this IReverseProxyBuilder builder, IReadOnlyList<RouteConfig> routes, IReadOnlyList<ClusterConfig> clusters)
        {
            builder.Services.AddSingleton<IProxyConfigProvider>(new InMemoryConfigProvider(routes, clusters));
            return builder;
        }
    }

    /// <summary>
    /// Provides an implementation of IProxyConfigProvider to support config being generated by code. 
    /// 提供IProxyConfigProvider實作，以支援程式碼產生組態
    /// </summary>
    public class InMemoryConfigProvider : IProxyConfigProvider
    {
        // Marked as volatile so that updates are atomic
        // 標記為volatile以取得更新的原子性
        private volatile InMemoryConfig _config;

        public InMemoryConfigProvider(IReadOnlyList<RouteConfig> routes, IReadOnlyList<ClusterConfig> clusters)
        {
            _config = new InMemoryConfig(routes, clusters);
        }

        /// <summary>
        /// Implementation of the IProxyConfigProvider.GetConfig method to supply the current snapshot of configuration
        /// 實作IProxyConfigProvider.GetConfig方法以提供組態當前的快照
        /// </summary>
        /// <returns>An immutable snapshot of the current configuration state
        /// 當前組態狀態的不可變快照
        ///</returns>
        public IProxyConfig GetConfig() => _config;

        /// <summary>
        /// Swaps the config state with a new snapshot of the configuration, then signals the change
        /// 使用組態新快照交換組態狀態，然後發出異動信號
        /// </summary>
        public void Update(IReadOnlyList<RouteConfig> routes, IReadOnlyList<ClusterConfig> clusters)
        {
            var oldConfig = _config;
            _config = new InMemoryConfig(routes, clusters);
            oldConfig.SignalChange();
        }

        /// <summary>
        /// IProxyConfig的實作，它是當前組態狀態的快照。這個類別的資料應該是不可變的。
        /// </summary>
        private class InMemoryConfig : IProxyConfig
        {
            // 用於實作狀態的異動的Token
            private readonly CancellationTokenSource _cts = new CancellationTokenSource();

            public InMemoryConfig(IReadOnlyList<RouteConfig> routes, IReadOnlyList<ClusterConfig> clusters)
            {
                Routes = routes;
                Clusters = clusters;
                ChangeToken = new CancellationChangeToken(_cts.Token);
            }

            /// <summary>
            // Routes清單的快照
            /// </summary>
            public IReadOnlyList<RouteConfig> Routes { get; }

            /// <summary>
            /// Clusters清單的快照，這些cluster是可互換目標端點的集合
            /// </summary>
            public IReadOnlyList<ClusterConfig> Clusters { get; }

            /// <summary>
            /// 觸發以指出Proxy狀態已異動，並且此快照已過時
            /// </summary>
            public IChangeToken ChangeToken { get; }

            internal void SignalChange()
            {
                _cts.Cancel();
            }
        }
    }
}
```

##  3. <a name='-1'></a>直接轉送

某些應用程式僅能夠接受特定請求並且將它轉送到特定目的地。這些應用程式不需要或其他方式解決Proxy的其他功能，例如，組態探索、Route、負載平衡等。

###  3.1. <a name='IHttpForwarder'></a>IHttpForwarder

`IHttpForwarder`作為傳入ASP.NET Core和傳出System.Net.Http請求之間的核心Proxy Adapter。它處理從`HttpContext`建立`HttpRequestMessage`的機制，發送它，並且中斷回應。

`IHttpForwarder`支援：

* 動態目的地的選擇，你為每個請求指定目的地。
* 提供你的`HttpMessageInvoker`，進行Http Client客制化
* 請求與回應的客制化（除了Body之外）
* Stream協定，例如，gRPC、WebSocket
* 錯誤處理

它不包含：

* Routing
* Load balancing
* Affinity
* Retries

###  3.2. <a name='-1'></a>範例

```csharp
using System.Net;
using Yarp.ReverseProxy.Forwarder;
using Yarp.ReverseProxy.Transforms;

var builder = WebApplication.CreateBuilder(args);
// 將HttpForwarder加入Services
builder.Services.AddHttpForwarder();

var app = builder.Build();

// 為Proxy操作的輸出呼叫我們自己HttpMessageInvoker組態
var httpClient = new HttpMessageInvoker(new SocketsHttpHandler()
{
    UseProxy = false,
    AllowAutoRedirect = false,
    AutomaticDecompression = DecompressionMethods.None,
    UseCookies = false
});

var transformer = HttpTransformer.Default; // 換自訂轉換器，例如：new CustomTransformer();
var requestOptions = new ForwarderRequestConfig { ActivityTimeout = TimeSpan.FromSeconds(100) };

app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.Map("/PowerShell/{**catch-all}", async (HttpContext httpContext, IHttpForwarder forwarder) =>
    {
        var error = await forwarder.SendAsync(httpContext, "https://blog.kkbruce.net", httpClient, requestOptions,
            static (context, proxyRequest) =>
            {
                // 自定義Query String
                //var queryContext = new QueryTransformContext(context.Request);
                //queryContext.Collection.Remove("param1");
                //queryContext.Collection["area"] = "xx2";

                // 指派自訂Uri
                // 這裡要小心額外的斜線，RequestUtilities.MakeDestinationAddress是一個安全的預設值
                proxyRequest.RequestUri = RequestUtilities.MakeDestinationAddress("https://blog.kkbruce.net/search/label/", context.Request.Path, context.Request.QueryString);

                // 停用原始請求Header，使用來自目的Uri的Header
                proxyRequest.Headers.Host = null;

                return default;
            });

        // 確認Proxy操作是否成功
        if (error != ForwarderError.None)
        {
            var errorFeature = httpContext.Features.Get<IForwarderErrorFeature>();
            var exception = errorFeature.Exception;
        }
    });

    endpoints.Map("/{**catch-all}", async (HttpContext httpContext, IHttpForwarder forwarder) =>
    {
        var error = await forwarder.SendAsync(httpContext, "https://blog.kkbruce.net", httpClient, requestOptions, transformer);
        // 確認Proxy操作是否成功
        if (error != ForwarderError.None)
        {
            var errorFeature = httpContext.Features.Get<IForwarderErrorFeature>();
            var exception = errorFeature.Exception;
        }
    });
});

app.Run();

class CustomTransformer : HttpTransformer
{
    public override async ValueTask TransformRequestAsync(HttpContext httpContext,
        HttpRequestMessage proxyRequest, string destinationPrefix)
    {
        // 複製所有Header
        await base.TransformRequestAsync(httpContext, proxyRequest, destinationPrefix);
        // 自定義Query String
        var queryContext = new QueryTransformContext(httpContext.Request);
        queryContext.Collection.Remove("param1");
        queryContext.Collection["area"] = "xx2";
        // 指派自訂Uri
        // 這裡要小心額外的斜線，RequestUtilities.MakeDestinationAddress是一個安全的預設值
        proxyRequest.RequestUri = RequestUtilities.MakeDestinationAddress("https://blog.kkbruce.net", httpContext.Request.Path, queryContext.QueryString);

        // 停用原始請求Header，使用來自目的Uri的Header
        proxyRequest.Headers.Host = null;
    }
}
```

讓我們注意一下這裡：

```csharp
// Routes Pattern
"/PowerShell/{**catch-all}"

// 替換Uri
RequestUtilities.MakeDestinationAddress("https://blog.kkbruce.net/search/label/", context.Request.Path, context.Request.QueryString);
```

當我們請求`https://localhost:7158/PowerShell`的時候，由於我們將加入後面`context.Request.Path`與`context.Request.QueryString`的設定，因此，它會將你原始請求的`Path`與`QueryString`附加到指定的位置，因此送出的請求Uri為`https://blog.kkbruce.net/search/label/PowerShell`。假設我們不希望將原始請求的`Path`與`QueryString`附加到指定的位置，而是採用指定Uri的方式，可以傳入對應的`Empty`物件。

```csharp
proxyRequest.RequestUri = RequestUtilities.MakeDestinationAddress("https://blog.kkbruce.net/search/label/PowerShell", PathString.Empty, QueryString.Empty);
```

###  3.3. <a name='HTTPClient'></a>HTTP Client

HTTP Client雖然可以自定義，但對於常見的Proxy場景建議使用上面的範例。始終使用`HttpMessageInvoker`而不是`HttpClient`，因為`HttpClient`預設會快取回應。快取會破壞stream的使用場景並且增加記憶體和延遲。出於效能原因，建議將用戶端重新用於同一目的地的請求，因為它允許你重新使用集區連接。如果組態相同，用戶端也可以重用於不同目的地的請求。

###  3.4. <a name='-1'></a>轉換

可以透過衍生自`HttpTransformer`的轉換（Transforms）類別作為參數提供給`SendAsync`方法來修改請求與回應。（範例還有另一種透過委派直接設定。）

###  3.5. <a name='-1'></a>錯誤處理

`IHttpForwarder`從HTTP用戶端捕獲例外與超時，並且記錄它們，將它們轉換為5xx狀態碼或中止回應。`SendAsync`傳回一個錯誤代碼，錯誤詳細訊息可以從`IForwarderErrorFeature`取得，如範例所示。

##  4. <a name='HTTPClient-1'></a>HTTP Client組態

每個Clusters都有一個專用的`HttpMessageInvoker`執行個體，用於將請求轉送到目的地。組態是按照Cluster定義的。在YARP啟動時，所有Cluster都會獲得新的`HttpMessageInvoker`執行個體，但是如果稍後Cluster組態發生異動，`IForwarderHttpClientFactory`將重新執行並決定它是否應該建立一個新的`HttpMessageInvoker`或繼續使用現有的執行個體。當`HttpClientConfig`發生異動的時候，預設的`IForwarderHttpClientFactory`實作會建立一個新的`HttpMessageInvoker`。

也可以組態指定Cluster傳出請求的屬性。它們由`ForwarderRequestConfig`定義。如果你使用`IConfiguration`模型或程式碼優先模型，則組態的表示方式有所不同。

###  4.1. <a name='IConfiguration'></a>IConfiguration

這些型別專注於定義可序列化組態。下面的“Code Configuration”部分描述了基於程式碼的組態模型。

####  4.1.1. <a name='HttpClient'></a>HttpClient

Http用戶端組態基於`HttpClientConfig`並且由以下組態架構表示。

```json
"HttpClient": {
    "SslProtocols": [ "<protocol-names>" ],
    "MaxConnectionsPerServer": "<int>",
    "DangerousAcceptAnyServerCertificate": "<bool>",
    "RequestHeaderEncoding": "<encoding-name>",
    "EnableMultipleHttp2Connections": "<bool>"
}
```

組態設定：

* `SslProtocols`：啟用HttpClient的SSL協定。協定名稱指定為字串陣列（Array）。預設值為`None`。

```json
"SslProtocols": [
    "Tls11",
    "Tls12"
]
```

* `MaxConnectionsPerServer`：同時連線至同一台伺服器最大HTTP 1.1連接數。預設值為`int32.MaxValue`。

```json
"MaxConnectionsPerServer": "10"
```

* `DangerousAcceptAnyServerCertificate`：表示用戶端是否檢查伺服器的SSL證書有效性。設定為`true`完全取消驗證作業。預設值為`false`。

```json
"DangerousAcceptAnyServerCertificate": "true"
```

* `RequestHeaderEncoding`：為傳出請求Header啟用非ASCII編碼。如果您需要更精細的方法，請使用自定義`IProxyHttpClientFactory`。設定此值後，將利用`SocketsHttpHandler.RequestHeaderEncodingSelector`為所有Header使用指定的編碼。如果你需要更精細的方法，請使用自定義`IProxyHttpClientFactory`。然後透過`Encoding.GetEncoding`解析此值，使用諸如“utf-8”、“iso-8859-1”等。此設定僅在.NET 5.0有效。

```json
"RequestHeaderEncoding": "utf-8"
```

如果你使用的編碼不是ASCII（或Kestrel的UTF-8），您還需要將伺服器設定為接受帶有此類Header的請求。例如，使用 `KestrelServerOptions.RequestHeaderEncodingSelector`設定Kestrel以接受`Latin1 ("iso-8859-1")`Header：

```csharp
private static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>()
                      .ConfigureKestrel(kestrel =>
                      {
                          kestrel.RequestHeaderEncodingSelector = _ => Encoding.Latin1;
                      });
        );
```

* `EnableMultipleHttp2Connections`：當所有現有連接達到最大並行資料流時，啟用與同一伺服器的額外HTTP/2連接。預設值為`true`。此設定僅在.NET 5.0有效，參考<a href="https://docs.microsoft.com/en-us/dotnet/api/system.net.http.socketshttphandler.enablemultiplehttp2connections?view=net-6.0" target="_blank">SocketsHttpHandler.EnableMultipleHttp2Connections</a>說明。

```json
"EnableMultipleHttp2Connections": false
```

對於.NET Core 3.1，`Latin1（“iso-8859-1”）`是唯一可以透過**appsettings.json**接受的非ASCIIHeader編碼：

```json
{
    "Kestrel":
    {
        "Latin1RequestHeaders": true
    }
}
```

連同應用程式範圍的開關：

```csharp
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.AllowLatin1Headers", true);
```

目前，沒有改變Kestrel中回應Header編碼的解決方案（參見 aspnetcore#26334），只接受ASCII。

####  4.1.2. <a name='HttpRequest'></a>HttpRequest

HTTP請求組態基於`ForwarderRequestConfig`，由以下組態模式表示。

```json
"HttpRequest": {
    "ActivityTimeout": "<timespan>",
    "Version": "<string>",
    "VersionPolicy": ["RequestVersionOrLower", "RequestVersionOrHigher", "RequestVersionExact"],
    "AllowResponseBuffering": "<bool>"
}
```

組態設定：

* `ActivityTimeout`：請求在任何操作完成之間允許保持空閒多長時間，之後它將被取消。預設值為`100`秒。當收到回應Header或成功讀取或寫入任何請求、回應或資料流資料（streaming data）（如gRPC或WebSockets）後，超時將重設。TCP keep-alives和 HTTP/2協定`ping`不會重設超時，但WebSocket的`ping`會重設。
* `Version`：傳出請求的版本。目前支援`1.0`、`1.1`和`2`。預設值為`2`。
* `VersionPolicy`：定義如何為傳出請求選擇最終版本。此設定僅在.NET 5.0有效，參考<a href="https://docs.microsoft.com/zh-tw/dotnet/api/system.net.http.httprequestmessage.versionpolicy" target="_blank">HttpRequestMessage.VersionPolicy</a>說明。預設值為`RequestVersionOrLower`。
* `AllowResponseBuffering`：如果託管YARP（例如IIS）的伺服器支援，那麼允許在將回應發送回用戶端時使用寫入快取。注意：啟用它可能會破壞SSE（Server Side Event）場景。

###  4.2. <a name='-1'></a>組態範例

以下展示了`cluster1`和`cluster2`的HTTPClient和請求組態的2個範例。

```json
{
    "Clusters": {
        "cluster1": {
            "LoadBalancingPolicy": "Random",
            "HttpClient": {
                "SslProtocols": [
                    "Tls11",
                    "Tls12"
                ],
                "MaxConnectionsPerServer": "10",
                "DangerousAcceptAnyServerCertificate": "true"
            },
            "HttpRequest": {
                "ActivityTimeout": "00:00:30"
            },
            "Destinations": {
                "cluster1/destination1": {
                    "Address": "https://localhost:10000/"
                },
                "cluster1/destination2": {
                    "Address": "http://localhost:10010/"
                }
            }
        },
        "cluster2": {
            "HttpClient": {
                "SslProtocols": [
                    "Tls12"
                ]
            },
            "HttpRequest": {
                "Version": "1.1",
                "VersionPolicy": "RequestVersionExact"
            },
            "Destinations": {
                "cluster2/destination1": {
                    "Address": "https://localhost:10001/"
                }
            }
        }
    }
}
```

###  4.3. <a name='-1'></a>程式碼組態

HTTP 用戶端組態使用`HttpClientConfig`型別。

以下是使用基於程式碼組態的`HttpClientConfig`範例。在將Cluster陣列傳遞給`LoadFromMemory`方法之前，將`HttpClientConfig`的一個執行個體分配給`ClusterConfig.HttpClient`屬性。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    var routes = new[]
    {
        new RouteConfig()
        {
            RouteId = "route1",
            ClusterId = "cluster1",
            Match =
            {
                Path = "{**catch-all}"
            }
        }
    };
    var clusters = new[]
    {
        new ClusterConfig()
        {
            ClusterId = "cluster1",
            Destinations =
            {
                { "destination1", new DestinationConfig() { Address = "https://localhost:10000" } }
            },
            HttpClient = new HttpClientConfig { MaxConnectionsPerServer = 10, SslProtocols = SslProtocols.Tls11 | SslProtocols.Tls12 }
        }
    };

    services.AddReverseProxy()
        .LoadFromMemory(routes, clusters)
        .AddProxyConfigFilter<CustomConfigFilter>();
}
```

###  4.4. <a name='HttpClient-1'></a>組態HttpClient

`ConfigureHttpClient` 提供了一個回呼（callback）來自定義用於Proxy請求的`SocketsHttpHandler`設定。每次加入或異動Cluster時都會呼叫此方法。Cluster設定在回呼之前套用於處理程序。自定義資料可以在Cluster metaData中提供。以下範例顯示加入目標伺服器驗證Proxy的用戶端憑證。

```csharp
var clientCert = new X509Certificate2("path");
services.AddReverseProxy()
        .ConfigureHttpClient((context, handler) =>
        {
            handler.SslOptions.ClientCertificates.Add(clientCert);
        })
```

###  4.5. <a name='IForwarderHttpClientFactory'></a>自定IForwarderHttpClientFactory

如果需要直接控制HTTP用戶端構造，可以將預設的`IForwarderHttpClientFactory`替換為自定義的。對於某些自定義，您可以從預設的`ForwarderHttpClientFactory`衍生並覆寫組態用戶端的方法。

建議任何自定義工廠將以下`SocketsHttpHandler`屬性設定為與預設工廠相同的值，以保留正確的反向代理行為並避免不必要的成本。

```csharp
new SocketsHttpHandler
{
    UseProxy = false,
    AllowAutoRedirect = false,
    AutomaticDecompression = DecompressionMethods.None,
    UseCookies = false
};
```

始終傳回`HttpMessageInvoker`執行個體而不是衍生自`HttpMessageInvoker`的`HttpClient`執行個體。預設情況下，`HttpClient`會快取回應，這會中斷資料流（streaming）傳輸場景並增加記憶體使用量和延遲。

自定義資料可以在Cluster metadata中提供。

下面是自定義`IForwarderHttpClientFactory`實作的範例。

```csharp
public class CustomForwarderHttpClientFactory : IForwarderHttpClientFactory
{
    public HttpMessageInvoker CreateClient(ForwarderHttpClientContext context)
    {
        var handler = new SocketsHttpHandler
        {
            UseProxy = false,
            AllowAutoRedirect = false,
            AutomaticDecompression = DecompressionMethods.None,
            UseCookies = false
        };

        return new HttpMessageInvoker(handler, disposeHandler: true);
    }
}
```

##  5. <a name='HTTPSTLS'></a>HTTPS & TLS

HTTPS（基於TLS加密連接的HTTP）是出於安全、完整性和隱私原因在Internet上發出HTTP請求的標準方式。在使用像YARP這樣的反向代理時，有幾個HTTPS/TLS考慮因素需要考慮。

###  5.1. <a name='TLSTermination'></a>TLS Termination

YARP是OSI模型中第7層的HTTP Proxy，這意味著傳入的HTTPS/TLS連接由Proxy完全解密，因此它可以處理和轉發HTTP請求。這通常稱為TLS Termination。到目的地的傳出連接可能加密也可能不加密，具體取決於提供的組態。

###  5.2. <a name='TLStunnelingCONNECT'></a>TLS tunneling (CONNECT)

使用`CONNECT`方法的TLS tunneling是一種用於Proxy請求而不解密它們的功能。YARP不支持此功能，也沒有計劃加入它。

###  5.3. <a name='-1'></a>組態傳入連接

YARP可以在所有ASP.NET Core伺服器之上運行，並且為傳入連接組態HTTPS/TLS是特定於伺服器的。組態細節請參考文件：

* <a href="https://docs.microsoft.com/zh-tw/aspnet/core/fundamentals/servers/kestrel/endpoints" target="_blank">Kestrel</a>
* <a href="https://docs.microsoft.com/zh-tw/iis/manage/configuring-security/how-to-set-up-ssl-on-iis" target="_blank">IIS</a>
* <a href="https://docs.microsoft.com/zh-tw/aspnet/core/fundamentals/servers/httpsys" target="_blank">HTTP.sys</a>

###  5.4. <a name='KestrelTLS'></a>使用Kestrel的進階TLS過濾器

Kestrel支持在TLS握手（TLS handshake）之前攔截傳入的連接。YARP包含一個`TlsFrameHelper` API，它可以解析原始TLS 握手，並使您能夠收集自定義遙測或急切拒絕連接。這些API無法修改TLS握手或解密資料流。可參考<a href="https://github.com/microsoft/reverse-proxy/blob/v1.0.0-rc.1/testassets/ReverseProxy.Direct/TlsFilter.cs" target="_blank">TlsFilter</a>線上範例。

###  5.5. <a name='-1'></a>組態傳出連接

要在與目的地通訊時啟用TLS加密，請將目的地地址指定為`https`，如`“https://destinationHost”`。

預設情況下，目標地址中指定的主機名將用於TLS握手，包括SNI和伺服器憑證驗證。如果啟用了Proxy<a href="https://microsoft.github.io/reverse-proxy/articles/transforms.html#requestheaderoriginalhost" target="_blank">原始主機Header</a>，則此值將用於TLS 握手。如果需要使用自定義主機值，那麼使用`RequestHeader`轉換來設定主機Header。

到目的地的傳出連接由`HttpClient`/`SocketsHttpHandler`處理。可以為每個Cluster組態不同的執行個體和設定。某些設定在組態模型中可用，而其他設定只能在程式碼中進行組態。

目標伺服器憑證需要被Proxy信任，或者需要透過`HttpClient`組態套用自定義驗證。

##  6. <a name='HTTPHeader'></a>HTTP Header指南

Header是處理HTTP請求一個非常重要的部分，每個Header都有自己的語義和注意事項。預設情況下，大多數Header都是Proxy的，儘管一些用於控制請求如何傳遞的Header會被Proxy自動調整或刪除。用戶端和Proxy之間的連接以及Proxy和目的地之間的連接是獨立的，因此需要過濾影響連接和傳輸的Header。許多Header包含域名、路徑或其他詳細資訊等訊息，當應用程式架構中包含反向代理時，這些資訊可能會受到影響。以下是有關特定Header如何受到影響以及如何處理這些Header的指南集合。

###  6.1. <a name='YARPHeader'></a>YARP Header過濾

YARP會自動刪除可能影響其正確轉發請求能力或可能被惡意使用以繞過Proxy功能的請求和回應Header。可以在<a href="https://github.com/microsoft/reverse-proxy/blob/v1.0.0-rc.1/src/ReverseProxy/Forwarder/RequestUtilities.cs#L62-L94" target="_blank">原始碼</a>找到完整清單清單，其中一些說明如下所述。

####  6.1.1. <a name='ConnectionKeepAliveClose'></a>Connection, KeepAlive, Close

這些Header控制TCP連接的管理和刪除方式，以避免影響Proxy另一端的連接。

####  6.1.2. <a name='Transfer-Encoding'></a>Transfer-Encoding

此Header描述了網際網路上的請求或回應主體（Body）的格式，例如'chunked'，並且它會被刪除，因為內部和外部連接的格式可能不同。傳入和傳出HTTP Stack將根據需要加入傳輸Header。

####  6.1.3. <a name='TE'></a>TE

由於某些gRPC實作需要它，因此僅允許`TE: trails`Header值通過Proxy。

####  6.1.4. <a name='Upgrade'></a>Upgrade

這用於像WebSockets這樣的協定。預設情況下，它會被刪除，並且僅針對特定支持的協議（WebSockets、SPDY）重新加入。

####  6.1.5. <a name='Proxy-'></a>Proxy-*

這些是與Proxy一起使用的Header，不適合轉發。

####  6.1.6. <a name='Alt-Svc'></a>Alt-Svc

此回應Header與HTTP/3 upgrade一起使用，僅適用於立即連接。

####  6.1.7. <a name='TraceParentRequest-IdTraceStateBaggageCorrelation-Context'></a>TraceParent, Request-Id, TraceState, Baggage, Correlation-Context

這些Header與分散式追蹤有關。它們會在.NET 6或更高版本上自動刪除，以便轉發`HttpClient`可以用更新的值替換它們。

###  6.2. <a name='Header'></a>其他Header指南

####  6.2.1. <a name='Host'></a>Host

HostHeader指出請求針對伺服器上的哪個站點。預設情況下會刪除此Header，因為Proxy公開使用的主機名稱可能與Proxy背後的服務使用的主機名稱不同。這可以使用`RequestHeaderOriginalHost`轉換進行組態。

####  6.2.2. <a name='X-Forwarded-Forwarded'></a>X-Forwarded-*, Forwarded

因為使用單獨的連接與目標進行通訊，所以這些請求Header可用於轉發有關原始連接的資訊，例如 IP、schema、port、用戶端證書等。X-Forwarded-For、X-Forwarded-Proto 、X-Forwarded-Host和 X-Forwarded-Prefix預設都是啟用。這此資訊會受到欺騙攻擊（spoofing attacks），因此預設情況下會刪除並替換請求中的任何現有Header。目標應用程式應該注意本身對這些值的信任程度。請參閱轉換（transforms）小節在Proxy中這些組態。有關組態應用程式以讀取這此Header，請參考<a href="https://docs.microsoft.com/zh-tw/aspnet/core/host-and-deploy/proxy-load-balancer" target="_blank">ASP.NET Core與Proxy</a>文件。

> URL的結構：scheme://host:port/path?query#hash<br>
> schema指協定，例如http、https、ftp、mailto等。

####  6.2.3. <a name='X-http-mehtod-overridex-http-methodx-method-override'></a>X-http-mehtod-override, x-http-method, x-method-override

一些用戶端和伺服器限制了它們允許的HTTP動詞（GET、POST等）。這些請求Header有時用於解決這些限制。預設情況下，這些Header是Proxy的。如果您想在Proxy中阻止這些繞過限制，請使用 `RequestHeaderRemove`轉換。

####  6.2.4. <a name='Set-Cookie'></a>Set-Cookie

此回應Header可能包含限制應該使用cookie的path、domain、schema的欄位。使用反向Proxy可能會從公眾的角度更改站點的有效domain、path或schema。雖然可以使用自定義轉換重寫（rewrite）回應 cookie，但建議使用上述ForwardedHeader將正確的值流向目標應用程式，以便它可以產生正確的 set-cookieHeader。

####  6.2.5. <a name='Location'></a>Location

此回應Header與重導向（redirect）一起使用，並且可能包含由於使用Proxy而與公開值不同的schema、domain和path。

####  6.2.6. <a name='Server'></a>Server

此回應Header指示用於產生回應的伺服器技術（IIS、Kestrel等）。預設情況下，此Header從目標進行Proxy。想要刪除它的應用程式可以使用`ResponseHeaderRemove`轉換，在這種情況下將使用Proxy的預設伺服器Header。禁止顯示Proxy預設伺服器Header是特定於伺服器的，例如對於Kestrel的`AddServerHeader`屬性。

####  6.2.7. <a name='X-Powered-By'></a>X-Powered-By

此響應Header指示用於生成響應的 Web 框架（ASP.NET 等）。此回應Header指示用於產生回應的Web Framework（ASP.NET等）。ASP.NET Core不會產生此Header，但IIS可以。預設情況下，此Header從目標進行Proxy。想要刪除它的應用程式可以使用`ResponseHeaderRemove`轉換。

##  7. <a name='HeaderRoute'></a>基於Header的Route

在組態中或通過程式碼指定的ProxyRoute必須至少包含要符合的path或host。除了這些之外，Route還可以指定一個或多個必須出現在請求中的Header。

###  7.1. <a name='-1'></a>優先順序

預需Route比對優先順序為

1. path
2. method
3. host
4. headers 
5. query parameters

這意味著指定method但沒有header的Route將在指定header但沒有method的Route之前相符。這可以透過在Route上設定`Order`屬性來覆寫。
 
###  7.2. <a name='-1'></a>組態

Header在ProxyRoute的`Match`部分中指定。

如果在Route上指定了多個Header規則，那麼所有Header規則都必須相符才能採用Route。或者邏輯必須在Header規則內或作為單獨的路由實作。

組態範例

```json
"Routes": {
  "route1" : {
    "ClusterId": "cluster1",
    "Match": {
      "Path": "{**catch-all}",
      "Headers": [
        {
          "Name": "header1",
          "Values": [ "value1" ],
          "Mode": "ExactHeader"
        }
      ]
    }
  },
  "route2" : {
    "ClusterId": "cluster1",
    "Match": {
      "Path": "{**catch-all}",
      "Headers": [
        {
          "Name": "header2",
          "Values": [ "1prefix", "2prefix" ],
          "Mode": "HeaderPrefix"
        }
      ]
    }
  },
  "route3" : {
    "ClusterId": "cluster1",
    "Match": {
      "Path": "{**catch-all}",
      "Headers": [
        {
          "Name": "header3",
          "Mode": "Exists"
        }
      ]
    }
  },
  "route4" : {
    "ClusterId": "cluster1",
    "Match": {
      "Path": "{**catch-all}",
      "Headers": [
        {
          "Name": "header4",
          "Values": [ "value1", "value2" ],
          "Mode": "ExactHeader"
        },
        {
          "Name": "header5",
          "Mode": "Exists"
        }
      ]
    }
  },
  "route5" : {
    "ClusterId": "cluster1",
    "Match": {
      "Path": "{**catch-all}",
      "Headers": [
        {
          "Name": "header5",
          "Values": [ "value1", "value2" ],
          "Mode": "Contains"
        },
        {
          "Name": "header6",
          "Mode": "Exists"
        }
      ]
    }
  },
   "route6" : {
    "ClusterId": "cluster1",
    "Match": {
      "Path": "{**catch-all}",
      "Headers": [
        {
          "Name": "header6",
          "Values": [ "value1", "value2" ],
          "Mode": "NotContains"
        },
        {
          "Name": "header7",
          "Mode": "Exists"
        }
      ]
    }
  }
}
```

程式碼範例

```csharp
var routes = new[]
{
    new RouteConfig()
    {
        RouteId = "route1",
        ClusterId = "cluster1",
        Match = new RouteMatch
        {
            Path = "{**catch-all}",
            Headers = new[]
            {
                new RouteHeader()
                {
                    Name = "Header1",
                    Values = new[] { "value1" },
                    Mode = HeaderMatchMode.ExactHeader
                }
            }
        }
    },
    new RouteConfig()
    {
        RouteId = "route2",
        ClusterId = "cluster1",
        Match = new RouteMatch
        {
            Path = "{**catch-all}",
            Headers = new[]
            {
                new RouteHeader()
                {
                    Name = "Header2",
                    Values = new[] { "1prefix", "2prefix" },
                    Mode = HeaderMatchMode.HeaderPrefix
                }
            }
        }
    },
    new RouteConfig()
    {
        RouteId = "route3",
        ClusterId = "cluster1",
        Match = new RouteMatch
        {
            Path = "{**catch-all}",
            Headers = new[]
            {
                new RouteHeader()
                {
                    Name = "Header3",
                    Mode = HeaderMatchMode.Exists
                }
            }
        }
    },
    new RouteConfig()
    {
        RouteId = "route4",
        ClusterId = "cluster1",
        Match = new RouteMatch
        {
            Path = "{**catch-all}",
            Headers = new[]
            {
            new RouteHeader()
                {
                    Name = "Header4",
                    Values = new[] { "value1", "value2" },
                    Mode = HeaderMatchMode.ExactHeader
                },
                new RouteHeader()
                {
                    Name = "Header5",
                    Mode = HeaderMatchMode.Exists
                }
            }
        }
    },
    new RouteConfig()
    {
        RouteId = "route5",
        ClusterId = "cluster1",
        Match = new RouteMatch
        {
            Path = "{**catch-all}",
            Headers = new[]
            {
                new RouteHeader()
                {
                    Name = "Header5",
                    Values = new[] { "value1", "value2" },
                    Mode = HeaderMatchMode.Contains
                }
            }
        }
    },
    new RouteConfig()
    {
        RouteId = "route6",
        ClusterId = "cluster1",
        Match = new RouteMatch
        {
            Path = "{**catch-all}",
            Headers = new[]
            {
                new RouteHeader()
                {
                     Name = "Header6",
                    Values = new[] { "value1", "value2" },
                    Mode = HeaderMatchMode.NotContains
                }
            }
        }
    }
};
```

###  7.3. <a name='Contract'></a>約束（Contract）

`RouteHeader`定義了程式碼約束，並從組態檔進行對應。

####  7.3.1. <a name='Name'></a>Name

在請求中檢查的Header名稱。需要一個非empty的值。根據HTTP RFC，此欄位不區分大小寫。

####  7.3.2. <a name='Values'></a>Values

要搜尋的可能值清單。根據指定的`Mode`，Header必須與這些值中的至少一個相符，但"NotContains"除外。除非`Mode`設定為`Exists`，否則至少需要一個值。

####  7.3.3. <a name='Mode'></a>Mode

`HeaderMatchMode`指定如何將值與請求Header比對。預設值為`ExactHeader`。

* ExactHeader：Header必須完全相符，大小寫取決於`IsCaseSensitive`的值。僅支持單一Header。如果有多個具有相同名稱的Header，則比對失敗。
* HeaderPrefix：Header必須按前綴比對，以`IsCaseSensitive`的值為基準。僅支持單一Header。如果有多個具有相同名稱的Header，則比對失敗。
* Exists：Header必須存在並包含任何非empty的值。
* Contains：Header必須包含比對的值，受`IsCaseSensitive`值的限制。僅支持單一Header。如果有多個具有相同名稱的Header，則比對失敗。
* NotContains：Header不得包含任何比對值，以`IsCaseSensitive`的值為基準。僅支持單一Header。如果有多個具有相同名稱的Header，則比對失敗。

####  7.3.4. <a name='IsCaseSensitive'></a>IsCaseSensitive

指示值比對是否應區分大小寫或不區分大小寫。預設為`false`，即不敏感。

###  7.4. <a name='-1'></a>範例

這些範例使用上面指定的組態。

####  7.4.1. <a name='1-Header'></a>情境1 - 精確的Header比對

具有以下Header的請求將與 route1 相符。

```http
Header1: Value1
```

當前不支持具有多個值的Header，並且不會相符。

```http
Header1: Value1, Value2
```

當前不支持具有相同名稱的多個Header，並且不會相符。

```http
Header1: Value1
Header1: Value2
```

####  7.4.2. <a name='2-'></a>情境2 - 多值

Route2定義了多個要在Header中搜尋的值（`1prefix`、`2prefix`），任何值都可以接受。它還將`Mode`指定為`HeaderPrefix`，因此任何以這些值開頭的Header都是可以接受的。

以下任何Header都將與route2相符。

```http
Header2: 1prefix
```

```http
Header2: 2prefix
```

```http
Header2: 1prefix-extra
```

```http
Header2: 2prefix-extra
```

當前不支持具有多個值的Header，並且不會相符。

```http
Header2: 1prefix, 2prefix
```

當前不支持具有相同名稱的多個Header，並且不會相符。

```http
Header2: 1prefix
Header2: 2prefix
```

####  7.4.3. <a name='3-'></a>情境3 - 存在

Route3只要求Header名稱`Header3`存在任何非Empty的值。

以下是符合route3的範例。

```http
Header3: value
```

空值Header將不相符。

```http
Header3:
```

此模式確實支持具有多個值的Header和具有相同名稱的多個Header，因為它不查看Header內容。以下將相符。

```http
Header3: value1, value2
```

```http
Header3: value1
Header3: value2
```

####  7.4.4. <a name='4-Header'></a>情境4 - 多個Header

Route4需要`header4`和`header5`，每個都根據它們指定的`Mode`進行比對。以下Hader將相符route4：

```http
Header4: value1
Header5: AnyValue
```

```http
Header4: value2
Header5: AnyValue
```

這些將與Route4不相符，因為它們缺少必需的Header之一：

```http
Header4: value2
```

```http
Header5: AnyValue
```

##  8. <a name='-1'></a>驗證和授權

> * 驗證（Authentication）：你是誰。
> * 授權（Authorization）：你能做什麼。

###  8.1. <a name='-1'></a>簡介

反向Proxy可用於在將請求Proxy到目標伺服器之前對其進行身份驗證和授權。這可以減少目標伺服器上的負載，加入一層保護，並確保在您的應用程式中實施一致的策略。

###  8.2. <a name='-1'></a>預設

除非在路由或應用程式組態中啟用，否則不會對請求執行身份驗證或授權。

###  8.3. <a name='-1'></a>組態

可以透過`RouteConfig.AuthorizationPolicy`為每個路由指定授權策略，並且可以從組態檔案的`Routes`部分繫結。與其他路由屬性一樣，可以在不重新啟動Proxy的情況下對其進行修改和重新載入。策略名稱不區分大小寫。

範例：

```json
{
  "ReverseProxy": {
    "Routes": {
      "route1" : {
        "ClusterId": "cluster1",
        "AuthorizationPolicy": "customPolicy",
        "Match": {
          "Hosts": [ "localhost" ]
        }
      }
    },
    "Clusters": {
      "cluster1": {
        "Destinations": {
          "cluster1/destination1": {
            "Address": "https://localhost:10001/"
          }
        }
      }
    }
  }
}
```

Proxy的<a href="https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/policies" target="_blank">授權策略</a>是使用的ASP.NET Core概念。Proxy提供上述組態以指定每個路由的策略，其餘部分由現有的ASP.NET Core身份驗證和授權元件處理。

授權策略可以在`Startup.ConfigureServices`中組態如下：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy("customPolicy", policy =>
            policy.RequireAuthenticatedUser());
    });
}
```

在`Startup.Configure`中加入`Routing`和`Endpoints`之間的授權和身份驗證中介軟體。

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapReverseProxy();
    });
}
```

請參閱<a href="https://docs.microsoft.com/zh-tw/aspnet/core/security/authentication/" target="_blank">身份驗證</a>文件以設定您喜歡的身份驗證類型。

####  8.3.1. <a name='-1'></a>特殊值

除了`customPolicy`名稱之外，還可以在路由的授權參數中指定兩個特殊值：`default`和`anonymous`。ASP.NET Core還具有適用於未指定策略的路由的`FallbackPolicy`設定。

##### DefaultPolicy

在路由的授權參數中指定值`default`意味著路由將使用`AuthorizationOptions.DefaultPolicy`中定義的策略。此策略已預先組態為要求經過身份驗證的使用者。

##### Anonymous

在路由的授權參數中指定值`anonymous`意味著無論應用程式中的任何其他組態（例如FallbackPolicy）如何設定，此路由都不需要授權。

##### FallbackPolicy

`AuthorizationOptions.FallbackPolicy`是將用於未組態策略的任何請求或路由的策略。FallbackPolicy預設沒有值，任何請求都將被允許。

###  8.4. <a name='FlowingCredentials'></a>Flowing Credentials（流動憑證）

即使請求已在Proxy中獲得授權，目標伺服器仍可能需要知道使用者是誰（身份驗證）以及允許他們做什麼（授權）。您如何傳輸這些資訊將取決於所使用的身份驗證類型。

####  8.4.1. <a name='CookiebearerAPIkeys'></a>Cookie, bearer, API keys

這些身份驗證類型已經在請求Header中傳遞了它們的值，預設情況下這些值將流向目標伺服器。此伺服器仍需要驗證和解釋這些值，從而導致一些重複工作。

####  8.4.2. <a name='OAuth2OpenIdConnectWsFederation'></a>OAuth2, OpenIdConnect, WsFederation

這些協定通常與遠端身份提供者一起使用。身份驗證過程可以在Proxy應用程式中進行組態，並將產生身份驗證cookie。此cookie 將作為一般請求Header流向目標伺服器。

####  8.4.3. <a name='WindowsNegotiateNTLMKerbereos'></a>Windows, Negotiate, NTLM, Kerbereos

這些身份驗證類型通常繫結到特定連接。不支援將它們作為在YARP Proxy後面的目標伺服器中對使用者進行身份驗證的方法（見<a href="https://github.com/microsoft/reverse-proxy/issues/166" target="_blank">#166</a>）。它們可用於驗證對Proxy的傳入請求，但此身份資訊必須以另一種形式傳達給目標伺服器。它們也可用於對目標伺服器的Proxy進行身份驗證，但僅作為Proxy自己的使用者，不支持模擬用戶端。

####  8.4.4. <a name='ClientCertificates'></a>Client Certificates

用戶端憑證是TLS功能，並作為連接的一部分進行協商。參考<a href="https://docs.microsoft.com/zh-tw/aspnet/core/security/authentication/certauth" target="_blank">這些</a>文件以瞭解更多資訊。可以使用`ClientCert`（Transforms小節）轉換將憑證作為HTTP Header轉發到目標伺服器。

####  8.4.5. <a name='Swappingauthenticationtypes'></a>Swapping authentication types

無法自然流向目標伺服器的身份驗證類型（例如：Windows）需要在Proxy中轉換為替代形式。例如，可以使用使用者資訊建立JWT bearer token並在Proxy請求上設定。

這些交換可以使用自定義請求轉換（Transforms小節）來執行。如果有足夠的社群使用者感興趣，我們可以針對特定場景開發詳細範例。我們需要更多關於您希望如何轉換和流動身份資訊的社群反饋。

##  9. <a name='CORS'></a>跨來源資源共享 (CORS)

###  9.1. <a name='-1'></a>簡介

反向Proxy可以在將跨網域請求Proxy到目標伺服器之前對其進行處理。這可以減少目標伺服器上的負載並確保在您的應用程式中實施一致的策略。

###  9.2. <a name='-1'></a>預設

除非在路由或應用程式組態中啟用，否則這些請求不會自動比對CORS預請求（CORS Preflight）。

###  9.3. <a name='-1'></a>組態

可以透過`RouteConfig.CorsPolicy`為每個路由指定CORS策略，並且可以從組態檔案的`Routes`部分繫結。與其他路由屬性一樣，可以在不重新啟動Proxy的情況下對它進行修改和重新載入。策略名稱不區分大小寫。

範例：

```json
{
  "ReverseProxy": {
    "Routes": {
      "route1" : {
        "ClusterId": "cluster1",
        "CorsPolicy": "customPolicy",
        "Match": {
          "Hosts": [ "localhost" ]
        }
      }
    },
    "Clusters": {
      "cluster1": {
        "Destinations": {
          "cluster1/destination1": {
            "Address": "https://localhost:10001/"
          }
        }
      }
    }
  }
}
```

<a href="https://docs.microsoft.com/zh-tw/aspnet/core/security/cors?view=aspnetcore-6.0#cors-with-named-policy-and-middleware" target="_blank">CORS策略</a>是Proxy利用的ASP.NET Core概念。Proxy提供上述組態以指定每個路由的策略，其餘部分由現有的 ASP.NET Core的CORS中介軟體處理。

可以在`Startup.ConfigureServices`中組態CORS策略，如下所示：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("customPolicy", builder =>
        {
            builder.AllowAnyOrigin();
        });
    });
}
```

在`Startup.Configure`中加入路由和端點之間的CORS中間層。

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapReverseProxy();
    });
}
```

####  9.3.1. <a name='DefaultPolicy'></a>DefaultPolicy

在路由的`CorsPolicy`參數中指定值`default`意味著路由將使用`CorsOptions.DefaultPolicy`中定義的策略。

####  9.3.2. <a name='CORS-1'></a>取消CORS

在路由的`CorsPolicy`參數中指定值`disable`意味著CORS中間層將拒絕CORS請求。

##  10. <a name='Session'></a>Session關聯性

###  10.1. <a name='-1'></a>概念

Session關聯性（Affinity）是一種將因果相關的請求序列（sequence ）繫結（關聯）到在多個目標之間平衡負載時處理第一個請求的目標的機制。它在序列中的大多數請求使用相同的資料並且處理請求的不同節點（目的地）的資料存取成本不同的情況下很有用。最常見的範例是暫時性快取（例如，記憶體中），其中第一個請求將資料從較慢的持久儲存體中取得到快速的本地快取中，而其他請求僅處理快取的資料，從而提高處理傳輸量（throughput）。

###  10.2. <a name='-1'></a>組態

####  10.2.1. <a name='Services'></a>Services和中介軟體註冊

Session關聯性服務由`AddReverseProxy()`自動註冊到DI容器中。預設情況下，中間層`UseSessionAffinity()`包含在無參數 `MapReverseProxy`方法中。如果您正在自定義Proxy管道，請在加入`UseLoadBalancing()`**之前**放置第一個中間層。

```csharp
endpoints.MapReverseProxy(proxyPipeline =>
{
    proxyPipeline.UseSessionAffinity();
    proxyPipeline.UseLoadBalancing();
});
```

####  10.2.2. <a name='Cluster'></a>Cluster組態

根據以下組態方案為每個Cluster組態Session關聯性。

```json
"ReverseProxy": {
    "Clusters": {
        "<cluster-name>": {
            "SessionAffinity": {
                "Enabled": "(true|false)", // 預設為'false'
                "Policy": "(Cookie|CustomHeader)", // 預設為'Cookie'
                "FailurePolicy": "(Redistribute|Return503)", // 預設為'Redistribute'
                "AffinityKeyName": "Key1",
                "Cookie": {
                    "Domain": "localhost",
                    "Expiration": "03:00:00",
                    "HttpOnly": true,
                    "IsEssential": true,
                    "MaxAge": "1.00:00:00",
                    "Path": "mypath",
                    "SameSite": "Strict",
                    "SecurePolicy": "Always"
            }
        }
    }
}
```

####  10.2.3. <a name='Policy'></a>特定Policy組態

當前實作了一個特定於策略的強型別組態部分。

* `SessionAffinityCookieConfig` 公開設定以自定義`Cookie`屬性，`CookieSessionAffinityPolicy`將使用這些屬性來建立新的關聯`cookie`。屬性可以是如上所示的JSON組態，也可以是如下所示的程式碼：

```csharp
new ClusterConfig
{
    ClusterId = "cluster1",
    SessionAffinity = new SessionAffinityConfig
    {
        Enabled = true,
        FailurePolicy = "Return503Error",
        Policy = "Cookie",
        AffinityKeyName = "Key1",
        Cookie = new SessionAffinityCookieConfig
        {
            Domain = "mydomain",
            Expiration = TimeSpan.FromHours(3),
            HttpOnly = true,
            IsEssential = true,
            MaxAge = TimeSpan.FromDays(1),
            Path = "mypath",
            SameSite = Microsoft.AspNetCore.Http.SameSiteMode.Strict,
            SecurePolicy = Microsoft.AspNetCore.Http.CookieSecurePolicy.SameAsRequest
        }
    }
}
```

###  10.3. <a name='-1'></a>關聯鍵值

對目標關聯的請求是通過標識目標目的地的關聯鍵值（Affinity Key）建立的。根據指定的Session關聯性實作，該鍵值可以儲存在不同的請求部分，但每個請求不能有多個這樣的鍵值。確切的鍵值語義（key semantics）取決於實作，例如內建的`CookieSessionAffinityPolicy`和`CustomHeaderAffinityPolicy`目前都使用`DestinationId`作為關聯鍵值。

當前設計不需要唯一標識單個關聯目的地的鍵值。允許建立與目標群組的關聯。在這種情況下，處理指定請求的確切目的地將由負載平衡器確定。

###  10.4. <a name='-1'></a>建立新的關聯性或現有的解決方案

一旦請求到達並被路由到啟用了Session關聯性的Cluster，Proxy會根據請求上關聯鍵值的存在和有效性自動決定是應該建立新的關聯性還是需要解析現有的關聯性，如下所示：

* **請求不包含鍵值。**
    * 跳過解析，並將與負載平衡器選擇的目標建立新的關聯
* **關聯鍵值在請求中找到並且有效。**
    * 關聯機制嘗試找到與該鍵值相符的所有健康目的地，如果找到一些，則將請求傳遞到管道中。如果找到多個相符的目的地，則會呼叫負載平衡器來選擇單一個目標目的地。如果僅找到一個相符的目標，則負載平衡器不會介入操作。
* **關聯鍵值無效或未找到健康的關聯目標。**
    * 它會被視為失敗，無法通過下面解釋的失敗策略處理。

如果為請求建立了新的關聯，則關聯鍵值附加到回應之中，其中確切的鍵值展示和位置取決於實作。目前，有兩個內建策略將鍵值儲存在cookie或自定義Header中。一旦回應被傳遞給用戶端，用戶端就有責任將鍵值加到同一Session中的所有後續請求。此外，當攜帶鍵值的下一個請求到達Proxy時，它會解析現有的關聯性，但關聯性鍵值不會再次附加到回應中。因此，只有第一個回應帶有關聯鍵值。

有兩個內建的關聯性策略，不同之處僅在於關聯性鍵值在請求中的儲存方式。預設策略是`Cookie`。

* `Cookie`
    * 將鍵值存儲為cookie。它期望請求的鍵值作為具有組態名稱的cookie傳遞，並在關聯序列中的第一個回應上設定具有 `Set-Cookie` Header的相同cookie。這是由`CookieSessionAffinityPolicy`實作的。`Cookie`名稱必須通過`CookieSessionAffinityPolicy`用於此目的的`SessionAffinityConfig.AffinityKeyName`顯式設定。其他cookie的屬性可以通過`SessionAffinityCookieConfig`進行組態。
* `CustomHeader`
    * 將鍵值存儲在Header中。它期望關聯鍵值在具有組態名稱的自定義Header中傳遞，並在關聯序列中的第一個回應上設定相同的Header。這是由`CustomHeaderSessionAffinityPolicy`實作的。Header名稱必須通過`CustomHeaderSessionAffinityPolicy`用於此目的的`SessionAffinityConfig.AffinityKeyName`設定。
    
> **重要提示**：`AffinityKeyName`在啟用了Session關聯性的所有Cluster中必須是唯一的，以避免衝突。

###  10.5. <a name='-1'></a>關聯失敗策略

如果無法解碼關聯鍵值或未找到健康的目的地，則將其視為失敗，並呼叫關聯失敗策略來處理它。該策略具有對`HttpContext`的完全存取權限，並且可以自行向用戶端發送回應。它傳回一個布林值，指示請求處理是否可以沿管道繼續進行或必須終止。

有兩個內建的失敗策略。默認值為`Redistribute`。

1. `Redistribute`
    * 嘗試通過跳過關聯性查找步驟並將所有健康的目的地傳遞給負載平衡器來建立與可用健康目的地之一的新關聯性，就像對沒有任何關聯性的請求所做的那樣。請求持續處理，這是由`RedistributeAffinityFailurePolicy`實作的。
2. `Return503Error`
    * 向用戶端發送 503 回應並終止請求處理。這是由`Return503ErrorAffinityFailurePolicy`實作的。

###  10.6. <a name='-1'></a>請求管道

Session關聯性機制由服務（如上所述）和以下兩個中介軟體實作：

1. `SessionAffinityMiddleware`
    * 協調請求關聯性解析過程。首先，它在`ClusterConfig.SessionAffinity.Policy`屬性上呼叫為給Cluster指定的策略。然後，它會檢查策略傳回的關聯解析狀態，並在發生故障時呼叫`ClusterConfig.SessionAffinity.FailurePolicy`上設定的故障處理策略。它必須在負載平衡器之前加入到管道中。
2. `AffinitizeTransform` 
    * 如果為請求建立了新的關聯，則在回應上設定鍵值。否則，如果請求遵循現有的關聯，它什麼也不做。這會自動加入為回應轉換（Response Transforms）。

##  11. <a name='-1'></a>負載平衡

###  11.1. <a name='-1'></a>概念

每當有多個健康的目的地可用時，YARP必須決定將哪一個用於給予請求。YARP帶有內建的負載平衡演算法，但也為任何自定義負載平衡方法提供了可擴展性。

###  11.2. <a name='-1'></a>組態

####  11.2.1. <a name='Services-1'></a>Services和中介軟體註冊

負載平衡策略通過`AddLoadBalancingPolicies()`方法在DI容器中註冊，該方法由`AddReverseProxy()`自動呼叫。中介軟體使用`UseLoadBalancing()`加入，預設情況下包含在無參數`MapReverseProxy`方法中。

####  11.2.2. <a name='Cluster-1'></a>Cluster組態

可以通過設定`LoadBalancingPolicy`來組態用於確定目標的演算法。

如果未指定策略，將使用`PowerOfTwoChoices`。

**檔案範例**

```json
"ReverseProxy": {
    "Clusters": {
        "cluster1": {
            "LoadBalancingPolicy": "RoundRobin",
            "Destinations": {
                "cluster1/destination1": {
                    "Address": "https://localhost:10000/"
                },
                "cluster1/destination2": {
                    "Address": "https://localhost:10010/"
                }
            }
        }
    }
}
```

**程式碼範例**

```csharp
var clusters = new[]
{
    new ClusterConfig()
    {
        ClusterId = "cluster1",
        LoadBalancingPolicy = LoadBalancingPolicies.RoundRobin,
        Destinations = new Dictionary<string, Destination>(StringComparer.OrdinalIgnoreCase)
        {
            { "destination1", new DestinationConfig() { Address = "https://localhost:10000" } },
            { "destination2", new DestinationConfig() { Address = "https://localhost:10010" } }
        }
    }
};
```

###  11.3. <a name='-1'></a>內建策略

YARP附帶以下內建策略：

* `FirstAlphabetical`
    * 在不考慮負載的情況下選擇按字母順序排列的第一個可用目的地。這對於雙目的地故障轉移系統很有用。
* `Random`
    * 隨機選擇一個目的地。
* `PowerOfTwoChoices`（預設）
    * 選擇兩個隨機目標，然後選擇分配請求最少的目標。這避免了`LeastRequests`的成本以及`Random` 選擇到繁忙目的地的最壞情況。
* `RoundRobin`
    * 通過按順序循環選擇目的地。
* `LeastRequests`
    * 選擇分配請求最少的目標。這需要檢查所有目的地。

###  11.4. <a name='-1'></a>擴展性

`ILoadBalancingPolicy`負責從可用的健康目的地清單中選擇一個目的地。

可以在DI中提供自定義實作。

```csharp
// 實作ILoadBalancingPolicy
public sealed class LastLoadBalancingPolicy : ILoadBalancingPolicy
{
    public string Name => "Last";

    public DestinationState? PickDestination(HttpContext context, ClusterState cluster, IReadOnlyList<DestinationState> availableDestinations)
    {
        return availableDestinations[^1];
    }
}

// 註冊到DI
services.AddSingleton<ILoadBalancingPolicy, LastLoadBalancingPolicy>();

// 設定Cluster的LoadBalancingPolicy
cluster.LoadBalancingPolicy = "Last";
```

##  12. <a name='-1'></a>中介軟體

###  12.1. <a name='-1'></a>概念

ASP.NET Core使用中介軟體管道將請求處理切分為一步步的步驟。應用程式開發者可以根據需要加入和排序中介軟體。ASP.NET Core的中介軟體也用於實作和自定義反向代理功能。

###  12.2. <a name='-1'></a>預設

在一開始的入門範例顯示了以下組態方法。這設定了一個帶有開發工具、路由和Proxy組態端點（`MapReverseProxy`）的中介軟體管道。

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    
    // 啟用reverse proxy所需要的endpoint路由
    app.UseRouting();
    // 註冊reverse proxy路由
    app.UseEndpoints(endpoints => 
    {
        endpoints.MapReverseProxy(); 
    }); 
}
```

`ReverseProxyIEndpointRouteBuilderExtensions`多載中的無參數`MapReverseProxy()` 包括用於Session關聯性、負載平衡、被動健康檢查和請求的最終Proxy的所有標準Proxy 中介軟體。它們之中的每一個都會檢查符合的路由、Cluster和目的地的組態，並相對應地執行它們的任務。

###  12.3. <a name='-1'></a>加入中介軟體

加入到應用程式管道的中介軟體將根據加入中介軟體的位置查看處於不同處理狀態的請求。在`UseRouting`之前加入的中介軟體將看到所有請求，並且可以在任何路由發生之前對它進行操作。在`UseRouting`和`UseEndpoints`之間加入的中介軟體可以呼叫 `HttpContext.GetEndpoint()`來檢查哪個端點路由與請求相符（如果有的話），並使用與該端點關聯的任何中繼資料（metadata）。這就是身份驗證、授權和CORS的處理方式。

`ReverseProxyIEndpointRouteBuilderExtensions`提供了`MapReverseProxy`的多載，允許您構建一個中介軟體管道，此管道僅針對與Proxy組態的路由相符的請求執行。

```csharp
endpoints.MapReverseProxy(proxyPipeline =>
{
    proxyPipeline.Use((context, next) =>
    {
        // 自定義內聯的中介軟體
        return next();
    });
    proxyPipeline.UseSessionAffinity();
    proxyPipeline.UseLoadBalancing();
    proxyPipeline.UsePassiveHealthChecks();
});
```

預設情況下，這種`MapReverseProxy`多載僅包括其管道開始和結束處的最小設定和Proxy邏輯。預設情況下不包括用於Session關聯性、負載平衡、被動健康檢查的中介軟體，因此您可以使用任何其他中介軟體排除、替換或控制它們的順序。

###  12.4. <a name='Proxy'></a>自定義Proxy中介軟體

`MapReverseProxy`管道內的中介軟體可以通過`IReverseProxyFeature`存取與請求（路由、Cluster、目的地等）相關的所有Proxy資料和狀態。這可從`HttpContext.Features`或擴充方法`HttpContext.GetReverseProxyFeature()`取得。

`IReverseProxyFeature`中的資料是在Proxy管道開始時從Proxy組態中截取的，不會受到處理請求時發生的Proxy組態異動的影響。

```csharp
proxyPipeline.Use((context, next) =>
{
    var proxyFeature = context.GetReverseProxyFeature();
    var cluster = proxyFeature.Cluster;
    var destinations = proxyFeature.AvailableDestinations;

    return next();
});
```

###  12.5. <a name='-1'></a>使用中介軟體

中介軟體可以產生日誌，控制請求是否被Proxy，影響它的Proxy位置，並加入額外的功能，如錯誤處理、重試等。

####  12.5.1. <a name='-1'></a>日誌和指標

中介軟體可以檢查請求和回應欄位以產生日誌和總計指標（metrics）。請參閱下面的"不要使用中介軟體"下關於Body的說明。

```csharp
proxyPipeline.Use(async (context, next) =>
{
    LogRequest(context);
    await next();
    LogResponse(context);
});
```

####  12.5.2. <a name='-1'></a>傳送一則立即的回應

如果中介軟體檢查一個請求並確定它不應該被Proxy，它可能會產生自己的回應並將控制權歸還給伺服器而不呼叫`next()`。

```csharp
proxyPipeline.Use((context, next) =>
{
    if (!CheckAllowedRequest(context, out var reason))
    {
        context.Response.StatusCode = StatusCodes.Status400BadRequest;
        return context.Response.WriteAsync(reason);
    }

    return next();
});
```

####  12.5.3. <a name='-1'></a>過濾目的地

Session關聯性、負載平衡等中介軟體會檢查`IReverseProxyFeature`和Cluster組態，以決定應將請求發送到哪個目標。

`AllDestinations`列出所選中Cluster中的所有目的地。

`AvailableDestinations`列出了當前被認為有資格處理請求的目的地。它被初始化為 `AllDestinations`，如果啟用了健康檢查，則不包括不健康的。`AvailableDestinations`應該在管道結束時減少到單一個目的地，否則將從其餘部分中隨機選擇一個。

`ProxiedDestination`由管道末端的Proxy邏輯設定，以指示最終使用哪個目的地。如果沒有剩餘的可用目的地，則發送 503 錯誤回應。

```csharp
proxyPipeline.Use(async (context, next) =>
{
    var proxyFeature = context.GetReverseProxyFeature();
    proxyFeature.AvailableDestinations = Filter(proxyFeature.AvailableDestinations);

    await next();

    Report(proxyFeature.ProxiedDestination);
});
```

`DestinationState`實作了`IReadOnlyList<DestinationState>`，因此可以將單一個目標分配給`AvailableDestinations`，而無需建立新清單。

####  12.5.4. <a name='-1'></a>錯誤處理

中介軟體可以將對`await next()`的呼叫包裝在`try/catch`區塊中，以處理來自後續元件的異常。管道末端的Proxy邏輯（`IHttpForwarder`）不會針對常見的請求Proxy錯誤拋出異常。這些在`HttpContext.Features`或`HttpContext.GetForwarderErrorFeature()`擴充方法提供的`IForwarderErrorFeature`中捕獲和報告。

```csharp
proxyPipeline.Use(async (context, next) =>
{
    await next();

    var errorFeature = context.GetForwarderErrorFeature();
    if (errorFeature != null)
    {
        Report(errorFeature.Error, errorFeature.Exception);
    }
});
```

如果回應尚未開始（`HttpResponse.HasStarted`），則可以將其清除（`HttpResponse.Clear()`）並發送替代回應，或者可以重置Proxy功能欄位並重試請求。

###  12.6. <a name='-1'></a>不要使用中介軟體

中介軟體應謹慎修改請求欄位（例如Header）以影響傳出的Proxy請求。此類修改可能會干擾重試等功能，並且可以通過下一節的轉換（transforms）更好地處理。

在呼叫`next()`之後修改回應欄位之前，中介軟體必須檢查`HttpResponse.HasStarted`。如果回應已經開始發送到用戶端，那麼中介軟體就不能再修改它（除了Trailer Header）。轉換可用於檢查和抑制不需要的回應。否則請參閱下一個註釋。

中介軟體應避免與請求或回應Body互動。預設情況下，Body 不會被快取，因此與它們互動會阻止它們到達目的地。雖然啟用快取是可能的，但不鼓勵這樣做，因為它會明顯增加的記憶體和延遲開銷。如果必須檢查或修改Body，建議使用包裝的`stream`方法。可以參考<a href="https://github.com/dotnet/aspnetcore/blob/24588220006bc164b63293129cc94ac6292250e4/src/Middleware/ResponseCompression/src/ResponseCompressionMiddleware.cs#L55-L73" target="_blank">ResponseCompression</a>中介軟體的範例。

##  13. <a name='-1'></a>請求和回應的轉換

###  13.1. <a name='-1'></a>概念

在Proxy請求時，通常會修改請求或回應的部分內容以適應目標伺服器器的要求或傳輸附加資料，例如用戶端端的原始IP地址。這個過程是通過轉換（Transform）來實作的。轉換類型是為應用程式全域定義的，然後各個路由提供參數以啟用和組態這些轉換。這些轉換不會修改原始請求物件，只會修改Proxy請求。

YARP不提供請求和回應Body轉換，但您可以撰寫中介軟體來執行此操作。

###  13.2. <a name='-1'></a>預設

預設情況下，所有路由都啟用以下的轉換。如本文後面展示的那樣，可以組態或禁用它們。

* `Host`
    * 抑制傳入請求的`Host`標頭。Proxy請求將預設為目標伺服器地址中指定的Host名稱。請參閱下面的`RequestHeaderOriginalHost`。
* `X-Forwarded-For` 
    * 將用戶端的IP地址設定為`X-Forwarded-For`標頭。請參閱下面的`X-Forwarded`。
* `X-Forwarded-Proto`
    * 將請求的原始Url結構的Schema（http／https）設定為`X-Forwarded-Proto`標頭。請參閱下面的`X-Forwarded`。
* `X-Forwarded-Host`
    * 將請求的原始主機設定為`X-Forwarded-Host`標頭。請參閱下面的`X-Forwarded`。
* `X-Forwarded-Prefix`
    * 將請求的原始`PathBase`（如果有）設定為`X-Forwarded-Prefix`標頭。請參閱下面的`X-Forwarded`。

例如以下對`http://IncomingHost:5000/path`的傳入請求：

```http
GET /path HTTP/1.1
Host: IncomingHost:5000
Accept: */*
header1: foo
```

將使用這些預設值轉換並Proxy到目標伺服器`https://DestinationHost:6000/`，如下所示：

```http
GET /path HTTP/1.1
Host: DestinationHost:6000
Accept: */*
header1: foo
X-Forwarded-For: 5.5.5.5
X-Forwarded-Proto: http
X-Forwarded-Host: IncomingHost:5000
```

###  13.3. <a name='-1'></a>轉換分類

轉換分為幾類別：請求、回應和回應`trailer`。目前不支持請求`trailer`，因為底層 `HttpClient`不支援它們。


###  13.4. <a name='-1'></a>加入轉換

可以通過組態或以撰寫程式碼將轉換加入到路由之中。

####  13.4.1. <a name='-1'></a>從組態加入

可以在`RouteConfig.Transforms`上組態轉換，並且可以從組態文件的`Routes`部分綁定。這些可以在不重新啟動Proxy的情況下修改和重新載入。使用一個或多個key-value字串組態轉換。

以下是常見轉換的範例：

```json
{
  "ReverseProxy": {
    "Routes": {
      "route1" : {
        "ClusterId": "cluster1",
        "Match": {
          "Hosts": [ "localhost" ]
        },
        "Transforms": [
          { "PathPrefix": "/apis" },
          {
            "RequestHeader": "header1",
            "Append": "bar"
          },
          {
            "ResponseHeader": "header2",
            "Append": "bar",
            "When": "Always"
          },
          { "ClientCert": "X-Client-Cert" },
          { "RequestHeadersCopy": "true" },
          { "RequestHeaderOriginalHost": "true" },
          {
            "X-Forwarded": "proto,host,for,prefix",
            "Append": "true",
            "Prefix": "X-Forwarded-"
          }
        ]
      },
      "route2" : {
        "ClusterId": "cluster1",
        "Match": {
          "Path": "/api/{plugin}/stuff/{**remainder}"
        },
        "Transforms": [
          { "PathPattern": "/foo/{plugin}/bar/{**remainder}" },
          {
            "QueryStringParameter": "q",
            "Append": "plugin"
          }
        ]
      }
    },
    "Clusters": {
      "cluster1": {
        "Destinations": {
          "cluster1/destination1": {
            "Address": "https://localhost:10001/Path/Base"
          }
        }
      }
    }
  }
}
```

所有組態項目都被視為不區分大小寫，但目標伺服器可能會將結果值視為區分大小寫或不區分大小寫，例如Path。

本文件稍後將介紹這些轉換的詳細資訊。

想要將自定義轉換與組態的轉換部分整合的開發人員可以使用下面描述的`ITransformFactory`來實現。

####  13.4.2. <a name='-1'></a>從程式碼加入

可以通過呼叫`AddTransforms`方法以撰寫程式碼將轉換加入到路由。

可以從`Startup.ConfigureServices`呼叫`AddTransforms`以提供用於組態轉換的回呼（callback ）。每次建構或重建路由時都會呼叫此回調，並允許開發人員檢查`RouteConfig`資訊並有條件地為其加入轉換。

`AddTransforms`回呼調提供了一個`TransformBuilderContext`，可以在其中加入轉換或組態轉換。大多數轉換都提供了`TransformBuilderContext`的擴充方法，使它們更容易加入。這些是下面文件的擴充，帶有單獨的轉換描述。

`TransformBuilderContext`還包括一個`IServiceProvider`用於存取任何需要的服務。

```csharp
services.AddReverseProxy()
    .LoadFromConfig(_configuration.GetSection("ReverseProxy"))
    .AddTransforms(builderContext =>
    {
        // 加入到所有路由。
        builderContext.AddPathPrefix("/prefix");

        // 有條件地為需要身分驗證的路由加入轉換。
        if (!string.IsNullOrEmpty(builderContext.Route.AuthorizationPolicy))
        {
            builderContext.AddRequestTransform(async transformContext =>
            {
                transformContext.ProxyRequest.Headers.Add("CustomHeader", "CustomValue");
            });
        }
    });
```

有關更進階的控制，請參閱下面描述的`ITransformProvider`。

###  13.5. <a name='-1'></a>請求轉換

請求轉換包括請求path、query、HTTP版本、method和header。在程式碼中，這些由`RequestTransformContext`物件象表示，並由抽像類別`RequestTransform`的實作處理。

注意：

* Proxy請求的schema (http/https)、權限和基礎路徑取自目標伺服器地址（上例中的 `https://localhost:10001/Path/Base`），不應該通過轉換進行修改。
* `Host`標頭可以被獨立於權限的轉換覆寫，請參閱下面的`RequestHeader`。
* 建構Proxy請求時不使用請求的原始`PathBase`屬性，請參閱`X-Forwarded`。
* 預設情況下，所有傳入的請求Header都會復製到Proxy請求，但`Host`標頭除外（請參閱「預設」一節）。預設情況下還會加入`X-Forwarded`標頭。可以使用以下轉換組態這些行為。可以指定其他請求標頭，或者可以通過將請求標頭設定為empty來排除請求標頭。

以下是由其主要組態鍵值識別的內建轉換。這些轉換按照它們在路由組態中指定的順序套用。

####  13.5.1. <a name='PathPrefix'></a>PathPrefix

**修改請求路徑添加前綴值。**

| Key        | Value     | Required |
|------------|-----------|----------|
| `PathPrefix` | 以"/"開頭的路徑 | yes      |

組態：

```json
{ "PathPrefix": "/prefix" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformPathPrefix(prefix: "/prefix");
```

```csharp
transformBuilderContext.AddPathPrefix(prefix: "/prefix");
```

範例：

`/request/path`會變成`/prefix/request/path`。

這將為請求路徑加入指定的前綴值。

####  13.5.2. <a name='PathRemovePrefix'></a>PathRemovePrefix

**修改請求路徑刪除前綴值。**

| Key                | Value     | Required |
|--------------------|-----------|----------|
| `PathRemovePrefix` | 以"/"開頭的路徑 | yes      |

組態：

```json
{ "PathRemovePrefix": "/prefix" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformPathRemovePrefix(prefix: "/prefix");
```

```csharp
transformBuilderContext.AddPathRemovePrefix(prefix: "/prefix");
```

範例：

* `/prefix/request/path`會變成`/request/path`。
* `/prefix2/request/path`不會被修改。

這將從請求路徑中刪除相符的前綴。在路徑段界限（`/`）上進行相符比較。如果前綴不相符，則不進行任何更改。

####  13.5.3. <a name='PathSet'></a>PathSet

**用指定的值替換請求路徑。**

| Key       | Value     | Required |
|-----------|-----------|----------|
| `PathSet` | 以"/"開頭的路徑 | yes      |

組態：

```json
{ "PathSet": "/newpath" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformPathSet(path: "/newpath");
```

```csharp
transformBuilderContext.AddPathSet(path: "/newpath");
```

範例：

`/request/path`會變成`/newpath`。

這將使用指定的值設定請求路徑。

####  13.5.4. <a name='PathPattern'></a>PathPattern

**使用Pattern樣板替換請求路徑。**

| Key           | Value       | Required |
|---------------|-------------|----------|
| `PathPattern` | 以"/"開頭的路徑樣板 | yes      |

組態：

```json
{ "PathPattern": "/my/{plugin}/api/{**remainder}" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformPathRouteValues(pattern: new PathString("/my/{plugin}/api/{**remainder}"));
```

```csharp
transformBuilderContext.AddPathRouteValues(pattern: new PathString("/my/{plugin}/api/{**remainder}"));
```

這將使用指定的值設定請求路徑，並將任何`{}`段落替換為關聯的路由值。`{}`沒有相符路由值的段落將被刪除。最後的`{}`段落可以標記為`{**remainder}`以指示這是一個可能包含多個路徑段落的各種狀況的段落。有關路由樣板的更多資訊，請參閱 ASP.NET Core 的<a href="https://docs.microsoft.com/zh-tw/aspnet/core/fundamentals/routing" target="_blank">路由文件</a>。

範例：

| Step             | Value                               |
|------------------|-------------------------------------|
| Route定義          | `/api/{plugin}/stuff/{**remainder}` |
| 請求路徑             | `/api/v1/stuff/more/stuff`          |
| `{plugin}`值      | `v1`                                |
| `{**remainder}`值 | `more/stuff`                        |
| PathPattern      | `/my/{plugin}/api/{**remainder}`    |
| 結果               | `/my/v1/api/more/stuff`             |

####  13.5.5. <a name='QueryValueParameter'></a>QueryValueParameter

**在請求查詢字串中加入或替換參數。**

| Key                   | Value     | Required |
|-----------------------|-----------|----------|
| `QueryValueParameter` | 查詢字串參數的名稱 | yes      |
| `Set/Append`          | 靜態值       | yes      |

組態：

```json
{
  "QueryValueParameter": "foo",
  "Append": "bar"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformQueryValue(queryKey: "foo", value: "bar", append: true);
```

```csharp
transformBuilderContext.AddQueryValue(queryKey: "foo", value: "bar", append: true);
```

這將加入一個名為`foo`的查詢字串參數並將其設定置為靜態值`bar`。

範例：

| Step                | Value                |
|---------------------|----------------------|
| Query               | `?a=b`               |
| QueryValueParameter | `foo`                |
| Append              | `remainder`          |
| Result              | `?a=b&foo=remainder` |

####  13.5.6. <a name='QueryRouteParameter'></a>QueryRouteParameter

**使用路由組態中的值加入或替換查詢字串參數。**

| Key                   | Value     | Required |
|-----------------------|-----------|----------|
| `QueryRouteParameter` | 查詢字串參數的名稱 | yes      |
| `Set/Append`          | 路由值的名稱    | yes      |

組態：

```json
{
  "QueryRouteParameter": "foo",
  "Append": "remainder"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformQueryRouteValue(queryKey: "foo", routeValueKey: "remainder", append: true);
```

```csharp
transformBuilderContext.AddQueryRouteValue(queryKey: "foo", routeValueKey: "remainder", append: true);
```

這將加入一個名為`foo`的查詢字串參數，並將它設定為關聯路由值的值。

範例：

| Step                | Value               |
|---------------------|---------------------|
| Route定義             | `/api/{*remainder}` |
| 請求路徑                | `/api/more/stuff`   |
| `{Remainder }`值     | `more/stuff`        |
| QueryRouteParameter | `foo`               |
| PathPattern         | `Append`            |
| 結果                  | `?foo=more/stuff`   |

> Bruce備註：結果覺得奇怪，待驗證。

####  13.5.7. <a name='QueryRemoveParameter'></a>QueryRemoveParameter

**從請求查詢字串中刪除指定的參數。**

| Key                   | Value     | Required |
|-----------------------|-----------|----------|
| `QueryRemoveParameter` | 查詢字串參數的名稱 | yes      |

組態：

```json
{ "QueryRemoveParameter": "foo" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformQueryRemoveKey(queryKey: "foo");
```

```csharp
transformBuilderContext.AddQueryRemoveKey(queryKey: "foo");
```

如果請求中存在名為`foo`的查詢字串參數，這將刪除此參數。

範例：

| Step                 | Value        |
|----------------------|--------------|
| 請求路徑                 | `?a=b&foo=c` |
| `QueryRemoveParameter` | `foo`        |
| 結果                   | `?a=b`       |

####  13.5.8. <a name='HttpMethodChange'></a>HttpMethodChange

**更改請求中使用的HTTP動詞。**

組態：

```json
{
  "HttpMethodChange": "PUT",
  "Set": "POST",
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformHttpMethodChange(fromHttpMethod: HttpMethods.Put, toHttpMethod: HttpMethods.Post);
```

```csharp
transformBuilderContext.AddHttpMethodChange(fromHttpMethod: HttpMethods.Put, toHttpMethod: HttpMethods.Post);
```

這會將`PUT`請求更改為`POST`。

####  13.5.9. <a name='RequestHeadersCopy'></a>RequestHeadersCopy

**設定是否將傳入的請求標頭複製到出站請求。**

| Key                  | Value      | Default | Required |
|----------------------|------------|---------|----------|
| `RequestHeadersCopy` | true/false | true    | yes      |

組態：

```json
{ "RequestHeadersCopy": "false" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformCopyRequestHeaders(copy: false);
```

```csharp
transformBuilderContext.CopyRequestHeaders = false;
```

這設定是否將所有傳入請求標頭複製到Proxy請求。預設情況下啟用此設定，可以通過使用`false` 組態轉換來禁用此設定。如果禁用此選項，參考特定標頭的轉換仍將運作。

####  13.5.10. <a name='RequestHeaderOriginalHost'></a>RequestHeaderOriginalHost

**指定是否應將傳入請求`Host`標頭複製到Proxy請求。**

| Key                         | Value      | Default | Required |
|-----------------------------|------------|---------|----------|
| `RequestHeaderOriginalHost` | true/false | false   | yes      |

組態：

```json
{ "RequestHeaderOriginalHost": "true" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformUseOriginalHostHeader(useOriginal: true);
```

```csharp
transformBuilderContext.UseOriginalHost = true;
```

這指定是否應將傳入請求`Host`標頭複製到Proxy請求。預設情況下禁用此設定，可以通過使用`true`組態轉換來啟用此設定。直接參考`Host`標頭的轉換將覆寫此轉換。

####  13.5.11. <a name='RequestHeader'></a>RequestHeader

**加入或替換請求標頭。**

| Key             | Value     | Required |
|-----------------|-----------|----------|
| `RequestHeader` | Header的名稱 | yes      |
| `Set/Append`    | Header的值  | yes      |

組態：

```json
{
  "RequestHeader": "MyHeader",
  "Set": "MyValue",
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformRequestHeader(headerName: "MyHeader", value: "MyValue", append: false);
```

```csharp
transformBuilderContext.AddRequestHeader(headerName: "MyHeader", value: "MyValue", append: false);
```

範例：

```http
MyHeader: MyValue
```

這會設定或附加名稱標頭的值。`Set`替換任何現有的標頭。`Append`加入擁有指定值的附加標頭。注意：不建議將""設定為標頭值，這可能會導致未定義的行為。

####  13.5.12. <a name='RequestHeaderRemove'></a>RequestHeaderRemove

| Key                   | Value     | Required |
|-----------------------|-----------|----------|
| `RequestHeaderRemove` | Header的名稱 | yes      |

組態：

```json
{
  "RequestHeaderRemove": "MyHeader"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformRequestHeaderRemove(headerName: "MyHeader");
```

```csharp
transformBuilderContext.AddRequestHeaderRemove(headerName: "MyHeader");
```

範例：

```http
MyHeader: MyValue
AnotherHeader: AnotherValue
```

這將刪除名稱為`MyHeader`的標頭。

####  13.5.13. <a name='RequestHeadersAllowed'></a>RequestHeadersAllowed

| Key                     | Value           | Required |
|-------------------------|-----------------|----------|
| `RequestHeadersAllowed` | 允許的標頭名稱的分號分隔清單。 | yes      |

組態：

```json
{
  "RequestHeadersAllowed": "Header1;header2"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformRequestHeadersAllowed("Header1", "header2");
```

```csharp
transformBuilderContext.AddRequestHeadersAllowed("Header1", "header2");
```

預設情況下，YARP會將大多數請求標頭複製到Proxy請求（請參閱`RequestHeadersCopy`）。一些安全模型只允許Proxy特定的標頭。此轉換禁用`RequestHeadersCopy`並僅複製指定的標頭。如果未包含在允許列表中，其他修改或附加到現有標頭的轉換可能會受到影響。

請注意，預設情況下YARP不會複製一些標頭，因為它們是特定於連接或其他安全敏感的（例如 `Connection`、`Alt-Svc`）。將這些標頭名稱放在允許列表中將繞過此限制，但強烈建議不要這樣做，因為它可能會對Proxy的功能產生負面影響或導致安全漏洞。

範例：

```http
Header1: value1
Header2: value2
AnotherHeader: AnotherValue
```

只有`header1`和`header2`被複製到Proxy請求。

####  13.5.14. <a name='X-Forwarded'></a>X-Forwarded

**加入包含有關原始用戶端請求的資訊的標頭。**

| Key            | Value                                                         | Default        | Required |
|----------------|---------------------------------------------------------------|----------------|----------|
| `X-Forwarded`  | 應用於下面列出的所有`X-Forwarded-*`的預設操作（`Set`、`Append`、`Remove`、`Off`） | `Set`          | yes      |
| `For`          | 套用於此標頭的操作                                                     | `Set`          | no       |
| `Proto`        | 套用於此標頭的操作                                                     | `Set`          | no       |
| `Host`         | 套用於此標頭的操作                                                     | `Set`          | no       |
| `Prefix`       | 套用於此標頭的操作                                                     | `Set`          | no       |
| `HeaderPrefix` | 標頭名稱前綴                                                        | `X-Forwarded-` | no       |

操作`Off`完全禁用轉換。

組態：

```json
{
  "X-Forwarded": "Set",
  "For": "Remove",
  "Proto": "Append",
  "Prefix": "Off",
  "HeaderPrefix": "X-Forwarded-"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformXForwarded(
  headerPrefix = "X-Forwarded-",
  ForwardedTransformActions xDefault = ForwardedTransformActions.Set,
  ForwardedTransformActions? xFor = null,
  ForwardedTransformActions? xHost = null,
  ForwardedTransformActions? xProto = null,
  ForwardedTransformActions? xPrefix = null);
```

```csharp
transformBuilderContext.AddXForwarded(ForwardedTransformAction.Set);
transformBuilderContext.AddXForwardedFor(headerName: "X-Forwarded-For", ForwardedTransformAction.Append);
transformBuilderContext.AddXForwardedHost(headerName: "X-Forwarded-Host", ForwardedTransformAction.Append);
transformBuilderContext.AddXForwardedProto(headerName: "X-Forwarded-Proto", ForwardedTransformAction.Off);
transformBuilderContext.AddXForwardedPrefix(headerName: "X-Forwarded-Prefix", ForwardedTransformAction.Remove);
```

範例：

```http
X-Forwarded-For: 5.5.5.5
X-Forwarded-Proto: https
X-Forwarded-Host: IncomingHost:5000
X-Forwarded-Prefix: /path/base
```

禁用預設標題：

組態：

```json
{ "X-Forwarded": "Off" }
```

程式碼：

```csharp
transformBuilderContext.UseDefaultForwarders = false;
```

當Proxy連接到目標伺服器時，此連接獨立於用戶端與Proxy建立的連接。目標伺服器可能需要原始連接資訊來進行安全檢查，並為連結和重導向正確產生絕對路徑的URI。為了使有關用戶端連接的資訊能夠傳遞到目標，可以加入一組額外的標頭。在建立`Forwarded`標準之前，一個常見的解決方案是使用`X-Forwarded-*`標頭。沒有定義`X-Forwarded-*`標頭的官方標準，並且實作有所不同，請檢查您的目標伺服器以獲得支援。

即使未在路由組態中指定，預設情況下也會啟用此轉換。

將`X-Forwarded`的值設定為逗號分隔的清單，其中包含您需要啟用的標頭。預設情況下啟用所有`for`標頭。可以通過指定`Off`來禁用所有功能。`Prefix`指定用於每個標頭的標頭名稱前綴。使用預設的`X-Forwarded-` 前綴，產生的標頭將是`X-Forwarded-For`、`X-Forwarded-Proto`、`X-Forwarded-Host`和`X-Forwarded-Prefix`。

轉換操作指定如何將每個標頭與同名的現有標頭組合。它可以是`Set`、`Append`、`Remove`或`Off`（完全禁用轉換）。走遍多個Proxy的請求可能會累積此類標頭的清單，並且目標伺服器將需要評估此清單以確定原始值。如果操作是`Set`並且相關的值在請求中不可用（例如`RemoteIpAddress` 為`null`），任何現有的標頭仍將被刪除以防止欺騙。

`{Prefix}For`標頭值取自`HttpContext.Connection.RemoteIpAddress`代表先前呼叫者的IP地址。Port不包括在內。IPv6地址不包括邊界`[]`括號。

`{Prefix}Proto`標頭值取自`HttpContext.Request.Scheme`，指示之前的呼叫者是使用HTTP還是HTTPS。

`{Prefix}Host`標頭值取自傳入請求的`Host`標頭。這與上面指定的`RequestHeaderOriginalHost`無關。Unicode/IDN Host是punycode編碼的。

`{Prefix}Prefix`標頭值取自`HttpContext.Request.PathBase`。產生Proxy請求時不使用`PathBase` 屬性，因此目標伺服器將需要原始值以正確產生連結和重導向。此值採用百分比編碼的Uri格式。

####  13.5.15. <a name='Forwarded'></a>Forwarded

**加入包含有關原始客戶端請求的資訊的標頭。**

| Key       | Value                                                                                                             | Default | Required |
|-----------|-------------------------------------------------------------------------------------------------------------------|---------|----------|
| Forwarded | 包含以下任何值的逗號分隔清單：for,by,proto,host                                                                                  | none    | yes      |
| ForFormat | Random/RandomAndPort/RandomAndRandomPort/Unknown/UnknownAndPort/UnknownAndRandomPort/Ip/IpAndPort/IpAndRandomPort | Random  | no       |
| ByFormat  | Random/RandomAndPort/RandomAndRandomPort/Unknown/UnknownAndPort/UnknownAndRandomPort/Ip/IpAndPort/IpAndRandomPort | Random  | no       |
| Action    | 應用於此Header的操作（Set, Append, Remove, Off）                                                                           | Set     | no       |

組態：

```json
{
  "Forwarded": "by,for,host,proto",
  "ByFormat": "Random",
  "ForFormat": "IpAndPort",
  "Action": "Append"
},
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformForwarded(useHost: true, useProto: true, forFormat: NodeFormat.IpAndPort, ByFormat: NodeFormat.Random, action: ForwardedTransformAction.Append);
```

```csharp
transformBuilderContext.AddForwarded(useHost: true, useProto: true, forFormat: NodeFormat.IpAndPort, ByFormat: NodeFormat.Random, action: ForwardedTransformAction.Append);
```

範例：

```http
Forwarded: proto=https;host="localhost:5001";for="[::1]:20173";by=_YQuN68tm6
```

`Forwarded`標頭由<a href="https://datatracker.ietf.org/doc/html/rfc7239" target="_blank">RFC 7239</a>定義。它整合了許多與非官方`X-Forwarded`標頭相同的功能，將資訊流向目標伺服器，否則使用Proxy會掩蓋這些資訊。

啟用此轉換將禁用預設的`X-Forwarded`轉換，因為它們以另一種格式攜帶類似資訊。`X-Forwarded`轉換仍然可以顯式啟用。

Action：這指定了轉換應如何處理現有的`Forwarded`標頭。它可以是"Set"、"Append"、"Remove"或"Off"（完全禁用轉換）。走訪多個Proxy的請求可能會累積此類標頭的清單，並且目標伺服器將需要評估該清單以確定原始值。

Proto：此值取自`HttpContext.Request.Scheme`，指示先前的呼叫者是使用HTTP還是HTTPS。

Host：該值取自傳入請求的`Host`標頭。這與上面指定的`RequestHeaderOriginalHost`無關。Unicode/IDN主機名稱是用punycode編碼的。

For：此值標識先前的呼叫者。IP地址取自`HttpContext.Connection.RemoteIpAddress`。有關詳細信息，請參閱下面的`ByFormat`和`ForFormat`。

By：此值標識Proxy接收請求的位置。IP地址取自`HttpContext.Connection.LocalIpAddress`。有關詳細信息，請參閱下面的`ByFormat`和`ForFormat`。

ByFormat和ForFormat：RFC允許對`By`和`For`欄位使用多種格式。它要求預設格式使用此處標識為`Random`的混淆標識符號。

| Format               | Description                                    | Example                      |
|----------------------|------------------------------------------------|------------------------------|
| Random               | 每個請求隨機產成的混淆標識符號。這允許診斷追蹤場景，同時出於隱私原因限制唯一標識資訊的流動。 | `by=_YQuN68tm6`              |
| RandomAndPort        | 隨機標識符號加上Port。                                  | `by="_YQuN68tm6:80"`         |
| RandomAndRandomPort  | 隨機標識符號加上Port的另一個隨機標識符號。                        | `by="_YQuN68tm6:_jDw5Cf3tQ"` |
| Unknown              | 這可以在前面實體的身份未知時使用，但Proxy伺服器仍希望發出請求已轉發的信號。       | `by=unknown`                 |
| UnknownAndPort       | 未知標識符號加上Port（如果可用）。                            | `by="unknown:80"`            |
| UnknownAndRandomPort | Port的未知標識符號加上隨機標識符。                            | `by="unknown:_jDw5Cf3tQ"`    |
| Ip                   | 包括括號的IPv4地址或IPv6地址。                            | `by="[::1]"`                 |
| IpAndPort            | IP地址加上Port。                                    | `by="[::1]:80"`              |
| IpAndRandomPort      | IP地址加上Port的隨機標識符號。                             | `by="[::1]:_jDw5Cf3tQ"`      |

####  13.5.16. <a name='ClientCert'></a>ClientCert

**將入站連接上使用的用戶端憑證作為標頭轉發到目標。**

| Key        | Value | Required |
|------------|-------|----------|
| ClientCert | 標頭名稱  | yes      |

組態：

```json
{ "ClientCert": "X-Client-Cert" }
```


程式碼：

```csharp
routeConfig = routeConfig.WithTransformClientCertHeader(headerName: "X-Client-Cert");
```

```csharp
transformBuilderContext.AddClientCertHeader(headerName: "X-Client-Cert");
```

範例：

```http
X-Client-Cert: SSdtIGEgY2VydGlmaWNhdGU...
```

由於入站和出站連接是獨立的，因此需要有一種方法可以將任何入站用戶端憑證傳遞到目標伺服器。此轉換導致從`HttpContext.Connection.ClientCertificate`獲取的用戶端憑證進行Base64編碼並設定為指定標頭名稱的值。目標伺服器可能需要該憑證來驗證用戶端。沒有定義此標頭的標準，並且實現有所不同，請檢查您的目標伺服器以獲得支持。

預設情況下，伺服器對傳入的客戶端憑證進行最少的驗證。憑證應在Proxy或目標中進行驗證，有關詳細信息，請參閱<a href="https://docs.microsoft.com/zh-tw/aspnet/core/security/authentication/certauth" target="_blank">用戶端憑證身份驗證</a>文件。

僅當連接上已存在用戶端憑證時，此轉換才適用。如果需要基於每個路由從用戶端請求，請參閱<a href="https://docs.microsoft.com/zh-tw/aspnet/core/security/authentication/certauth#optional-client-certificates" target="_blank">選擇性用戶端憑證</a>文件。

###  13.6. <a name='-1'></a>回應和回應尾端

預設情況下，所有回應標頭和尾端（Trailer）都從Proxy回應複製到傳出用戶端回應。回應和回應尾端轉換可以指定它們是否應該僅應用於成功回應或所有回應。

在程式碼中，這些實作為抽像類別`ResponseTransform`和`ResponseTrailersTransform`的衍生類別。

####  13.6.1. <a name='ResponseHeadersCopy'></a>ResponseHeadersCopy

**設定是否將目標回應標頭複製到用戶端。**

| Key                 | Value      | Default | Required |
|---------------------|------------|---------|----------|
| ResponseHeadersCopy | true/false | true    | yes      |

組態：

```json
{ "ResponseHeadersCopy": "false" }
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformCopyResponseHeaders(copy: false);
```

```csharp
transformBuilderContext.CopyResponseHeaders = false;
```

這設定是否將所有Proxy回應標頭複製到用戶端回應。預設情況下啟用此設定，可以通過使用`false` 值組態轉換來禁用此設定。如果禁用此選項，參考特定標頭的轉換仍將運作。

####  13.6.2. <a name='ResponseHeader'></a>ResponseHeader

| Key            | Value                  | Default | Required |
|----------------|------------------------|---------|----------|
| ResponseHeader | 標頭名稱               | none    | yes      |
| Set/Append     | 標頭值                 | none    | yes      |
| When           | Success/Always/Failure | Success | no       |

組態：

```json
{
  "ResponseHeader": "HeaderName",
  "Append": "value",
  "When": "Success"
}
```


程式碼：

```csharp
routeConfig = routeConfig.WithTransformResponseHeader(headerName: "HeaderName", value: "value", append: true, ResponseCondition.Success);
```

```csharp
transformBuilderContext.AddResponseHeader(headerName: "HeaderName", value: "value", append: true, always: ResponseCondition.Success);
```

範例：

```http
HeaderName: value
```

這將設定或附加命名回應標頭的值。`Set`替換任何現有的標頭。`Append`加入具有指定值的附加標頭。注意：不建議將""設定為標頭值，這可能會導致未定義的行為。

`When`指定是否應為所有、成功或失敗回應包含回應標頭。任何狀態碼小於400的回應都會被視為成功。

####  13.6.3. <a name='ResponseHeaderRemove'></a>ResponseHeaderRemove

**刪除回應標頭。**

| Key                  | Value                  | Default | Required |
|----------------------|------------------------|---------|----------|
| ResponseHeaderRemove | 標頭名稱               | none    | yes      |
| When                 | Success/Always/Failure | Success | no       |

組態：

```json
{
  "ResponseHeaderRemove": "HeaderName",
  "When": "Success"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformResponseHeaderRemove(headerName: "HeaderName", ResponseCondition.Success);
```

```csharp
transformBuilderContext.AddResponseHeaderRemove(headerName: "HeaderName", ResponseCondition.Success);
```

範例：

```http
HeaderName: value
AnotherHeader: another-value
```

這將刪除命名為`HeaderName`的回應標頭。

`When`指定是否應為所有、成功或失敗回應包含回應標頭。任何狀態碼小於400的回應都會被視為成功。

####  13.6.4. <a name='ResponseHeadersAllowed'></a>ResponseHeadersAllowed

| Key                    | Value           | Required |
|------------------------|-----------------|----------|
| ResponseHeadersAllowed | 允許的標頭名稱的分號分隔清單。 | yes      |

組態：

```json
{
  "ResponseHeadersAllowed": "Header1;header2"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformResponseHeadersAllowed("Header1", "header2");
```

```csharp
transformBuilderContext.AddResponseHeadersAllowed("Header1", "header2");
```

預設情況下，YARP會從Proxy回應中複製大多數回應標頭（請參閱`ResponseHeadersCopy`）。一些安全模型只允許Proxy特定的標頭。此轉換禁用`ResponseHeadersCopy`並僅複製指定的標頭。如果未包含在允許清單中，其他修改或附加到現有標頭的轉換可能會受到影響。

請注意，預設情況下YARP不會複製一些標頭，因為它們是特定於連接或其他安全敏感性（例如`Connection`、`Alt-Svc`）。將這些標頭名稱放在允許清單中將繞過原始限制，強烈建議不要這樣做，因為它可能會對Proxy的功能產生負面影響或導致安全漏洞。

範例：

```http
Header1: value1
Header2: value2
AnotherHeader: AnotherValue
```

只有`header1`和`header2`從Proxy回應中複製。

####  13.6.5. <a name='ResponseTrailersCopy'></a>ResponseTrailersCopy

**設定是否將目標尾端隨回應標頭複製到用戶端。**

| Key                  | Value      | Default | Required |
|----------------------|------------|---------|----------|
| ResponseTrailersCopy | true/false | true    | yes      |

組態：

{ "ResponseTrailersCopy": "false" }

程式碼：

```csharp
routeConfig = routeConfig.WithTransformCopyResponseTrailers(copy: false);
```

```csharp
transformBuilderContext.CopyResponseTrailers = false;
```

這設定是否將所有Proxy回應尾端複製到用戶端回應。預設情況下啟用此設定，可以通過使用`false`值組態轉換來禁用此設置。如果禁用此選項，參考特定標頭的轉換仍將運行。

####  13.6.6. <a name='ResponseTrailer'></a>ResponseTrailer

**加入或替換尾端回應標頭**

| Key            | Value                  | Default | Required |
|----------------|------------------------|---------|----------|
| ResponseTrailer| 標頭名稱               | none    | yes      |
| Set/Append     | 標頭值                 | none    | yes      |
| When           | Success/Always/Failure | Success | no       |

組態：

```json
{
  "ResponseTrailer": "HeaderName",
  "Append": "value",
  "When": "Success"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformResponseTrailer(headerName: "HeaderName", value: "value", append: true, ResponseCondition.Success);
```

```csharp
transformBuilderContext.AddResponseTrailer(headerName: "HeaderName", value: "value", append: true, ResponseCondition.Success);
```

範例：

```http
HeaderName: value
```

回應尾端是在回應本文（response body）尾端發送的標頭。對`Trailer`的支持在HTTP/1.1實作中並不常見，但在HTTP/2實作中變得很普遍。檢查您的用戶端和伺服器以取得支持。

`ResponseTrailer`遵循與`ResponseHeader`相同的結構和指導。

####  13.6.7. <a name='ResponseTrailerRemove'></a>ResponseTrailerRemove

**刪除尾端回應標頭。**

| Key                   | Value                  | Default | Required |
|-----------------------|------------------------|---------|----------|
| ResponseTrailerRemove | 標頭名稱               | none    | yes      |
| When                  | Success/Always/Failure | Success | no       |

組態：

```json
{
  "ResponseTrailerRemove": "HeaderName",
  "When": "Success"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformResponseTrailerRemove(headerName: "HeaderName", ResponseCondition.Success);
```

```csharp
transformBuilderContext.AddResponseTrailerRemove(headerName: "HeaderName", ResponseCondition.Success);
```

範例：

```http
HeaderName: value
AnotherHeader: another-value
```

這將刪除命名`HeaderName`的尾端標頭。

`ResponseTrailerRemove`遵循與`ResponseHeaderRemove`相同的結構和指導。

####  13.6.8. <a name='ResponseTrailersAllowed'></a>ResponseTrailersAllowed

| Key                     | Value           | Required |
|-------------------------|-----------------|----------|
| ResponseTrailersAllowed | 允許的標頭名稱的分號分隔清單。 | yes      |

組態：

```json
{
  "ResponseTrailersAllowed": "Header1;header2"
}
```

程式碼：

```csharp
routeConfig = routeConfig.WithTransformResponseTrailersAllowed("Header1", "header2");
```

```csharp
transformBuilderContext.AddResponseTrailersAllowed("Header1", "header2");
```

預設情況下，YARP會從Proxy回應中複製大多數回應標頭（請參閱`ResponseTrailersCopy`）。一些安全模型只允許Proxy特定的標頭。此轉換禁用`ResponseTrailersCopy`並僅複製指定的標頭。如果未包含在允許清單中，其他修改或附加到現有標頭的轉換可能會受到影響。

請注意，預設情況下YARP不會複製一些標頭，因為它們是特定於連接或其他安全敏感性（例如`Connection`、`Alt-Svc`）。將這些標頭名稱放在允許清單中將繞過原始限制，強烈建議不要這樣做，因為它可能會對Proxy的功能產生負面影響或導致安全漏洞。

範例：

```http
Header1: value1
Header2: value2
AnotherHeader: AnotherValue
```

只有`header1`和`header2`從Proxy回應中複製。

###  13.7. <a name='-1'></a>可擴充性

####  13.7.1. <a name='AddRequestTransform'></a>AddRequestTransform

`AddRequestTransform`是一個`TransformBuilderContext`擴展方法，它將請求轉換定義為`Func<RequestTransformContext, ValueTask>`。這允許在不實作`RequestTransform`衍生類別的情況下建立自定義請求轉換。

####  13.7.2. <a name='AddResponseTransform'></a>AddResponseTransform

`AddResponseTransform`是一個`TransformBuilderContext`擴展方法，它將請求轉換定義為`Func<ResponseTransformContext, ValueTask>`。這允許在不實作`RequestTransform`衍生類別的情況下建立自定義請求轉換。

####  13.7.3. <a name='AddResponseTrailersTransform'></a>AddResponseTrailersTransform

`AddResponseTrailersTransform`是一個`TransformBuilderContext`擴充方法，它將回應尾端轉換定義為`Func<ResponseTrailersTransformContext, ValueTask>`。這允許在不實作`ResponseTrailersTransform`衍生類別的情況下建立自定義回應尾端轉換。

####  13.7.4. <a name='RequestTransform'></a>RequestTransform

所有請求轉換都必須衍生自抽象基礎類別`RequestTransform`。這些可以隨意修改Proxy的`HttpRequestMessage`。避免讀取或修改請求本文（request body），因為這可能會中斷Proxy流程。還可以考慮在`TransformBuilderContext`上加入參數化擴充方法，以提高可發現性和易用性。

####  13.7.5. <a name='ResponseTransform'></a>ResponseTransform

所有回應轉換都必須衍生自抽象基礎類別`ResponseTransform`。這些可以隨意修改Proxy的`HttpResponse`。避免讀取或修改回應本文（response body），因為這可能會中斷Proxy流程。還可以考慮在`TransformBuilderContext`上加入參數化擴充方法，以提高可發現性和易用性。

####  13.7.6. <a name='ResponseTrailersTransform'></a>ResponseTrailersTransform

所有回應尾端轉換必須衍生自抽象基礎類別`ResponseTrailersTransform`。這些可以自由修改用戶端的`HttpResponse`尾端。這些在回應本文之後運行，不應嘗試修改回應標頭或本文。還可以考慮在`TransformBuilderContext`上加入參數化擴充方法，以提高可發現性和易用性。

####  13.7.7. <a name='ITransformProvider'></a>ITransformProvider

`ITransformProvider`提供上述`AddTransforms`的功能以及DI整合和驗證支援。

`ITransformProvider`可以通過呼叫`AddTransforms<T>()`在DI容器中註冊。可以註冊多個`ITransformProvider`實作，所有實作都將運行。

`ITransformProvider`有兩個方法：`Validate`和`Apply`。`Validate`使您有機會檢查組態轉換所需的任何參數的路由，例如自定義中繼資料（metadata），並在任何所需值遺失或無效時返回環境（Context）驗證錯誤。`Apply`方法提供與上面討論的`AddTransform`相同的功能，為每個路由加入和組態轉換。

```csharp
services.AddReverseProxy()
    .LoadFromConfig(_configuration.GetSection("ReverseProxy"))
    .AddTransforms<MyTransformProvider>();
```

```csharp
internal class MyTransformProvider : ITransformProvider
{
    public void Validate(TransformValidationContext context)
    {
        // Check all routes for a custom property and validate the associated
        // transform data.
        string value = null;
        if (context.Route.Metadata?.TryGetValue("CustomMetadata", out value) ?? false)
        {
            if (string.IsNullOrEmpty(value))
            {
                context.Errors.Add(new ArgumentException(
                    "A non-empty CustomMetadata value is required")); 
            }
        }
    }

    public void Apply(TransformBuilderContext transformBuildContext)
    {
        // Check all routes for a custom property and add the associated transform.
        string value = null;
        if (transformBuildContext.Route.Metadata?.TryGetValue("CustomMetadata", out value)
            ?? false)
        {
            if (string.IsNullOrEmpty(value))
            {
                throw new ArgumentException(
                    "A non-empty CustomMetadata value is required");
            }

            transformBuildContext.AddRequestTransform(transformContext =>
            {
                transformContext.ProxyRequest.Headers.Add("CustomHeader", value);
                return default;
            });
        }
    }
}
```

####  13.7.8. <a name='ITransformFactory'></a>ITransformFactory

想要將自定義轉換與組態的轉換部分整合的開發人員可以實作`ITransformFactory`。這應該使用`AddTransformFactory<T>()`方法在 DI容器中註冊。可以註冊多個工廠以全部使用。

`ITransformFactory`提供了兩種方法：`Validate`和`Build`。它們一次處理一組轉換值，由`IReadOnlyDictionary<string, string>`表示。載入組態時會呼叫`Validate`方法來驗證內容並報告所有錯誤。任何報告的錯誤都將阻止套用組態。

`Build`方法採用指定的組態並為路由產生關聯的轉換執行個體。

```csharp
services.AddReverseProxy()
    .LoadFromConfig(_configuration.GetSection("ReverseProxy"))
    .AddTransformFactory<MyTransformFactory>();
```

```csharp
internal class MyTransformFactory : ITransformFactory
{
    public bool Validate(TransformValidationContext context,
        IReadOnlyDictionary<string, string> transformValues)
    {
        if (transformValues.TryGetValue("CustomTransform", out var value))
        {
            if (string.IsNullOrEmpty(value))
            {
                context.Errors.Add(new ArgumentException(
                    "A non-empty CustomTransform value is required"));
            }

            return true; // Matched
        }
        return false;
    }

    public bool Build(TransformBuilderContext context,
        IReadOnlyDictionary<string, string> transformValues)
    {
        if (transformValues.TryGetValue("CustomTransform", out var value))
        {
            if (string.IsNullOrEmpty(value))
            {
                throw new ArgumentException(
                    "A non-empty CustomTransform value is required");
            }

            context.AddRequestTransform(transformContext =>
            {
                transformContext.ProxyRequest.Headers.Add("CustomHeader", value);
                return default;
            });

            return true; // Matched
        }

        return false;
    }
}
```

如果`Validate`和`Build`已將指定的轉換組態標識為他們擁有的組態，則返回`true`。`ITransformFactory`可以實作多個轉換。任何 `ITransformFactory`未處理的任何`RouteConfig.Transforms`項目都將被視為組態錯誤並阻止套用組態。

還可以考慮在`RouteConfig`上加入參數化擴充方法，例如`WithTransformQueryValue`，以促進可程式化路由建構。

```csharp
public static RouteConfig WithTransformQueryValue(this RouteConfig routeConfig, string queryKey, string value, bool append = true)
{
    var type = append ? QueryTransformFactory.AppendKey : QueryTransformFactory.SetKey;
    return routeConfig.WithTransform(transform =>
    {
        transform[QueryTransformFactory.QueryValueParameterKey] = queryKey;
        transform[type] = value;
    });
}
```

##  14. <a name='-1'></a>目的地健康檢查

在大多數現實世界的系統中，由於過載、資源洩漏、硬件故障等多種原因，預計它們的節點偶爾會遇到暫時性問題並完全停機。理想情況下，最好是 以主動的方式完全防止這些不幸事件的發生，但設計和建構這樣一個理想系統的成本通常高得令人望而卻步。但是，還有另一種更便宜的反應式（reactive）方法，旨在最大限度地減少故障對用戶端請求造成的負面影響。Proxy可以分析每個節點的健康狀況並停止向不健康的節點發送用戶端流量，直到它們恢復。YARP以主動（active）和被動（passive）目的地健康檢查的形式實施這種方法。它們彼此獨立，並存儲在每個目的地的相關屬性中。健康狀態用`Unknown`值初始化，稍後可以通過相應的策略將其更改為`Healty`或`Unhealthy`，細節如下所述。

###  14.1. <a name='-1'></a>主動健康檢查

YARP可以通過向指定的健康端點發送定期探測請求並分析回應來主動監控目標健康狀況。該分析由為Cluster指定的活動健康檢查策略執行，並導致計算新的目標健康狀態。最後，策略根據HTTP回應狀態碼將每個目的地標記為健康或不健康（HTTP 2xx 被認為是健康的），並重建Cluster集群的健康目的地集合。

有幾個Cluster範圍的組態設定可以控制活動的健康檢查，可以在組態檔案或程式碼中設定。

####  14.1.1. <a name='-1'></a>組態檔案範例

```json
"Clusters": {
      "cluster1": {
        "HealthCheck": {
          "Active": {
            "Enabled": "true",
            "Interval": "00:00:10",
            "Timeout": "00:00:10",
            "Policy": "ConsecutiveFailures",
            "Path": "/api/health"
          }
        },
        "Metadata": {
          "ConsecutiveFailuresHealthPolicy.Threshold": "3"
        },
        "Destinations": {
          "cluster1/destination1": {
            "Address": "https://localhost:10000/"
          },
          "cluster1/destination2": {
            "Address": "http://localhost:10010/",
            "Health": "http://localhost:10020/"
          }
        }
      }
```

####  14.1.2. <a name='-1'></a>程式碼範例

```csharp
var clusters = new[]
{
    new ClusterConfig()
    {
        ClusterId = "cluster1",
        HealthCheck = new HealthCheckConfig
        {
            Active = new ActiveHealthCheckConfig
            {
                Enabled = true,
                Interval = TimeSpan.FromSeconds(10),
                Timeout = TimeSpan.FromSeconds(10),
                Policy = HealthCheckConstants.ActivePolicy.ConsecutiveFailures,
                Path = "/api/health"
            }
        },
        Metadata = new Dictionary<string, string> { { ConsecutiveFailuresHealthPolicyOptions.ThresholdMetadataName, "5" } },
        Destinations =
        {
            { "destination1", new DestinationConfig() { Address = "https://localhost:10000" } },
            { "destination2", new DestinationConfig() { Address = "https://localhost:10010", Health = "https://localhost:10010" } }
        }
    }
};
```

####  14.1.3. <a name='-1'></a>組態

除一項活動健康檢查設定外，所有活動健康檢查設定都在`Cluster/HealthCheck/Active`這部分的Cluster層級上指定。唯一的例外是一個選擇性的`Destination/Health`元素，它指定了一個單獨的活動健康檢查端點。實際的健康探測 URI 建構為`Destination/Address`（或設定時的`Destination/Health`）加上`Cluster/HealthCheck/Active/Path`的組合。

主動健康檢查設定也可以通過`Yarp.ReverseProxy.Configuration`命名空間中鏡像組態合約的對應型別在程式碼中定義。

`Cluster/HealthCheck/Active`部分和`ActiveHealthCheckConfig`：

* `Enabled` - 指示是否為Cluster啟用主動健康檢查的標誌。預設`false`。
* `Interval` - 發送健康探測請求的時間。預設`00:00:15`。
* `Timeout` - 探測請求超時。預設`00:00:10`。
* `Policy` - 評估目標的活動健康狀態的策略名稱。此為必須參數。
* `Path` - 所有Cluster目的地的健康檢查路徑。預設為`null`。

`Destination`部分和`DestinationConfig`：

* `Health` - 專用的健康探測端點，例如`http://destination:12345/`。預設為`null`並返回到`Destination/Address`。

####  14.1.4. <a name='-1'></a>內建策略

目前有一個內建的主動健康檢查策略：`ConsecutiveFailuresHealthPolicy`。它計算連續的健康探測失敗，並在達到指定臨界值後將目標標記為不健康。在第一次成功回應時，目的地被標記為健康並且計數器被重置。策略參數在Cluster的中繼資料（metadata）中設n定如下：

* `ConsecutiveFailuresHealthPolicy.Threshold` - 將目標標記為不健康所需的連續失敗的活動健康探測請求數。預設`2`。

####  14.1.5. <a name='-1'></a>設計

此過程中的主要服務是`IActiveHealthCheckMonitor`，它通過`IProbingRequestFactory`定期建立探測請求，將它們發送到啟用了主動健康檢查的每個`ClusterConfig`的所有`DestinationConfig`，然後將所有回應傳遞給為Cluster指定的`IActiveHealthCheckPolicy`。

`IActiveHealthCheckMonitor`不會對目標是否健康做出實際決定，而是將此職責委派給為Cluster指定的`IActiveHealthCheckPolicy`。一旦完成對所有Cluster目的地的所有探測，就會呼叫一個策略來評估新的健康狀態。它接受一個表示Cluster動態狀態的`ClusterState`和一組存儲Cluster目的地探測結果的`DestinationProbingResult`。為每個目的地評估新的健康狀態後，策略呼叫`IdestinationHealthUpdater`來實際更新`DestinationHealthState.Active`值。

```plaintext
-{For each cluster's destination}-
IActiveHealthCheckMonitor <--(Create probing request)--> IProbingRequestFactory
        |
        V
 HttpMessageInvoker <--(Send probe and receive response)--> Destination
        |
(Save probing result)
        |
        V
DestinationProbingResult
--------------{END}---------------
        |
(Evaluate new destination active health states using probing results)
        |
        V
IActiveHealthCheckPolicy --(New active health states)--> IDestinationHealthUpdater --(Update each destination's)--> DestinationState.Health.Active
```

所有上述元件都有預設的內建實作，必要時也可以用自定義的實作替換。

####  14.1.6. <a name='-1'></a>可擴充性

主動健康檢查子系統中有2個主要的可擴充點。

##### IActiveHealthCheckPolicy

`IActiveHealthCheckPolicy`分析目標如何回應`IActiveHealthCheckMonitor`發送的活動健康探測，評估所有探測目標的新活動健康狀態，然後呼叫`IDestinationHealthUpdater.SetActive`設定新的活動健康狀態並根據更新的值重建健康目標集合。

以下是自定義`IActiveHealthCheckPolicy`將目標標記為健康的簡單示例，如果為探測返回成功的回應狀態碼，否則標記為不健康。

```csharp
public class FirstUnsuccessfulResponseHealthPolicy : IActiveHealthCheckPolicy
{
    private readonly IDestinationHealthUpdater _healthUpdater;

    public FirstUnsuccessfulResponseHealthPolicy(IDestinationHealthUpdater healthUpdater)
    {
        _healthUpdater = healthUpdater;
    }

    public string Name => "FirstUnsuccessfulResponse";

    public void ProbingCompleted(ClusterState cluster, IReadOnlyList<DestinationProbingResult> probingResults)
    {
        if (probingResults.Count == 0)
        {
            return;
        }

        var newHealthStates = new NewActiveDestinationHealth[probingResults.Count];
        for (var i = 0; i < probingResults.Count; i++)
        {
            var response = probingResults[i].Response;
            // 成功狀態碼則健康
            var newHealth = response != null && response.IsSuccessStatusCode ? DestinationHealth.Healthy : DestinationHealth.Unhealthy;
            newHealthStates[i] = new NewActiveDestinationHealth(probingResults[i].Destination, newHealth);
        }

        _healthUpdater.SetActive(cluster, newHealthStates);
    }
}
```

##### IProbingRequestFactory

`IProbingRequestFactory`建立要發送到目標健康端點的活動健康探測請求。它可以考慮`ActiveHealthCheckOptions.Path`、`DestinationConfig.Health`和其他組態設定來建構探測請求。

預設的`IProbingRequestFactory`使用與Proxy請求相同的`HttpRequest`組態，以自定義實現您自己的`IProbingRequestFactory`並將其註冊到DI容器中，如下所示。

```csharp
services.AddSingleton<IProbingRequestFactory, CustomProbingRequestFactory>();
```

以下是用戶`IProbingRequestFactory`連接`DestinationConfig.Address`和固定運行狀況探測路徑以建立探測請求URI的簡單示例。

```csharp
public class CustomProbingRequestFactory : IProbingRequestFactory
{
    public HttpRequestMessage CreateRequest(ClusterConfig clusterConfig, DestinationConfig destinationConfig)
    {
        var probeUri = new Uri(destinationConfig.Address + "/api/probe-health");
        return new HttpRequestMessage(HttpMethod.Get, probeUri) { Version = ProtocolHelper.Http11Version };
    }
}
```

###  14.2. <a name='-1'></a>被動健康檢查

YARP可以被動地觀察用戶端請求Proxy中的成功和失敗，以反應性地評估目標健康狀態。對Proxy請求的回應被一個專用的被動健康檢查中間層（middleware）攔截，該中間層將它們傳遞給Cluster上組態的策略。該策略分析回應以評估產生它們的目的地是否健康。然後，它計算並將新的被動健康狀態分配給各個目的地，並重建Cluster的健康目的地集合。

請注意，回應通常在被動健康策略運行之前發送到用戶端，因此策略不能攔截回應主體，也不能修改回應標頭中的任何內容，除非Proxy應用程式引入了完整的回應緩衝（buffering）。

與主動健康檢查邏輯有一個重要區別。一旦一個目的地被分配了一個不健康的被動狀態，它就會停止接收所有新的流量，這會封鎖未來的健康重新評估。該策略還會在組態的時間段後安排目標重新啟動。重新啟動意味著將被動健康狀態從`Unhealthy`重置回初始 `Unknown`值，這使得目的地再次符合流量條件。

有幾個Cluster範圍的配置設置可以控制被動運行狀況檢查，可以在組態檔案或程式碼進行設定。

####  14.2.1. <a name='-1'></a>組態檔案範例

```json
"Clusters": {
      "cluster1": {
        "HealthCheck": {
          "Passive": {
            "Enabled": "true",
            "Policy": "TransportFailureRate",
            "ReactivationPeriod": "00:02:00"
          }
        },
        "Metadata": {
          "TransportFailureRateHealthPolicy.RateLimit": "0.5"
        },
        "Destinations": {
          "cluster1/destination1": {
            "Address": "https://localhost:10000/"
          },
          "cluster1/destination2": {
            "Address": "http://localhost:10010/"
          }
        }
      }
```

####  14.2.2. <a name='-1'></a>程式碼範例

```csharp
var clusters = new[]
{
    new ClusterConfig()
    {
        ClusterId = "cluster1",
        HealthCheck = new HealthCheckConfig
        {
            Passive = new PassiveHealthCheckConfig
            {
                Enabled = true,
                Policy = HealthCheckConstants.PassivePolicy.TransportFailureRate,
                ReactivationPeriod = TimeSpan.FromMinutes(2)
            }
        },
        Metadata = new Dictionary<string, string> { { TransportFailureRateHealthPolicyOptions.FailureRateLimitMetadataName, "0.5" } },
        Destinations =
        {
            { "destination1", new DestinationConfig() { Address = "https://localhost:10000" } },
            { "destination2", new DestinationConfig() { Address = "https://localhost:10010" } }
        }
    }
};
```

####  14.2.3. <a name='-1'></a>組態

被動健康檢查設置在`Cluster/HealthCheck/Passive`部分的Cluster層級上指定。或者，它們可以通過`Yarp.ReverseProxy.Configuration`命名空間中反映組態合約的相應類型在程式碼中定義。

被動健康檢查需要將`PassiveHealthCheckMiddleware`加入到管道中才能工作。預設`MapReverseProxy(this IEndpointRouteBuilder endpoints)`方法會自動執行此操作，但在手動建構管道的情況下，必須呼叫`UsePassiveHealthChecks` 方法來否入該Middleware，如下例所示：

```csharp
endpoints.MapReverseProxy(proxyPipeline =>
{
    proxyPipeline.UseAffinitizedDestinationLookup();
    proxyPipeline.UseProxyLoadBalancing();
    proxyPipeline.UseRequestAffinitizer();
    proxyPipeline.UsePassiveHealthChecks();
});
```

`Cluster/HealthCheck/Passive`部分和`PassiveHealthCheckConfig`：

* `Enabled` - 指示是否為Cluster啟用被動健康狀況檢查的標誌。默認`false`。
* `Policy` - 評估目的地被動健康狀態的策略名稱。必須參數。
* `ReactivationPeriod` - 不健康目標的被動健康狀態重置為`Unknown`並再次開始接收流量的時間區間。預設值為`null`，這意味著周期將由`IPassiveHealthCheckPolicy`設定。

####  14.2.4. <a name='-1'></a>內建策略

當前有一個內置的被動健康檢查策略 - `TransportFailureRateHealthPolicy`。它計算每個目標的Proxy請求失敗率，如果超過指定的限制，則將其標記為不健康。速率計算為在指定時間區間內失敗的請求佔Proxy到目的地的請求總數的百分比。在滑動時間視窗（a sliding time window）中追蹤失敗和總計數器，這意味著僅考慮適合視窗中的最近讀取數。全域和每個Cluster層級定義了兩組策略參數。

全域參數通過選項機制使用`TransportFailureRateHealthPolicyOptions`類型設定，擁有以下屬性：

* `DetectionWindowSize` - 檢測到的故障被保留並在速率計算中考慮在內的時間區間。預設值為`00:01:00`。
* `MinimalTotalCountThreshold` - 在此策略開始評估目標的健康狀況並實施故障率限制之前，必須在檢測視窗內Proxy到目標的最小請求總數。預設值為`10`。
* `DefaultFailureRateLimit` - 如果未在Cluster的中繼資料上設定，則應用將目標標記為不健康的默認故障率限制。該值在`(0,1)` 範圍內。預設值為`0.3`（即`30%`）。

全域策略選項可以在程式碼中設定如下：

```csharp
services.Configure<TransportFailureRateHealthPolicyOptions>(o =>
{
    o.DetectionWindowSize = TimeSpan.FromSeconds(30);
    o.MinimalTotalCountThreshold = 5;
    o.DefaultFailureRateLimit = 0.5;
});
```

Cluster特定的參數在Cluster的中繼資料中設定如下：

* `TransportFailureRateHealthPolicy.RateLimit` - 將目標標記為不正常的故障率限制。該值在`(0,1)`範圍內。預設值由全域`DefaultFailureRateLimit`參數提供。

####  14.2.5. <a name='-1'></a>設計

主要元件是`PassiveHealthCheckMiddleware`，它位於請求管道中並分析目的地返回的回應。對於來自啟用了被動健康檢查的Cluster的目標的每個回應，`PassiveHealthCheckMiddleware`呼叫為Cluster指定的`IPassiveHealthCheckPolicy`。該策略分析指定的回應，評估新目的地的被動健康狀態並呼叫`IDestinationHealthUpdater`以實際更新`DestinationHealthState.Passive`值。更新發生在後端非同步，不會阻塞請求管道。當目標被標記為不健康時，它會停止接收新請求，直到在組態的時間區間後重新啟動。重新啟動意味著目標的`DestinationHealthState.Passive`狀態從`Unhealthy`重置為`Unknown`，並且重新建構Cluster的健康目標清單以包含它。在將目標的`DestinationHealthState.Passive`設定為`Unhealthy`後，`IDestinationHealthUpdater`會立即安排重新啟動。

```plaintext
(Response to a proxied request)
              |
  PassiveHealthCheckMiddleware
              |
              V
  IPassiveHealthCheckPolicy
              |
(Evaluate new passive health state)
              |
  IDestinationHealthUpdater --(Asynchronously update passive state)--> DestinationState.Health.Passive
              |
              V
  (Schedule a reactivation) --(Set to Unknown)--> DestinationState.Health.Passive
```

####  14.2.6. <a name='-1'></a>可擴充性

被動健康檢查子系統中有一個主要的可擴展點，即`IPassiveHealthCheckPolicy`。

##### IPassiveHealthCheckPolicy

`IPassiveHealthCheckPolicy`分析目標如何回應Proxy用戶端請求，評估其新的被動健康狀態，最後呼叫`IDestinationHealthUpdater.SetPassiveAsync`來建立非同步任務，實際更新被動健康狀態並重建健康目標集合。

以下是自定義`IPassiveHealthCheckPolicy`在對Proxy請求的第一次不成功回應時將目標標記為不健康的簡單範例。

```csharp
public class FirstUnsuccessfulResponseHealthPolicy : IPassiveHealthCheckPolicy
{
    private static readonly TimeSpan _defaultReactivationPeriod = TimeSpan.FromSeconds(60);
    private readonly IDestinationHealthUpdater _healthUpdater;

    public FirstUnsuccessfulResponseHealthPolicy(IDestinationHealthUpdater healthUpdater)
    {
        _healthUpdater = healthUpdater;
    }

    public string Name => "FirstUnsuccessfulResponse";

    public void RequestProxied(ClusterState cluster, DestinationState destination, HttpContext context)
    {
        var error = context.Features.Get<IProxyErrorFeature>();
        if (error != null)
        {
            var reactivationPeriod = cluster.Model.Config.HealthCheck.Passive.ReactivationPeriod ?? _defaultReactivationPeriod;
            _ = _healthUpdater.SetPassiveAsync(cluster, destination, DestinationHealth.Unhealthy, reactivationPeriod);
        }
    }
}
```

###  14.3. <a name='Availabledestinationcollection'></a>Available destination collection

目的地健康狀態用於確定它們中的哪些有資格接收Proxy請求。每個Cluster在`ClusterDestinationState`類型的`AvailableDestinations`屬性上維護自己可用目的地清單。當任何目的地的健康狀況發生變化時，該清單將被重新建立。`IClusterDestinationsUpdater`控制該過程並呼叫Cluster上組態的`IAvailableDestinationsPolicy`以實際從所有Cluster的目的地中選擇可用目的地。提供了以下內建策略，如有必要，可以實施自定義策略。

* `HealthyAndUnknown` - 如果以下所有語句都為`TRUE`，則檢查每個`DestinationState`並將其加入到可用目標清單中。如果沒有可用的目的地，則請求將收到HTTP 503錯誤。這是預設策略。
    * 在Cluster上禁用主動健康檢查或`DestinationHealthState.Active != DestinationHealth.Unhealthy`。
    * 在Cluster上禁用被動健康檢查或`DestinationHealthState.Passive != DestinationHealth.Unhealthy`。
* `HealthyOrPanic` - 首先呼叫`HealthyAndUnknown`策略來獲取可用的目的地。如果此呼叫沒有傳回它們，則它將所有Cluster的目的地標記為可用。

注意：無論是否在指定Cluster上啟用了任何健康狀況檢查，都將始終呼叫在Cluster上組態的可用目標策略。禁用的健康狀況檢查的健康狀況設置為`Unknown`。

####  14.3.1. <a name='-1'></a>組態

##### 檔案範例

```json
"Clusters": {
      "cluster1": {
        "AvailableDestinationsPolicy": "HealthyOrPanic",
        "HealthCheck": {
          "Passive": {
            "Enabled": "true"
          }
        },
        "Destinations": {
          "cluster1/destination1": {
            "Address": "https://localhost:10000/"
          },
          "cluster1/destination2": {
            "Address": "http://localhost:10010/"
          }
        }
      }
```

##### 程式碼範例

```csharp
var clusters = new[]
{
    new ClusterConfig()
    {
        ClusterId = "cluster1",
        HealthCheck = new HealthCheckConfig
        {
            AvailableDestinationsPolicy = HealthCheckConstants.AvailableDestinations.HealthyOrPanic,
            Passive = new PassiveHealthCheckConfig
            {
                Enabled = true
            }
        },
        Destinations =
        {
            { "destination1", new DestinationConfig() { Address = "https://localhost:10000" } },
            { "destination2", new DestinationConfig() { Address = "https://localhost:10010" } }
        }
    }
};
```

##  15. <a name='-1'></a>分散式追蹤

作為ASP.NET Core元件，YARP可以像任何其他Web應用程式一樣輕鬆整合到不同的追蹤系統中。

* <a href="https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core" target="_blank">Application Insights</a>
* <a href="https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/docs/trace/getting-started/README.md" target="_blank">OpenTelemetry</a>

.NET 6.0內健了對分散式追蹤的可組態支持，YARP利用這些支持立即可用地啟用此類場景。

###  15.1. <a name='-1'></a>使用自定義追蹤標頭

當使用非內建於.NET中的傳播機制（例如<a href="https://github.com/openzipkin/b3-propagation" target="_blank">b3-propagation</a>）時，您應該為該方案實作自定義`DistributedContextPropagator`。

YARP將刪除`DistributedContextPropagator.Fields`中的任何標頭，以便傳播者可以在`Inject`呼叫期間將它們重新加入到請求中。

###  15.2. <a name='Proxy-1'></a>傳遞Proxy

如果您不希望Proxy主動參與追蹤，並且希望保持所有追蹤標頭不變，可以通過將`SocketsHttpHandler.ActivityHeadersPropagator`設定為`null`來實現。

```csharp
services.AddReverseProxy()
    .ConfigureHttpClient((context, handler) => handler.ActivityHeadersPropagator = null);
```

###  15.3. <a name='NET5.0'></a>.NET 5.0之前

在.NET 6.0之前，`SocketsHttpHandler`不能與分散式追蹤一起使用。在.NET 3.1或5.0上運行時，YARP將按原樣複製追蹤標頭，而不考慮應用程式中追蹤可能發生的任何更改。

要讓YARP積極參與，您必須使用解決方法手動插入正確的標頭。

推薦的解決方法是：

* 在您的專案中包含一個自定義`IForwarderHttpClientFactory`
* 在DI容器中註冊

```csharp
#if !NET6_0_OR_GREATER
services.AddSingleton<IForwarderHttpClientFactory, DiagnosticsHandlerFactory>();
#endif
```

該變通方法模仿`HttpClient`使用的內部`DiagnosticsHandler`之類的行為。因此，它會自動與來自OpenTelemetry或Application Insights的檢測打包在一起使用。
