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
    jcenter()
    maven { url 'https://raw.githubusercontent.com/Teknasyon-Teknoloji/hera-android-sdk/master/' }
    maven { url 'http://repo.admost.com:8081/artifactory/amr' }
```

Hera dependency should be added in app level build.gradle file
   
```groovy 
    implementation 'hera:hera:2.0.1'
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

Hera.init(apiKey,debuggable) method should be called on Application class. Hera needs 2 variables for init.

Parameter|Value
---------|-----
apiKey | String
debuggable | Boolean flag to print logs

```kotlin
class App: Application(){

    override fun onCreate(){
        Hera.init(
            application = this,
            apiKey = "MEDIATON_MANAGER_KEY",
            debuggable = false
        )
    }

}    
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
    heraUserProperties = HeraUserProperties(
        deviceId = "1234567890123456",
        language = "tr",
        country = "TR",
        advertiseAttributions = advertisingAttributions,
        extraData = null
    )
)

```

Language changes can be passed in to Hera by Hera.updateUserProperties(language) method.

```kotlin

Hera.updateUserProperties(language = "en")

```

### Load Ads

You can easily load your ads from Hera with action key. 

```kotlin
    
    Hera.loadAd(activity: Activity, adType: AdType, action: String)

```

Usage example
```kotlin

// Interstitial ad usage inside activity
    Hera.loadAd(
      activity = this,
      adType = AdType.INTERSTITIAL,
      action = "MainScreen"
    )

// Interstitial ad usage inside fragment
    Hera.loadAd(
      fragment = this,
      adType = AdType.INTERSTITIAL,
      action = "MainScreen"
    )

```
 

### Show Ads

You can show your ads after load.

```kotlin

// Banner ad usage inside activity
    Hera.showAd(
      activity = this,
      adType = AdType.BANNER,
      action = "MainScreen",
      view = viewGroupForBanner
    )

// Banner ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adType = AdType.BANNER,
      action = "MainScreen",
      view = viewGroupForBanner
    )

// Interstitial ad usage inside activity
    Hera.showAd(
      activity = this,
      adType = AdType.INTERSTITIAL,
      action = "MainScreen"
    ) {
      TODO("lambda function called after an ad shown or ad failed to shown")
      openNextActivity()
    }

// Interstitial ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adType = AdType.INTERSTITIAL,
      action = "MainScreen"
    ) {
      TODO("lambda function called after an ad shown or ad failed to shown")
      openNextActivity()
    }

```

#### Global Ads
Global ads feature is working autimatically when setting up in Hera control panel. It does not require any method call from application layer. 

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
    implementation "hera:hera-applovin:10.2.0.1"
    implementation "hera:hera-chartboost:8.2.1.0"
    implementation "hera:hera-ironsource:7.1.5.0"
    implementation "hera:hera-unityads:3.7.1.0"
    implementation "hera:hera-vungle:6.9.1.3"
    implementation "hera:hera-tiktok:3.8.0.0"
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

## Requirements
Min Android SDK Level 19+
Target Android SDK Level 30
Kotlin 1.4+