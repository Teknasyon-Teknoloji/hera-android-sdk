# Hera


Hera strives to simplify the process of integrating and remotely configuring 3rd party ads libraries in your project, here a list of the features it provides:

  

## Features


- ✅ Remotely changing the ad unit id associated with a specific action.

- 🚀 Changing the ads provider on the fly.

- ⏰ Control the ads showing intervals remotly.

- ⚙️ Showing ads based on a specific action.

  

The following document offers a quick guide on how to use this SDK.
  

## Installation

Hera, Admost, IronSource repositories should be added in project level build.gradle file

```groovy
    maven { url 'https://maven.teknasyon.com' }
    maven { 
        allowInsecureProtocol true
        url 'http://repo.admost.com:8081/artifactory/amr' 
    }
    maven { url 'https://android-sdk.is.com/' }
```

Hera dependency should be added in app level build.gradle file
   
```groovy 
    implementation 'hera:hera:2.4.3'
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
      adConfig = AdConfig.InterstitialAd(
        action = "MainScreen"
      )
    )

// Interstitial ad usage inside fragment
    Hera.loadAd(
      fragment = this,
      adConfig = AdConfig.InterstitialAd(
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
      adConfig = AdConfig.InterstitialAd(
        action = "MainScreen"
      )
    )

// Interstitial ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adConfig = AdConfig.InterstitialAd(
        action = "MainScreen"
      )
    )

// Rewarded ad usage inside activity
    Hera.showAd(
      activity = this,
      adConfig = AdConfig.RewardedAd(
        action = "MainScreen"
      )
    )

// Rewarded ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adConfig = AdConfig.RewardedAd(
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
      adConfig = AdConfig.NativeAd(
        action = "MainScreen"
      ),
      view = viewGroupForBanner
      binder = HeraBinder(hera, facebook, google, tiktok)
    )

// Native ad usage inside fragment
    Hera.showAd(
      fragment = this,
      adConfig = AdConfig.NativeAd(
        action = "MainScreen"
      ),
      binder = HeraBinder(hera, facebook, google, tiktok)
    )

```

### Events
Hera uses protocol oriented approach for events. A class that implements `AdListener` interface can start to fetch events by calling `addHeraObserver()`and can stop to fetch events by calling `removeHeraObserver()`. All members of AdListener have default implementation, so methods only desired to be used can be overriden.

When AdListener is used on an anonymous object and reference of it not kept anywhere, it can be removed before actual `removeHeraObserver()` called becuase of garbage collection. 

```kotlin
interface AdListener : ESPListener {
    @OnLifecycleEvent(Lifecycle.Event.ON_CREATE)
    fun addHeraObserver() {
        AdNotifier.addListener(this)
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_DESTROY)
    fun removeHeraObserver() {
        AdNotifier.removeListener(this)
    }

    /**
     * Fired when Hera SDK ready to use. It is fired after Hera::setUserProperties or
     * Hera::updateUserProperties
     *
     * @see hera.Hera.setUserProperties
     * @see hera.Hera.updateUserProperties
     */
    fun onHeraReady() = Unit

    /**
     * Called when an ad loaded successfully
     * @param config loaded ad config
     */
    fun onAdLoaded(config: AdConfig) = Unit

    /**
     *  Called when an ad shown
     *  @param config shown ad config
     */
    fun onAdShown(config: AdConfig) = Unit

    /**
     * Called when an interstitial ad closed or rewarded ad closed before user earn reward
     * @param config closed ad config
     */
    fun onAdClosed(config: AdConfig) = Unit

    /**
     * Called when an ad failed to load
     * @param config failed ad config
     * @param message detail about fail
     */
    fun onAdFailed(config: AdConfig, message: String?) = Unit

    /**
     * Called when an ad clicked by user
     * @param config clicked ad config
     */
    fun onAdClicked(config: AdConfig) = Unit

    /**
     * Called when user earned reward from a rewarded ad
     * @param config completed rewarded ad config
     */
    fun onAdCompleted(config: AdConfig.RewardedAd) = Unit
}

```


## Ad Network Integration


Hera supports Google Admob and Facebook Ads out of the box if you would like to integrate other ad networks please add corresponding adapter dependencies in your gradle file.

It is possible to integrate a third party network by adding corresponding repository in project gradle file or settings gradle file and network adapter in app module dependencies.

```groovy
allprojects {
    repositories {
        // criteo
        maven { url 'https://pubsdk-bin.criteo.com/publishersdk/android' }

        // ogury
        maven { url 'https://maven.ogury.co' }

        // verve
        maven { url 'https://verve.jfrog.io/artifactory/verve-gradle-release' }

        // tiktok
        maven { url 'https://artifact.bytedance.com/repository/pangle' }
    }
}
```
```groovy
    implementation "hera:hera-adcolony:4.6.5.2"
    implementation "hera:hera-applovin:11.1.2.2"
    implementation "hera:hera-chartboost:8.3.1.1"
    implementation "hera:hera-criteo:4.4.0.5"
    implementation "hera:hera-inmobi:10.0.3.1"
    implementation "hera:hera-ironsource:7.1.14.1"
    implementation "hera:hera-ogury:5.1.0.2"
    implementation "hera:hera-unityads:4.0.0.1"
    implementation "hera:hera-vungle:6.10.4.2"
    implementation "hera:hera-tiktok:4.2.5.4"
    implementation "hera:hera-verve:2.11.1.1"
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
