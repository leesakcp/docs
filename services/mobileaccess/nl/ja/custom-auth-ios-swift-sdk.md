---

copyright:
  years: 2016
lastupdated: "2016-10-09"
---

# {{site.data.keyword.amashort}} iOS (Swift SDK) アプリ用のカスタム認証の構成
{: #custom-ios}

{{site.data.keyword.amafull}} Client SDK の使用および {{site.data.keyword.Bluemix}} へのアプリケーションの接続のためにカスタム認証を使用する iOS アプリケーションを構成します。新しくリリースされた {{site.data.keyword.amashort}} Swift SDK は、既存の Mobile Client Access Objective-C SDK によって提供される機能を増強します。

**注:** Objective-C SDK は現在も完全にサポートされており、{{site.data.keyword.Bluemix_notm}} モバイル・サービス用の主要 SDK とされていますが、今年後半には廃止され、この新しい Swift SDK が後継になる予定です。

## 開始する前に
{: #before-you-begin}

カスタム ID プロバイダーを使用するように構成済みの{{site.data.keyword.amashort}} サービスのインスタンスにより保護されているリソースを持っている必要があります。また、モバイル・アプリに {{site.data.keyword.amashort}} Client SDK が装備されている必要があります。詳しくは、以下の情報を参照してください。

 * [{{site.data.keyword.amashort}} 概説](https://console.{DomainName}/docs/services/mobileaccess/index.html)
 * [iOS Swift SDK のセットアップ](https://console.{DomainName}/docs/services/mobileaccess/getting-started-ios-swift-sdk.html)
 * [カスタム ID プロバイダーの使用](https://console.{DomainName}/docs/services/mobileaccess/custom-auth.html)
 * [カスタム ID プロバイダーの作成](https://console.{DomainName}/docs/services/mobileaccess/custom-auth-identity-provider.html)
 * [カスタム認証用の {{site.data.keyword.amashort}} の構成 ](https://console.{DomainName}/docs/services/mobileaccess/custom-auth-config-mca.html)


## カスタム認証用の {{site.data.keyword.amashort}} の構成
 {: #custom-auth-ios-configmca}

 1. サービス・ダッシュボードを開きます。
 
 1. **「モバイル・オプション」**をクリックし、**「経路」** (*applicationRoute*) と **「アプリ GUID」/「TenantId」** (*serviceTenantID*) のメモを取ります。SDK を初期化する際、および要求をバックエンド・アプリケーションに送信する際に、これらの値が必要になります。

 1. {{site.data.keyword.amashort}} タイルをクリックします。{{site.data.keyword.amashort}} ダッシュボードがロードされます。

 1. **「カスタム」**タイルをクリックします。

 1. **「レルム名」**で、カスタム認証レルムを指定します。

 1. **「URL」**で、applicationRoute を指定します。

 1. **「保存」**をクリックします。




### Client SDK の初期化
{: #custom-ios-sdk-initialize}

`applicationGUID` (tenantId) パラメーターを渡して、SDK を初期化します。初期化コードを入れる場所は一般的に (必須ではありませんが)、アプリケーション代行の `application:didFinishLaunchingWithOptions` メソッドの中です。

1. {{site.data.keyword.amashort}} Client SDK を使用したいクラス内で必要なフレームワークをインポートします。

	```Swift
	import UIKit
	import BMSCore
	import BMSSecurity
	```

1. {{site.data.keyword.amashort}} Client SDK を初期化し、許可マネージャーを `MCAAuthorizationManager` に変更し、認証代行を定義して登録します。

```Swift

	let tenantId = "<serviceTenantID>"
	let regionName = <applicationBluemixRegion>
	let customRealm = "<yourProtectedRealm>"

	func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: 
		[UIApplicationLaunchOptionsKey: Any]?) -> Bool {

 let mcaAuthManager = MCAAuthorizationManager.sharedInstance
	    		mcaAuthManager.initialize(tenantId: tenantId, bluemixRegion: regionName)
	 // regionName は以下の BMSClient.Region 定数のいずれか: BMSClient.Region.usSouth、BMSClient.Region.unitedKingdom、BMSClient.Region.sydney
		BMSClient.sharedInstance.authorizationManager = mcaAuthManager

	//Auth delegate for handling custom challenge
 class MyAuthDelegate : AuthenticationDelegate {
      func onAuthenticationChallengeReceived(_ authContext: AuthenticationContext, challenge: AnyObject){
		    print("onAuthenticationChallengeReceived")
		    let answer = try? Utils.parseJsonStringtoDictionary( "{\"userName\":\"" + "test" + "\",\"password\":\"" + "test" + "\"}")
			authContext.submitAuthenticationChallengeAnswer(answer)
		}

		func onAuthenticationSuccess(_ info: AnyObject?) {
		    print("onAuthenticationSuccess info = \(info)")
           print("onAuthenticationSuccess")
      }

      func onAuthenticationFailure(_ info: AnyObject?){
		     print("onAuthenticationFailure info = \(info)")
        print("onAuthenticationFailure")
      }
  }

	     let delegate = MyAuthDelegate()
	     mcaAuthManager.registerAuthenticationDelegate(delegate, realm: customRealm)

	     return true
 }
 ```

コードの中で次のようにします。

* `<applicationBluemixRegion>` を、{{site.data.keyword.Bluemix_notm}} アプリケーションがホストされている地域に置き換えます。{{site.data.keyword.Bluemix_notm}} 地域を表示するには、メニュー・バーにある「アバター」アイコン ![「アバター」アイコン](images/face.jpg "「アバター」アイコン") をクリックして、**「アカウントとサポート」**ウィジェットを開きます。表示される地域の値は、**「米国南部」**、**「英国」**、または**「シドニー」**のいずれかでなければならず、またコードで必要な定数 (`BMSClient.Region.usSouth`、`BMSClient.Region.unitedKingdom`、または `BMSClient.Region.sydney`) に対応している必要があります。
* `"<yourProtectedRealm>"` を、{{site.data.keyword.amashort}} ダッシュボードの**「カスタム」**タイルで定義した**「レルム名」**値に置き換えます。 
* `"<serviceTenantID>"` を、**「モバイル・オプション」**から取得した **tenantId** 値に置き換えます。『[カスタム認証用の Mobile Client Access の構成](#custom-auth-ios-configmca)』を参照してください。

### Client SDK の初期化
{: #custom-ios-sdk-initialize}
   
  
## 認証のテスト
{: #custom-ios-testing}

Client SDK を初期化し、カスタム認証代行を登録した後、モバイル・バックエンド・アプリケーションに要求を出すことを開始できます。

### 開始する前に
{: #custom-ios-testing-before}

 {{site.data.keyword.mobilefirstbp}} ボイラープレートを使用して作成されたアプリケーションと、 `/protected` エンドポイントで{{site.data.keyword.amashort}} により保護されているリソースを持っている必要があります。

1. ブラウザーで、`{applicationRoute}/protected` を開いて、モバイル・バックエンド・アプリケーションの保護エンドポイントに要求を送信します。`{applicationRoute}` を、**「モバイル・オプション」**で取得した値 (『[カスタム認証用の Mobile Client Access の構成](#custom-auth-ios-configmca)』を参照) に置き換えます。例: `http://my-mobile-backend.mybluemix.net/protected`。

 {{site.data.keyword.mobilefirstbp}} ボイラープレートを使用して作成されたモバイル・バックエンド・アプリケーションの `/protected` エンドポイントは、{{site.data.keyword.amashort}} で保護されています。このエンドポイントは {{site.data.keyword.amashort}} Client SDK により装備されたモバイル・アプリケーションからのみアクセス可能です。その結果、`承認されていない`というメッセージがブラウザーに表示されます。

1. iOS アプリケーションを使用して、同じエンドポイントへ要求を出します。`BMSClient` を初期化し、カスタム認証代行を登録した後に、以下のコードを追加します。

 

	```Swift

	let protectedResourceURL = "<your protected resource absolute path>"
	let request = Request(url: protectedResourceURL, method: HttpMethod.GET)

	let callBack:BMSCompletionHandler = {(response: Response?, error: Error?) in
  if error == nil {
      print ("response:\(response?.responseText), no error")
  } else {
      print ("error: \(error)")
  }
  }

	request.send(completionHandler: callBack)
	 ```

1. 要求が成功したら、Xcode コンソールに次のような出力が表示されます。

	 ```
	 onAuthenticationSuccess info = Optional({
     attributes =     {
     };
     deviceId = don;
     displayName = "Don+Lon";
     isUserAuthenticated = 1;
     userId = don;
 })
 response:Optional("Hello Don Lon"), no error
 ```

1. 次のコードを追加してログアウト機能を追加することもできます。

	 ```
	 MCAAuthorizationManager.sharedInstance.logout(callBack)
 ```  

 ユーザーのログイン後に、このコードを呼び出すと、そのユーザーはログアウトされます。そのユーザーが再度ログインしようとする場合は、サーバーから受信した要求に再度応じる必要があります。

 ログアウト機能へ `callBack` を渡すことは、オプションです。`nil` を渡すこともできます。
