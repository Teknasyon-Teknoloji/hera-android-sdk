# Hera


Hera strives to simplify the process of integrating and remotely configuring 3rd party ads libraries in your project, here a list of the features it provides:

  

## Features


- ✅ Remotely changing the ad unit id associated with a specific action.

- 🚀 Changing the ads provider on the fly.

- ⏰ Control the ads showing intervals remotly.

- ⚙️ Showing ads based on a specific action.

  

The following document offers a quick guide on how to use this SDK.
  

## Installation

Hera and Admost repositories should be added in project level build.gradle file

```groovy
    maven { url 'https://raw.githubusercontent.com/Teknasyon-Teknoloji/hera-android-sdk/master/' }
    maven { url 'http://repo.admost.com:8081/artifactory/admost' }
```

Hera dependency should be added in app level build.gradle file
   
```groovy 
    implementation 'hera:hera:1.1.2'
```


Starting with Android 9.0 (API level 28), cleartext support is disabled by default. For applications targetSdkVersion higher than 27 must use the following manifest config for AdMost cleartext support.

Create network security config xml and add it to your application
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config>
        <domain-config cleartextTrafficPermitted="true">
            <domain includeSubdomains="true">admost.com</domain>
        </domain-config>
    </network-security-config>

    <application
      ...
        android:networkSecurityConfig="@xml/network_security_config">

     </application>
```

or you can just add this line to your application
```xml
    <application
      ...
        android:usesCleartextTraffic="true">

     </application>
```
You must add your Admob App Id and Facebook App Id to your manifest  
```xml
    <meta-data
        android:name="com.facebook.sdk.ApplicationId"
        android:value="@string/facebook_app_id" />
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="@string/admob_app_id" />
```
## Usage

### Initialization

Hera needs 2 variables for init.

Parameter|Value
---------|-----
apiKey | String
debuggable | Boolean flag to print logs

```kotlin
    Hera.init(
      apiKey = "MEDIATON_MANAGER_KEY",
      debuggable = false
    )
```

You need to collect advertise attributions first. The advertise attribution is a Map<String,String> type

Here is an example of how it look like

```kotlin

mapOf("adjust" to "{\"trackerToken\":\"xxx\",\"adid\":\"xxxxx\",\"trackerName\":\"Organic\",\"network\":\"Organic\"}")

```

  
#### HeraUserProperties

After preparing `AddvertisementAttributions` we can initialize `HeraUserProperties` like follows

```kotlin

val atrributions = mapOf("adjust" to "{\"trackerToken\":\"xxx\",\"adid\":\"xxxxx\",\"trackerName\":\"Organic\",\"network\":\"Organic\"}")

  
Hera.setUserProperties(
    activity = this,
    heraUserProperties = HeraUserProperties(
        device_id = "1234567890123456",
        language = "tr",
        country = "TR",
        advertise_attributions = advertisingAttributions,
        extra_data = null
    )
)

```

### Load Ads

You can easily load your ads from Hera with action key. 

```kotlin
    Hera.loadAd(activity : Activity, action : String)
```

Usage example
```kotlin
    Hera.loadAd(
      activity = requireActivity(),
      action = "inAppTransition"
    )
```
 

### Show Ads

You can show your ads after load.

```kotlin
    Hera.showAd(
      activity = this,
      action = "inAppTransition"
    ) {
      TODO("lambda function called after an ad shown or ad failed to shown")
      openNextActivity()
    }
```

### Events

Event|Type|Description
-----|----|-----------
HERA_READY|-|It fires After fetch config successful.
AD_LOADED|String|
AD_SHOWED|String|
AD_CLOSED|String|
AD_CLICKED|String|
AD_FAILED|String|

You can use Ares EventBus system with Mediation 

```kotlin
    Hera.subscribe(Consumer {
        when (it.type) {
            MediationEvent.AD_CLOSED.value -> {
                openNextActivity()
            }
            MediationEvent.AD_FAILED.value -> {
                showToast()
            }
            MediationEvent.HERA_READY.value -> {
                lifecycleScope.launch(Dispatchers.Main) {
                    Hera.loadAd(this@SplashActivity, "action")
                }
            }
        }
    })
```

## Updating User Status

`Hera.updateUserProperties(activity,userProperties)`



> Calling this method will fetch the configurations again and re-initialize `Hera`.

## Ad Network Integration


Hera supports Google Admob and Facebook Ads out of the box if you would like to integrate other ad networks please add corresponding adapter dependencies in your gradle file.

It is possible to integrate a third party network by adding corresponding network adapter in app module dependencies.
```groovy
    implementation "hera:hera-applovin:10.2.0.0"
    implementation "hera:hera-chartboost:8.2.0.3"
    implementation "hera:hera-ironsource:7.1.5.0"
    implementation "hera:hera-unityads:3.7.1.0"
    implementation "hera:hera-vungle:6.9.1.2"
    implementation "hera:hera-tiktok:3.6.0.0"
```
### Applovin Integration

Besides adding required adapter, Applovin also requires api key. 
```xml
    <manifest>
      ...
      <application
      ...
      >
        ...
        <meta-data
            android:name="applovin.sdk.key"
            android:value="APPLOVIN SDK KEY" />
        ...
      </application>
    <manifest>
```