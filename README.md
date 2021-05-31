Mediation is a feature that lets you serve ads to your apps from multiple sources, including third-party ad networks etc

Hera is a mediation management library. It provides usage for Admost Mediation and Mopub Mediation systems. 

## Implementation

First of all you need this implementation on app gradle

    implementation 'hera:hera:1.1.1'

And you must add Hera and Admost repositories to your project gradle

    maven { url 'https://raw.githubusercontent.com/Teknasyon-Teknoloji/hera-android-sdk/master/' }
    maven { url 'http://repo.admost.com:8081/artifactory/admost' }

Starting with Android 9.0 (API level 28), cleartext support is disabled by default. For applications targetSdkVersion higher than 27 must use the following manifest config for AdMost cleartext support.

Create network security config xml and add it to your application

    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config>
        <domain-config cleartextTrafficPermitted="true">
            <domain includeSubdomains="true">admost.com</domain>
        </domain-config>
    </network-security-config>

    <application
      ...
        android:networkSecurityConfig="@xml/network_security_config"

     </application>


or you can just add this line to your application

    <application
      ...
        android:usesCleartextTraffic="true"

     </application>

You must add your Admob App Id and Facebook App Id to your manifest  

    <meta-data
        android:name="com.facebook.sdk.ApplicationId"
        android:value="@string/facebook_app_id" />
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="@string/admob_app_id" />
    <meta-data

## Initialization

Hera needs 2 variables for init.

Parameter|Value
---------|-----
apiKey | String
debuggable | Boolean flag to print logs

    Hera.init(
      apiKey = "MEDIATON_MANAGER_KEY",
      debuggable = false
    )

After that Hera needs User properties for the get correct mediation info. 

Please wait attribution info before use to Hera.setUserProperties() method.

Ad intervals and first ad times come from Hera service and Hera manages it.

    Hera.setUserProperties(
      activity = this,
      heraUserProperties : HeraUserProperties
    )

-Landing Count 

We need paywall(landing) opening count for our extra data info. You can implement this methodology on your side.

You can listen to PAYWALL_OPENED event and increase your landing count in your onCreate method of application class. Also you can disable showing ads over the paywall by using Hera.canShow(booleanI) method. Because loading and showing an interstitial ad is not synchronize, there is chance to display an interstitial ad over paywall.

    EventBus.subscribe(Consumer {
        when (it.type) {
            DeepwallEvent.PAYWALL_OPENED.value -> {
                Log.d("landingCount", cacheManager.read("landingCount", 0).toString())
                cacheManager.write("landingCount", cacheManager.read("landingCount", 0) + 1)
                Hera.canShowAd(false)
            }
            DeepwallEvent.CLOSED.value, DeepwallEvent.CANCELED.value -> {
                Hera.canShowAd(true)
            }
        }
    })

HeraUserProperties

Parameter|Value
---------|-----
device_id | String
language | String
country | String
advertise_attributions | Map<String,String>
extraData | HashMap<String,Any>

    val extraData = mapOf(
      "landing_loading_count" to cacheManager.read("landingCount", 0),
      "is_premium" to cacheManager.read("isPro", false)
    )

    Hera.setUserProperties(
        activity = this,
        heraUserProperties = HeraUserProperties(
            device_id = deviceId,
            language = deviceUtils.local,
            country = mobileCountryCode(),
            advertise_attributions = AttributionAgent.deepLinkResponse.attributions,
            extra_data = extraData
        )
    )

    EventBus.subscribe(Consumer {
        when (it.type) {
            "DEEP_LINK_RESPONSE".hashCode() -> {
                  // You must call your set user properties method in here
            }
        }
    })

## Load Ads

You can easily load your ads from Hera with action key. 

    Hera.loadAd(activity : Activity, action : String)

Usage example

    Hera.loadAd(
      activity = this,
      action = "inAppTransition"
    )

 

## Show Ads

You can show your ads after load.

    Hera.showAd(
      activity = this,
      action = "inAppTransition"
    ) {
      TODO("lambda function called after an ad shown or ad failed to shown")
      openNextActivity()
    }

## Events

Event|Type|Description
-----|----|-----------
HERA_READY|-|It fires After fetch config successful.
AD_LOADED|String|
AD_SHOWED|String|
AD_CLOSED|String|
AD_CLICKED|String|
AD_FAILED|String|

You can use Ares EventBus system with Mediation 

    Hera.subscribe(Consumer {
        when (it.type) {
            "DEEP_LINK_RESPONSE".hashCode() -> {
                setHeraProperties()
            }
            MediationEvent.AD_CLOSED.value -> {
                openNextActivity()
            }
            MediationEvent.AD_FAILED.value -> {
                showToast()
            }
            MediationEvent.HERA_READY.value -> {
                lifecycleScope.launch(Dispatchers.Main) {
                    Hera.loadAd(this@SplashActivity, "afterLanding")
                }
                //After second fetch config for premium state changes. Landing closed will occur here
                if (cache.isPro.value == true) {
                    aresLanding.closeLanding()
                }

            }
        }
    })

## Ad Network Integration

It is possible to integrate a third party network by adding corresponding network adapter in app module dependencies.

    implementation "hera:hera-applovin:10.2.0.0"
    implementation "hera:hera-chartboost:8.2.0.3"
    implementation "hera:hera-ironsource:7.1.5.0"
    implementation "hera:hera-unityads:3.7.1.0"
    implementation "hera:hera-vungle:6.9.1.2"
    implementation "hera:hera-tiktok:3.6.0.0"

### Applovin Integration

Besides adding required adapter, Applovin also requires api key. 

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
