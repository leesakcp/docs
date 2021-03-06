---

copyright:
  years: 2015, 2016
lastupdated: "2016-10-02"
---
{:screen: .screen}
{:shortdesc: .shortdesc}

# Cordova アプリ用の Google 認証の使用可能化
{: #google-auth-cordova}


{{site.data.keyword.amafull}} Cordova アプリケーションを Google 認証統合用に構成するには、Cordova アプリケーションのネイティブ・コード (Java、Objective-C、または Swift) で変更を行う必要があります。各プラットフォームは別々に構成する必要があります。ネイティブ・コードの変更は、Android Studio や Xcode などのネイティブ開発環境で行ってください。

## 開始する前に
{: #before-you-begin}
以下が必要です。
* {{site.data.keyword.amashort}} Client SDK が装備された Cordova プロジェクト。詳しくは、[Cordova プラグインのセットアップ](https://console.{DomainName}/docs/services/mobileaccess/getting-started-cordova.html)を参照してください。  
* {{site.data.keyword.amashort}} サービスによって保護された {{site.data.keyword.Bluemix_notm}} アプリケーションのインスタンス。{{site.data.keyword.Bluemix_notm}} バックエンド・アプリケーションの作成方法について詳しくは、[概説](index.html)を参照してください。
* (オプション) 次のセクションの内容をよく理解してください。
   * [Android アプリ用の Google 認証の使用可能化](https://console.{DomainName}/docs/services/mobileaccess/google-auth-android.html)
   * [iOS アプリ用の Google 認証の使用可能化](https://console.{DomainName}/docs/services/mobileaccess/google-auth-ios.html)


## Android プラットフォームの構成
{: #google-auth-cordova-android}

Cordova アプリケーションの Android プラットフォームを Google 認証統合用に構成するために必要なステップは、ネイティブ・アプリケーションに必要なステップに非常に似ています。[Android アプリでの Google 認証の使用可能化](https://console.{DomainName}/docs/services/mobileaccess/google-auth-android.html)を参照して、以下をセットアップします。

* Android プラットフォーム用の Google プロジェクトの構成
* Google 認証用の {{site.data.keyword.amashort}} の構成

### Android Cordova 用の {{site.data.keyword.amashort}} Client SDK の構成


2. Android プロジェクト・フォルダーで、アプリケーション・モジュールの `build.gradle` ファイルを開きます (プロジェクト `build.gradle` ファイル**ではありません**)。以下のように、依存関係セクションを見つけ、Client SDK の新しいコンパイル依存関係を追加します。

	```Gradle
	dependencies {
		compile group: 'com.ibm.mobilefirstplatform.clientsdk.android',    
        name:'googleauthentication',
        version: '1.+',
        ext: 'aar',
        transitive: true
    	// other dependencies  
	}
	```

2. **「ツール」>「Android」>「プロジェクトを Gradle ファイルと同期 (Sync Project with Gradle Files)」**をクリックして Gradle とプロジェクトを同期します。

3. Cordova アプリケーションの場合、Java コードではなく JavaScript コードで {{site.data.keyword.amashort}} Client SDK を初期化する必要があります。ネイティブ・コードでの `GoogleAuthenticationManager` API の登録はまだ必要になります。次のコードをメイン・アクティビティー `onCreate` メソッドに追加します。 

	```Java
	GoogleAuthenticationManager.getInstance().registerDefaultAuthenticationListener(this);
	```

1. 以下のコードをアクティビティーに追加します。
 
 	```Java
	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		GoogleAuthenticationManager.getInstance()
			.onActivityResultCalled(requestCode, resultCode, data);
	}
```

## iOS プラットフォームの構成
{: #google-auth-cordova-ios}

Cordova アプリケーションの iOS プラットフォームを Google 認証統合用に構成するために必要なステップは、ネイティブ・アプリケーション用のステップに似ています。主な違いは、現在 Cordova CLI が CocoaPods の依存関係マネージャーをサポートしていない点です。Google 認証との統合に必要なファイルは、手動で追加する必要があります。詳細情報については、[iOS アプリでの Google 認証の使用可能化](https://console.{DomainName}/docs/services/mobileaccess/google-auth-ios.html)を参照してください。以下のステップを完了してください。

* iOS プラットフォーム用の Google プロジェクトの構成
* Google 認証用の {{site.data.keyword.amashort}} の構成

### Google 認証用の {{site.data.keyword.amashort}} SDK および Google SDK の手動インストール
{: #google-auth-cordova-ios-sdk}
1. [{{site.data.keyword.Bluemix}} Mobile Services SDK for iOS](https://hub.jazz.net/git/bluemixmobilesdk/imf-ios-sdk/archive?revstr=master) が含まれたアーカイブをダウンロードします。

1. `Sources/Authenticators/IMFGoogleAuthentication` ディレクトリーに移動し、すべてのファイルを Xcode で iOS プロジェクトにコピー (ドラッグ・アンド・ドロップ) します。コピーする必要があるファイルは以下のとおりです。

	* IMFDefaultGoogleAuthenticationDelegate.h
	* IMFDefaultGoogleAuthenticationDelegate.m
	* IMFGoogleAuthenticationDelegate.h
	* IMFGoogleAuthenticationHandler.h
	* IMFGoogleAuthenticationHandler.m

	**「Copy files....(ファイルのコピー)」**チェック・ボックスを選択します。

1. [Google+ iOS SDK](http://goo.gl/9cTqyZ) をダウンロードしインストールします。

1. [Start integrating Google+ into your iOS app](https://developers.google.com/+/mobile/ios/getting-started) チュートリアルのステップ 2 に従って、Google+ iOS SDK を Xcode プロジェクトに統合します。

[Google 認証用の iOS プラットフォームの構成](https://console.{DomainName}/docs/services/mobileaccess/google-auth-ios.html)の **Google 認証用の iOS プロジェクトの構成**セクションに進みます。`{{site.data.keyword.amashort}} Client SDK の初期化`セクションの説明に従って、ネイティブ・コードで `IMFGoogleAuthenticationHandler` を登録します。ネイティブ・コードで `IMFClient` を初期化しないでください (クライアントは次のセクションで JavaScript で初期化されます)。

アプリケーション代行の `application:openURL:sourceApplication:annotation` メソッドに以下の行を追加します。これにより、すべての Cordova プラグインに各イベントが確実に通知されます。

```
[[ NSNotificationCenter defaultCenter] postNotification:
		[NSNotification notificationWithName:CDVPluginHandleOpenURLNotification object:url]];
```
{: codeblock}

## {{site.data.keyword.amashort}} Client SDK の初期化
{: #google-auth-cordova-initialize}

Cordova アプリケーションで以下の JavaScript コードを使用して、{{site.data.keyword.amashort}} Client SDK を初期化します。

```JavaScript
BMSClient.initialize("applicationRoute", "applicationGUID");
```
{: codeblock}

`applicationRoute` 値および `applicationGUID` 値を、アプリケーションの **「経路」**値および**「AppGuid」**値に置き換えます。これらの値は、ダッシュボードのアプリケーション・ページで**「モバイル・オプション」**ボタンをクリックすると見つけることができます。
	



##{{site.data.keyword.amashort}} AuthorizationManager の初期化
Cordova アプリケーションで以下の JavaScript コードを使用して、{{site.data.keyword.amashort}} AuthorizationManager を初期化します。

```JavaScript
  MFPAuthorizationManager.initialize("tenantId");
  ```
{: codeblock}

`tenantId` 値を、{{site.data.keyword.amashort}} サービスの `tenantId` に置き換えます。この値は、{{site.data.keyword.amashort}} サービス・タイルの**「資格情報の表示」**ボタンをクリックすると、見つけることができます。





## 認証のテスト
{: #google-auth-cordova-test}
Client SDK が初期化されたら、モバイル・バックエンド・アプリケーションに要求を出すことができるようになります。

### 開始する前に
{: #google-auth-cordova-testing-before}
`/protected` エンドポイントに {{site.data.keyword.amashort}} によって保護されたバックエンド・アプリケーションがなければなりません。`/protected` エンドポイントをセットアップする必要がある場合、[リソースの保護 ](https://console.{DomainName}/docs/services/mobileaccess/protecting-resources.html)を参照してください。


1. デスクトップ・ブラウザーで、`{applicationRoute}/protected` (例えば、`http://my-mobile-backend.mybluemix.net/protected`) を開くことによって、モバイル・バックエンド・アプリケーションの保護エンドポイントへの要求の送信を試行します。

1. MobileFirst Services ボイラープレートを使用して作成されたモバイル・バックエンド・アプリケーションの `/protected` エンドポイントは、{{site.data.keyword.amashort}} によって保護されています。したがって、このエンドポイントにアクセスできるのは、{{site.data.keyword.amashort}} Client SDK が装備されたモバイル・アプリケーションのみです。結果的に、デスクトップ・ブラウザーに `Unauthorized` が表示されます。

1. Cordova アプリケーションを使用して、同じエンドポイントに要求を実行します。`BMSClient` を初期化した後に、以下のコードを追加してください。

	```JavaScript
	var success = function(data){
    	console.log("success", data);
    }
	var failure = function(error)
    	{console.log("failure", error);
    }
	var request = new MFPRequest("/protected", MFPRequest.GET);
	request.send(success, failure);
	```
{: codeblock}

1. アプリケーションを実行します。Google のログイン画面が表示されます。

	![Google のログイン画面](images/android-google-login.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;	![Google のログイン画面](images/ios-google-login.png)
	
	この画面は、デバイスに Facebook アプリをインストールしていない場合、または現在 Facebook に ログインしていない場合は少し違って見えるかもしれません。 1. **「OK」**をクリックして、{{site.data.keyword.amashort}} が Google ユーザー ID を認証目的に使用することを許可します。

1. ユーザーの要求は正常に処理されます。使用しているプラットフォームに応じて、LogCat/Xcode コンソールに以下の出力が表示されます。

	![Android のコード・スニペット](images/android-google-login-success.png)

	![iOS のコード・スニペット](images/ios-google-login-success.png)
