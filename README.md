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
    maven { url 'https://maven.teknasyon.com' }
    maven { url 'http://repo.admost.com:8081/artifactory/amr' }
    maven { url 'https://pubsdk-bin.criteo.com/publishersdk/android' }
    maven { url 'https://maven.ogury.co' }
```

Hera dependency should be added in app level build.gradle file
   
```groovy 
    implementation 'hera:hera:2.2.2'
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
    
    Hera.loadAd(activity: Activity, adConfig: AdConfig)

```

Usage example
```kotlin

// Interstitial ad usage inside activity
    Hera.loadAd(
      activity = this,
      adConfig = AdType.INTERSTITIAL,
      action = "MainScreen"
    )

// Interstitial ad usage inside fragment
    Hera.loadAd(
      fragment = this,
      adType = AdConfig.InterstitialAd(
        action = "MainScreen"
      )
    )

```
 

### Show Ads

You can show your ads after load.

```kotlin

// Banner ad usage inside activity
    Hera.showAd(
      activity = this,
      adConfig = AdConfig.BannerAd(
        action = "MainScreen"
      ),
      view = viewGroupForBanner
    )

// Banner ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adConfig = AdConfig.BannerAd(
        action = "MainScreen"
      ),
      view = viewGroupForBanner
    )

// Interstitial ad usage inside activity
    Hera.showAd(
      activity = this,
      adConfig = AdType.InterstitialAd(
        action = "MainScreen"
      )
    )

// Interstitial ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adConfig = AdType.InterstitialAd(
        action = "MainScreen"
      )
    )

// Rewarded ad usage inside activity
    Hera.showAd(
      activity = this,
      adConfig = AdType.RewardedAd(
        action = "MainScreen"
      )
    )

// Rewarded ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adConfig = AdType.RewardedAd(
        action = "MainScreen"
      )
    )
    
// Native ad binder objects
    val hera = HeraNativeBannerBinder.Builder(R.layout.custom_native_layout)
        .titleId(R.id.ad_headline)
        .textId(R.id.ad_body)
        .mainImageId(R.id.ad_image)
        .iconImageId(R.id.ad_app_icon)
        .callToActionId(R.id.ad_call_to_action)
        .privacyIconId(R.id.ad_privacy_icon)
        .sponsoredTextId(R.id.ad_attribution)
        .build()

    val facebook = HeraFacebookBinder.Builder(R.layout.facbook_native_layout)
        .titleId(R.id.native_title)
        .textId(R.id.native_text)
        .mediaViewId(R.id.native_media_view)
        .adIconViewId(R.id.native_icon)
        .callToActionId(R.id.native_cta)
        .adChoicesRelativeLayoutId(R.id.native_privacy_information_icon_layout)
        .build()

    val google = HeraGooglePlayBinder.Builder(R.layout.admob_video_ad_list_item)
        .titleId(R.id.native_title)
        .textId(R.id.native_text)
        .mediaLayoutId(R.id.native_media_layout)
        .iconImageId(R.id.native_icon_image)
        .callToActionId(R.id.native_cta)
        .privacyInformationIconImageId(R.id.native_privacy_information_icon_image)
        .build()

    val tiktok = HeraTiktokBinder.Builder(R.layout.tiktok_native_layout)
        .titleId(R.id.native_title)
        .textId(R.id.native_text)
        .mediaViewId(R.id.native_media_layout)
        .adIconViewId(R.id.native_icon_image)
        .callToActionId(R.id.native_cta)
        .build()

// Native ad usage inside activity
    Hera.showAd(
      activity = this,
      adConfig = AdType.NativeAd(
        action = "MainScreen"
      ),
      view = viewGroupForBanner
      binder = HeraBinder(hera, facebook, google, tiktok)
    )

// Native ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adConfig = AdType.NativeAd(
        action = "MainScreen"
      ),
      binder = HeraBinder(hera, facebook, google, tiktok)
    )

```

### Events

Event|Type|Description
-----|----|-----------
HERA_READY|-|It's fired After fetch config successful.
AD_LOADED|String|
AD_SHOWED|String|
AD_CLOSED|String|
AD_CLICKED|String|
AD_FAILED|String|
AD_COMPLETED|String|It's fired when user earn reward from rewarded ads. To open premium content, AD_CLOSED event should be waited

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
    implementation "hera:hera-adcolony:4.6.3.1"
    implementation "hera:hera-applovin:10.3.3.1"
    implementation "hera:hera-chartboost:8.2.1.1"
    implementation "hera:hera-criteo:4.4.0.1"
    implementation "hera:hera-inmobi:9.1.9.1"
    implementation "hera:hera-ironsource:7.1.10.1"
    implementation "hera:hera-ogury:5.0.10.1"
    implementation "hera:hera-unityads:3.7.2.2"
    implementation "hera:hera-vungle:6.10.2.1"
    implementation "hera:hera-tiktok:3.9.0.1"
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
