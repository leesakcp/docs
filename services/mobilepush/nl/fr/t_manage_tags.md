---

copyright:
 years: 2015, 2016

---

# Gestion des balises
{: #manage_tags}
Dernière mise à jour : 17 octobre 2016
{: .last-updated}

Utilisez le tableau de bord {{site.data.keyword.mobilepushshort}} afin de créer et de supprimer des balises pour votre application puis d'initier des notifications basées sur des balises. Ces notifications basées sur des balises sont reçues sur les appareils abonnés à ces balises.


## Création de balises
{: #create_tags}

Les notifications basées sur les balises sont des messages qui sont ciblés vers tous les appareils abonnés à une balise
particulière. Chaque appareil peut s'abonner à un nombre illimité de balises. Quand une balise est supprimée, les informations qui lui sont associées, y compris ses abonnés et appareils, sont supprimées. Un désabonnement automatique n'est pas nécessaire car cette balise n'existe plus. Aucune autre action supplémentaire n'est nécessaire côté client.

1. Dans le tableau de bord {{site.data.keyword.mobilepushshort}}, sélectionnez l'onglet relatif aux balises.
1. Cliquez sur le bouton + **Créer une balise**.   
   1. Dans la zone **Nom**, entrez le nom de la balise. Exemple : "coupons".
   1. Dans la zone **Description**, entrez la description de la balise.
   1. Cliquez sur **Sauvegarder**.

1. Dans la zone **Fragments de code**, sélectionnez la plateforme pour votre application mobile.
1. Modifiez les fragments de code pour traiter les erreurs, puis copiez-les pour chaque balise dans votre application mobile.

## Suppression de balises
{: #delete_tags}

1. Dans l'onglet **Balise**, sélectionnez la balise que vous voulez supprimer puis cliquez sur l'icône **Supprimer**.
1. Cliquez sur **OK**.

## Edition d'une description de balise
{: #edit_tags}

1. Dans l'onglet **Balise**, sélectionnez la balise à éditer.
1. Cliquez sur l'icône **Editer**.
1. Editez la description de la balise, puis cliquez sur le bouton **Sauvegarder**.

# Obtention des balises
{: #get_tags}

Les balises permettent d'envoyer des notifications ciblées aux utilisateurs en fonction de leurs intérêts, à la différence des diffusions
générales qui sont envoyées à toutes les applications. Vous pouvez créer et gérer des balises à l'aide de l'onglet Balise du tableau de bord {{site.data.keyword.mobilepushshort}} ou utiliser l'API REST. Vous pouvez utiliser des fragments de code pour gérer et interroger vos abonnements aux balises pour votre application mobile. Ils permettent d'obtenir des abonnements, de s'abonner à une balise, de se désabonner d'une balise ou d'obtenir la liste des balises disponibles. Copiez ces fragments de code dans votre application mobile.

## Android
{: android-get-tags}

L'API **getTags** renvoie la liste des balises disponibles auxquelles l'appareil peut s'abonner. Une fois qu'un
appareil est abonné à une balise en particulier, il peut recevoir toutes les notifications de type {{site.data.keyword.mobilepushshort}} envoyées pour cette balise.

Copiez les fragments de code ci-après dans votre application mobile Android afin d'obtenir la liste des balises auxquelles l'appareil est abonné ainsi que la liste des balises disponibles.

Utilisez l'API **getTags** pour obtenir la liste des balises disponibles auxquelles l'appareil peut s'abonner.

```
// Get a list of available tags to which the device can subscribe
push.getTags(new MFPPushResponseListener<List<String>>(){  
   @Override
   public void onSuccess(List<String> tags){
   updateTextView("Retrieved available tags: " + tags);  
   System.out.println("Available tags are: "+tags);
   availableTags = tags;   
   subscribeToTag();   
  }    
  @Override    
  public void onFailure(MFPPushException ex){
     updateTextView("Error getting available tags.. " + ex.getMessage());
  }
   })  
```
	{: codeblock}

Utilisez l'API **getSubscriptions** pour obtenir la liste des balises auxquelles l'appareil est abonné.

```
// Get a list of tags that to which the device is subscribed.
push.getSubscriptions(new MFPPushResponseListener<List<String>>() {
  @Override
    public void onSuccess(List<String> tags) {
  updateTextView("Retrieved subscriptions : " + tags);
    System.out.println("Subscribed tags are: "+tags);
    subscribedTags = tags;
    subscribeToTag();
    }
  @Override
    public void onFailure(MFPPushException ex) {
       updateTextView("Error getting subscriptions.. " + ex.getMessage());
  }
})
	```
	{: codeblock}

## Cordova
{: cordova-get-tags}

Copiez les fragments de code ci-après dans votre application mobile afin d'obtenir la liste des balises auxquelles l'appareil est abonné ainsi que la liste des balises disponibles.

Renvoyez un tableau des balises auxquelles l'appareil peut s'abonner.

```
//Get a list of available tags to which the device can subscribe
MFPPush.retrieveAvailableTags(function(tags) {
  alert(tags);
}, null);
```
	{: codeblock}

```
//Get a list of available tags to which the device is subscribed.
MFPPush.getSubscriptionStatus(function(tags) {
  alert(tags);
}, null);
```
	{: codeblock}

## Objective-C
{: objc-get-tags}

Copiez les fragments de code ci-après dans votre application iOS développée avec Objective-C afin d'obtenir la liste des balises auxquelles l'appareil est abonné ainsi que la liste des balises disponibles auxquelles l'appareil peut s'abonner.

Utilisez l'API **retrieveAvailableTags** ci-après pour obtenir la liste des balises disponibles auxquelles l'appareil peut s'abonner.

```
//Get a list of available tags to which the device can subscribe
[push retrieveAvailableTagsWithCompletionHandler:
^(IMFResponse *response, NSError *error){
 if(error){    
   [self updateMessage:error.description];  
 } else {
   [self updateMessage:@"Successfully retrieved available tags."];
 NSDictionary *availableTags = [[NSDictionary alloc]init];
 availableTags = [response tags];
[self.appDelegateVC updateMessage:availableTags.description];
}
   }];
 ```
	{: codeblock}

Utilisez l'API **retrieveSubscriptions** pour obtenir la liste des balises auxquelles l'appareil est abonné.


```
// Get a list of tags that to which the device is subscribed.
[push retrieveSubscriptionsWithCompletionHandler:
^(IMFResponse *response, NSError *error) {
  if(error){
     [self updateMessage:error.description];
   } else {
   [self updateMessage:@"Successfully retrieved subscriptions."];
 NSDictionary *subscribedTags = [[NSDictionary alloc]init];
subscribedTags = [response subscriptions];
[self.appDelegateVC updateMessage:subscribedTags.description];
}
  }];
  ```
	{: codeblock}

## Swift
{: swift-get-tags}

L'API **retrieveAvailableTagsWithCompletionHandler** renvoie la liste des balises disponibles auxquelles l'appareil peut s'abonner. Une fois qu'un
appareil est abonné à une balise en particulier, il peut recevoir toutes les notifications de type {{site.data.keyword.mobilepushshort}} envoyées pour cette balise.

Appelez le service {{site.data.keyword.mobilepushshort}} pour obtenir les abonnements à une balise.

Copiez les fragments de code ci-après dans votre application mobile Swift afin d'obtenir la liste des balises auxquelles l'appareil est abonné ainsi que la liste des balises disponibles auxquelles l'appareil peut s'abonner.
```
//Get a list of available tags to which the device can subscribe
	push.retrieveAvailableTagsWithCompletionHandler({ (response, statusCode, error) -> Void in
    if error.isEmpty 
		{
     print( "Response during retrieve tags : \(response)")
        print( "status code during retrieve tags : \(statusCode)")
    }
    else
	{
    print( "Error during retrieve tags \(error) ")
    Print( "Error during retrieve tags \n  - status code: \(statusCode) \n Error :\(error) \n")
    	}
	}
```
		{: codeblock}

```
//Get a list of available tags to which the device is subscribed
push.retrieveSubscriptionsWithCompletionHandler { (response, statusCode, error) -> Void in
    if error.isEmpty {
    print( "Response during retrieving subscribed tags : \(response?.description)")
        print( "status code during retrieving subscribed tags : \(statusCode)")
    }
    else
	{
    print( "Error during retrieving subscribed tags \(error) ")
    Print( "Error during retrieving subscribed tags \n  - status code: \(statusCode) \n Error :\(error) \n")
        }
	}
```
	{: codeblock}

## Google Chrome et Mozilla Firefox
{: web-get-tags}

Pour obtenir la liste des étiquettes (balises) auxquelles les utilisateurs peuvent s'abonner, utilisez le code suivant.

```
var bmsPush = new BMSPush();
  bmsPush.retrieveAvailableTags(function(response) 
	{
  alert(response.response)
    var json = JSON.parse(response.response);
    var tagsA = []
  for (i in json.tags)
	{
    tagsA.push(json.tags[i].name)
    }
   alert(tagsA)
 })
```
	{: codeblock}

Copiez les fragments de code ci-après dans vos applications et extensions Google Chrome pour obtenir une liste des étiquettes (balises) auxquelles les utilisateurs se sont abonnés.

```
var bmsPush = new BMSPush();
  bmsPush.retrieveSubscriptions(function(response) 
	{
   alert(response.response)
 })
```
	{: codeblock}

## Applications et extensions Google Chrome
{: web-get-tags}

Pour obtenir la liste des étiquettes (balises) auxquelles les utilisateurs peuvent s'abonner, utilisez le code suivant.

```
var bmsPush = new BMSPush();
  bmsPush.retrieveAvailableTags(function(response) 
	{
  alert(response.response)
    var json = JSON.parse(response.response);
    var tagsA = []
  for (i in json.tags)
	{
    tagsA.push(json.tags[i].name)
    }
   alert(tagsA)
 })
```
	{: codeblock}

Copiez les fragments de code ci-après dans vos applications et extensions Google Chrome pour obtenir une liste des étiquettes (balises) auxquelles les utilisateurs se sont abonnés.

```
var bmsPush = new BMSPush();
  bmsPush.retrieveSubscriptions(function(response) 
	{
   alert(response.response)
 })
```
	{: codeblock}


# Abonnement à des balises et désabonnement
{: #Subscribe_tags}

Utilisez les fragments de code ci-après pour permettre à vos appareils de s'abonner à une balise et de s'en désabonner.

## Android
{: android-subscribe-tags}

Copiez et collez le fragment de code suivant dans votre application mobile Android.

```
push.subscribe(allTags.get(0),
new MFPPushResponseListener<String>() {
  @Override
    public void onFailure(MFPPushException ex) {
  updateTextView("Error subscribing to Tag1.."
           + ex.getMessage());
  }
  @Override
  public void onSuccess(String arg0) {
  updateTextView("Succesfully Subscribed to: "+ arg0);
   unsubscribeFromTags(arg0);
   }
  });
```
	{: codeblock}

```
push.unsubscribe(tag, new MFPPushResponseListener<String>() {
 @Override
 public void onSuccess(String s) {
 updateTextView("Unsubscribing from tag");
   updateTextView("Successfully unsubscribed from tag . "+ tag);
 }
 @Override
 public void onFailure(MFPPushException e) {
 updateTextView("Error while unsubscribing from tags. "+ e.getMessage());
 }
 });
```
	{: codeblock}

## Cordova
{: cordova-subscribe-tags}

Copiez et collez le fragment de code suivant dans votre application mobile Cordova.

```
var tag = "YourTag";
MFPPush.subscribe(tag, success, failure);
MFPPush.unsubscribe(tag, success, failure);
```
	{: codeblock}

## Objective-C
{: objc-subscribe-tags}

Copiez et collez le fragment de code suivant dans votre application mobile Objective-C.

Utilisez l'API **subscribeToTags** pour vous abonner à une balise.

```
[push subscribeToTags:tags completionHandler:
^(IMFResponse *response, NSError *error) {
  if(error){
     [self updateMessage:error.description];
  }else{
      NSDictionary* subStatus = [[NSDictionary alloc]init];
      subStatus = [response subscribeStatus];
      [self updateMessage:@"Parsed subscribe status is:"];
      [self updateMessage:subStatus.description];
  }
  }];
```
	{: codeblock}

Utilisez l'API **unsubscribeFromTags** pour vous désabonner d'une balise.

```
[push unsubscribeFromTags:tags completionHandler:
^(IMFResponse *response, NSError *error) {
  if (error){
       [self updateMessage:error.description];
 } else {
     NSDictionary* subStatus = [[NSDictionary alloc]init];
       subStatus = [response unsubscribeStatus];
       [self updateMessage:subStatus.description];
  }
  }];
```
	{: codeblock}

## Swift
{: swift-subscribe-tags}

Copiez et collez le fragment de code suivant dans votre application mobile Swift.

**Abonnement à des balises disponibles**

Utilisez l'API **subscribeToTags** pour vous abonner à une balise.

```
push.subscribeToTags(tagsArray: tags) { (response: IMFResponse!, error: NSError!) -> Void in
	if (error != nil) {
	//error while subscribing to tags
	} else {
//successfully subscribed to tags var subStatus = response.subscribeStatus();
	}
}
```
	{: codeblock}

**Désabonnement de balises**

Utilisez l'API **unsubscribeFromTags** pour vous désabonner d'une balise.

```
push.unsubscribeFromTags(response, completionHandler: { (response, statusCode, error) -> Void in
    if error.isEmpty {
     print( "Response during unsubscribed tags : \(response?.description)")
        print( "status code during unsubscribed tags : \(statusCode)")
    }
  else {
    print( "Error during unsubscribed tags \(error) ")
    print( "Error during unsubscribed tags \n  - status code: \(statusCode) \n Error :\(error) \n")
  }
}
```
	{: codeblock}

## Google Chrome et Mozilla Firefox
{: web-subscribe-tags}

Pour vous abonner à des balises depuis des applications Web, utilisez le fragment de code suivant :

```
var tagsArray = ["tag1", "Tag2"]
bmsPush.subscribe(tagsArray,function(response) {
  alert(response.response)
})
```
	{: codeblock}

Le désabonnement à des balises utilise la méthode `unSubscribe`.

```
var tagsArray = ["tag1", "Tag2"]
 bmsPush.unSubscribe(tagsArray,function(response) {
 alert(response.response)
})
```
	{: codeblock}

# Utilisation de notifications basées sur les balises
{: #using_tags}

Les messages de notifications basées sur les balises sont envoyés à tous les appareils abonnés à une balise particulière. Chaque appareil peut s'abonner à un nombre illimité de balises. Cette rubrique explique comment envoyer des des notifications basées sur des balises. Les abonnements sont gérés par l'instance de service Bluemix {{site.data.keyword.mobilepushshort}}. Quand une balise est supprimée, toutes les informations qui lui sont associées, y compris ses abonnés et appareils, sont supprimées. Aucun désabonnement automatique n'est requis pour cette balise car elle n'existe plus et aucune action supplémentaire n'est requise depuis le côté client.

###Avant de commencer
{: before-you-begin}

Créez des balises sur l'écran **Tag**. Pour plus d'informations sur la création de balises,
voir [Création de balises](t_manage_tags.html).

1. Dans le tableau de bord des notifications push, cliquez sur **Envoyer des notifications**.
1. Sélectionnez l'option **Appareil par étiquette** dans la liste déroulante **Envoyer à**.
1. Recherchez les étiquettes (balises) que vous voulez utiliser et sélectionnez-les.
![Ecran des notifications](images/tag_notification.jpg)
1. Dans la zone **Message Text**, entrez le texte qui sera envoyé en tant que notification aux destinataires abonnés.
1. Cliquez sur **Envoyer**.
