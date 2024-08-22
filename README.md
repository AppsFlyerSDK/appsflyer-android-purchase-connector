<img src="https://massets.appsflyer.com/wp-content/uploads/2018/06/20092440/static-ziv_1TP.png"  width="400" > 

# Android Purchase Connector

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/AppsFlyerSDK/android-purchase-connector/blob/main/LICENSE)
[![Release Artifacts](https://img.shields.io/nexus/r/com.appsflyer/purchase-connector.svg?server=https%3A%2F%2Foss.sonatype.org)](https://repo1.maven.org/maven2/com/appsflyer/purchase-connector/)

🛠 In order for us to provide optimal support, we would kindly ask you to submit any issues to
support@appsflyer.com

> *When submitting an issue please specify your AppsFlyer sign-up (account) email , your app ID , production steps, logs, code snippets and any additional relevant information.*

<!-- TOC start -->
## Table Of Content
* [This Module is Built for](#plugin-build-for)
* [Important Note](#important-note)
* [Adding The Connector To Your Project](#install-connector)
* [Basic Integration Of The Connector](#basic-integration)
    + [Create PurchaseClient Instance ](#create-instance)
    + [Start Observing Transactions](#start)
    + [Stop Observing Transactions](#stop)
    + [Log Subscriptions](#log-subscriptions)
    + [Log In App](#log-inapps)
* [Register Purchase Event Data Source](#data-source)
    + [Subscription Purchase Event Data Source](#ars-data-source)
    + [In Apps Purchase Event Data Source](#inapps-data-source)
* [Register Validation Results Listeners](#validation-callbacks)
    + [Subscription Validation Result Listener](#ars-validation-callbacks)
    + [In Apps Validation Result Listener](#inapps-validation-callbacks)
* [ Testing The Integration](#testing)
* [Full Code Example](#example)
<!-- TOC end -->

## <a id="plugin-build-for"> This Module is Built for

- Android AppsFlyer SDK **6.15.0** and above

## <a id="important-note"> ⚠️ ⚠️ Important Note ⚠️ ⚠️ 

Some Purchase Connector versions require a specific version of the AppsFlyer SDK to operate properly.
The following table details the compatibility between the Purchase Connector versions and the SDK versions.

| Purchase Connector Version | Supported AppsFlyer SDK Versions | Supported Billing Library Versions 
|----------------------------|------------------------------------|----------------------------------|
| v2.0.0                     | v6.12.2 - v6.14.3                | v5.x.x                             |
| v2.0.1                     | v6.12.2 - v6.14.3                | v5.x.x - v6.x.x                    |
| v2.1.0                     | v6.15.0 (and above)                |v5.x.x - v6.x.x                    |


## <a id="install-connector">  Adding The Connector To Your Project

1. Add to your build.gradle file:

   ```
   implementation 'com.appsflyer:purchase-connector:2.1.0'
   implementation 'com.android.billingclient:billing:$play_billing_version'
   ```

   where `play_billing_version` is 5.x.x or 6.x.x. </br>
2.  If you are using ProGuard, add following keep rules to your `proguard-rules.pro` file:
    ```grooby
    -keep class com.appsflyer.** { *; }
    -keep class kotlin.jvm.internal.Intrinsics{ *; }
    -keep class kotlin.collections.**{ *; }
    ```

## <a id="basic-integration"> Basic Integration Of The Connector
### <a id="create-instance"> Create PurchaseClient Instance
Create an instance of this Connector to configure (in the following steps) for observing and validating transactions in your app. </br>
**Make sure to save a reference to the built object. If the object is not saved, it could lead to unexpected behavior and memory leaks.** </br>

* Kotlin
```kotlin
        // init
        val builder = PurchaseClient.Builder(this, Store.GOOGLE)
        // Make sure to keep this instance
        val afPurchaseClient = builder.build()
```

* Java
```java
        // init
        PurchaseClient.Builder builder = new PurchaseClient.Builder(context, Store.GOOGLE);
        // Make sure to keep this instance
        PurchaseClient purchaseClient = builder.build();
```

### <a id="start"> Start Observing Transactions
Start the SDK instance to observe transactions. </br>

**⚠️ Please Note**
> This should be called right after calling the Android SDK's [`start`](https://dev.appsflyer.com/hc/docs/integrate-android-sdk#starting-the-android-sdk) method.
>  Calling `startObservingTransactions` activates a listener that automatically observes new billing transactions. This includes new and existing subscriptions and new in app purchases.
>  The best practice is to activate the listener as early as possible, preferably in the `Application` class.
* Kotlin
```kotlin
        // start
        afPurchaseClient.startObservingTransactions()
```

* Java
```java
        // start
        afPurchaseClient.startObservingTransactions();
```

### <a id="stop"> Stop Observing Transactions
Stop the SDK instance from observing transactions. </br>
**⚠️ Please Note**
> This should be called if you would like to stop the Connector from listening to billing transactions. This removes the listener and stops observing new transactions. 
> An example for using this API is if the app wishes to stop sending data to AppsFlyer due to changes in the user's consent (opt-out from data sharing). Otherwise, there is no reason to call this method.
> If you do decide to use it, it should be called right before calling the Android SDK's [`stop`](https://dev.appsflyer.com/hc/docs/android-sdk-reference-appsflyerlib#stop) API

* Kotlin
```kotlin
        // start
        afPurchaseClient.stopObservingTransactions()
```

* Java
```java
        // start
        afPurchaseClient.stopObservingTransactions();
```

### <a id="log-subscriptions"> Log Subscriptions
Enables automatic logging of subscription events. </br>
Set true to enable, false to disable.</br>
If this API is not used,  by default, the connector will not record Subscriptions.</br>
* Kotlin
```kotlin
        builder.logSubscriptions(true)
```

* Java
```java
        builder.logSubscriptions(true);
```

### <a id="log-inapps"> Log In App Purchases
Enables automatic logging of In-App purchase events</br>
Set true to enable, false to disable.</br>
If this API is not used,  by default, the connector will not record In App Purchases.</br>
* Kotlin
```kotlin
        builder.autoLogInApps(true)
```

* Java
```java
        builder.autoLogInApps(true);
```

##  <a id="data-source"> Register Purchase Event Data Source
Purchase Event Data source listener. Invoked before sending data to AppsFlyer servers to let the developer add extra parameters to the payload.

### <a id="ars-data-source"> Subscription Purchase Event Data Source

* Kotlin
```kotlin
        builder.setSubscriptionPurchaseEventDataSource(object : PurchaseClient.SubscriptionPurchaseEventDataSource{
            override fun onNewPurchases(purchaseEvents: List<SubscriptionPurchaseEvent>): Map<String, Any> {
                return mapOf("some key" to "some value")
            }
        })

        // or use lambda
        builder.setSubscriptionPurchaseEventDataSource {
            mapOf(
                "some key" to "some value",
                "another key" to it.size
            )
        }
```
* Java
```java
        builder.setSubscriptionPurchaseEventDataSource(new PurchaseClient.SubscriptionPurchaseEventDataSource() {
            @NonNull
            @Override
            public Map<String, Object> onNewPurchases(@NonNull List<? extends SubscriptionPurchaseEvent> purchaseEvents) {
                Map<String, Object> map = new HashMap<String, Object>();
                map.put("some key", "value");
                return map;
            }
        });

        // or use lambda 
        builder.setSubscriptionPurchaseEventDataSource(purchaseEvents -> {
            Map<String, Object> map = new HashMap<String, Object>();
            map.put("some key", "value");
            return map;
        });
```

### <a id="inapps-data-source"> In Apps Purchase Event Data Source

* Kotlin
```kotlin
        builder.setInAppPurchaseEventDataSource(object :
            PurchaseClient.InAppPurchaseEventDataSource {
            override fun onNewPurchases(purchaseEvents: List<InAppPurchaseEvent>): Map<String, Any> {
                return mapOf(
                    "some key" to "some value",
                    "another key" to purchaseEvents.size
                )
            }
        })

        // or use lambda
        builder.setInAppPurchaseEventDataSource { 
            mapOf(
                "some key" to "some value",
                "another key" to it.size
            )
        }
```
* Java
```java
        builder.setInAppPurchaseEventDataSource(new PurchaseClient.InAppPurchaseEventDataSource() {
            @NonNull
            @Override
            public Map<String, Object> onNewPurchases(@NonNull List<? extends InAppPurchaseEvent> purchaseEvents) {
                Map<String, Object> map = new HashMap<String, Object>();
                map.put("some key", "value");
                return map;
            }
        });

        // or use lambda 
        builder.setInAppPurchaseEventDataSource(purchaseEvents -> {
            Map<String, Object> map = new HashMap<String, Object>();
            map.put("some key", "value");
            return map;
        });
```


##  <a id="validation-callbacks"> Register Validation Results Listeners
You can register listeners to get the validation results once getting a response from AppsFlyer servers to let you know if the purchase was validated successfully.</br>

| Listener Method               | Description  |
|-------------------------------|--------------|
| `onResponse(result: Result?)` | Invoked when we got 200 OK response from the server (INVALID purchase is considered to be successful response and will be returned to this callback) |
|`onFailure(result: String, error: Throwable?)`|Invoked when we got some network exception or non 200/OK response from the server.|

### <a id="ars-validation-callbacks"> Subscription Validation Result Listener

* Kotlin
```kotlin
        builder.setSubscriptionValidationResultListener(object :
            PurchaseClient.SubscriptionPurchaseValidationResultListener {
            override fun onResponse(result: Map<String, SubscriptionValidationResult>?) {
                result?.forEach { (k: String, v: SubscriptionValidationResult?) ->
                    if (v.success) {
                        Log.d(TAG, "[PurchaseConnector]: Subscription with ID $k was validated successfully")
                        val subscriptionPurchase = v.subscriptionPurchase
                        Log.d(TAG, subscriptionPurchase.toString())
                    } else {
                        Log.d(TAG, "[PurchaseConnector]: Subscription with ID $k wasn't validated successfully")
                        val failureData = v.failureData
                        Log.d(TAG, failureData.toString())
                    }
                }
            }

            override fun onFailure(result: String, error: Throwable?) {
                Log.d(TAG, "[PurchaseConnector]: Validation fail: $result")
                error?.printStackTrace()
            }
        })
```

* Java
```java
        builder.setSubscriptionValidationResultListener(new PurchaseClient.SubscriptionPurchaseValidationResultListener() {
            @Override
            public void onResponse(@Nullable Map<String, ? extends SubscriptionValidationResult> result) {
                if (result == null) {
                    return;
                }
                result.forEach((k, v) -> {
                    if (v.getSuccess()) {
                        Log.d(TAG, "[PurchaseConnector]: Subscription with ID " + k + " was validated successfully");
                        SubscriptionPurchase subscriptionPurchase = v.getSubscriptionPurchase();
                        Log.d(TAG, subscriptionPurchase.toString());
                    } else {
                        Log.d(TAG, "[PurchaseConnector]: Subscription with ID " + k + " wasn't validated successfully");
                        ValidationFailureData failureData = v.getFailureData();
                        Log.d(TAG, failureData.toString());
                    }
                });
            }

            @Override
            public void onFailure(@NonNull String result, @Nullable Throwable error) {
                Log.d(TAG, "[PurchaseConnector]: Validation fail: " + result);
                if (error != null) {
                    error.printStackTrace();
                }
            }
        });
```

### <a id="inapps-validation-callbacks"> In Apps Validation Result Listener

* Kotlin
```kotlin
        builder.setInAppValidationResultListener(object :
            PurchaseClient.InAppPurchaseValidationResultListener {
            override fun onResponse(result: Map<String, InAppPurchaseValidationResult>?) {
                result?.forEach { (k: String, v: InAppPurchaseValidationResult?) ->
                    if (v.success) {
                        Log.d(TAG, "[PurchaseConnector]:  Product with Purchase Token$k was validated successfully")
                        val productPurchase = v.productPurchase
                        Log.d(TAG, productPurchase.toString())
                    } else {
                        Log.d(TAG, "[PurchaseConnector]:  Product with Purchase Token $k wasn't validated successfully")
                        val failureData = v.failureData
                        Log.d(TAG, failureData.toString())
                    }
                }
            }

            override fun onFailure(result: String, error: Throwable?) {
                Log.d(TAG, "[PurchaseConnector]: Validation fail: $result")
                error?.printStackTrace()
            }
        })
```

* Java
```java
        builder.setInAppValidationResultListener(new PurchaseClient.InAppPurchaseValidationResultListener() {
            @Override
            public void onResponse(@Nullable Map<String, ? extends InAppPurchaseValidationResult> result) {
                if (result == null) {
                    return;
                }
                result.forEach((k, v) -> {
                    if (v.getSuccess()) {
                        Log.d(TAG, "[PurchaseConnector]: Product with Purchase Token " + k + " was validated successfully");
                        ProductPurchase productPurchase = v.getProductPurchase();
                        Log.d(TAG, productPurchase.toString());
                    } else {
                        Log.d(TAG, "[PurchaseConnector]: Subscription with Purchase Token " + k + " wasn't validated successfully");
                        ValidationFailureData failureData = v.getFailureData();
                        Log.d(TAG, failureData.toString());
                    }
                });
            }

            @Override
            public void onFailure(@NonNull String result, @Nullable Throwable error) {
                Log.d(TAG, "[PurchaseConnector]: Validation fail: " + result);
                if (error != null) {
                    error.printStackTrace();
                }
            }
        });
```

## <a id="testing"> Testing The Integration
You can select which environment will be used for validation **production** or **sandbox** (production by default). The sandbox environment should be used while testing your [Google Play Billing Library integration](https://developer.android.com/google/play/billing/test).</br>
To set the environment to sandbox, call the following builder method with `true` value. Make sure to set the environment to production before uploading your app to the plat store (call the method with `false` or completely remove this call).
* Kotlin
```kotlin
        // sandbox environment
        builder.setSandbox(true)
        // production environment
        builder.setSandbox(false)

```
* Java
```java
        // sandbox environment
        builder.setSandbox(true);
        // production environment
        builder.setSandbox(false);

```

## <a id="example"> Full Code Example
* Kotlin
```kotlin
    override fun onCreate() {
        super.onCreate()
        // init and start the native AppsFlyer Core SDK
        AppsFlyerLib.getInstance().apply {
            init("YOUR_DEV_KEY", listener, applicationContext)
            start(applicationContext)
        }
        // init - Make sure to save a reference to the built object. If the object is not saved, 
        // it could lead to unexpected behavior and memory leaks.
        val afPurchaseClient = PurchaseClient.Builder(applicationContext, Store.GOOGLE)
            // Enable Subscriptions auto logging
            .logSubscriptions(true)
            // Enable In Apps auto logging
            .autoLogInApps(true)
            // set production environment
            .setSandbox(false)
            // Subscription Purchase Event Data source listener. Invoked before sending data to AppsFlyer servers
            // to let customer add extra parameters to the payload
            .setSubscriptionPurchaseEventDataSource {
                mapOf(
                    "some key" to "some value",
                    "another key" to it.size
                )
            }
            // In Apps Purchase Event Data source listener. Invoked before sending data to AppsFlyer servers
            // to let customer add extra parameters to the payload
            .setInAppPurchaseEventDataSource {
                mapOf(
                    "some key" to "some value",
                    "another key" to it.size
                )
            }
            // Subscriptions Purchase Validation listener. Invoked after getting response from AppsFlyer servers
            // to let customer know if purchase was validated successfully
            .setSubscriptionValidationResultListener(object :
                PurchaseClient.SubscriptionPurchaseValidationResultListener {
                override fun onResponse(result: Map<String, SubscriptionValidationResult>?) {
                    result?.forEach { (k: String, v: SubscriptionValidationResult?) ->
                        if (v.success) {
                            Log.d(
                                TAG,
                                "[PurchaseConnector]: Subscription with ID $k was validated successfully"
                            )
                            val subscriptionPurchase = v.subscriptionPurchase
                            Log.d(TAG, subscriptionPurchase.toString())
                        } else {
                            Log.d(
                                TAG,
                                "[PurchaseConnector]: Subscription with ID $k wasn't validated successfully"
                            )
                            val failureData = v.failureData
                            Log.d(TAG, failureData.toString())
                        }
                    }
                }

                override fun onFailure(result: String, error: Throwable?) {
                    Log.d(TAG, "[PurchaseConnector]: Validation fail: $result")
                    error?.printStackTrace()
                }
            })
            // In Apps Purchase Validation listener. Invoked after getting response from AppsFlyer servers
            // to let customer know if purchase was validated successfully
            .setInAppValidationResultListener(object :
                PurchaseClient.InAppPurchaseValidationResultListener {
                override fun onResponse(result: Map<String, InAppPurchaseValidationResult>?) {
                    result?.forEach { (k: String, v: InAppPurchaseValidationResult?) ->
                        if (v.success) {
                            Log.d(
                                TAG,
                                "[PurchaseConnector]:  Product with Purchase Token$k was validated successfully"
                            )
                            val productPurchase = v.productPurchase
                            Log.d(TAG, productPurchase.toString())
                        } else {
                            Log.d(
                                TAG,
                                "[PurchaseConnector]:  Product with Purchase Token $k wasn't validated successfully"
                            )
                            val failureData = v.failureData
                            Log.d(TAG, failureData.toString())
                        }
                    }
                }

                override fun onFailure(result: String, error: Throwable?) {
                    Log.d(TAG, "[PurchaseConnector]: Validation fail: $result")
                    error?.printStackTrace()
                }
            })
            // Build the client
            .build()

        // Start the SDK instance to observe transactions.
        afPurchaseClient.startObservingTransactions()
    }
```

* Java
```java
    @Override
    public void onCreate() {
        super.onCreate();
        AppsFlyerLib.getInstance().init("YOUR_DEV_KEY", listener, getApplicationContext());
        AppsFlyerLib.getInstance().start(getApplicationContext());
        // init - Make sure to save a reference to the built object. If the object is not saved,
        // it could lead to unexpected behavior and memory leaks.
        PurchaseClient afPurchaseClient = new PurchaseClient.Builder(getApplicationContext(), Store.GOOGLE)
                // Enable Subscriptions auto logging
                .logSubscriptions(true)
                // Enable In Apps auto logging
                .autoLogInApps(true)
                // set production environment
                .setSandbox(false)
                // Subscription Purchase Event Data source listener. Invoked before sending data to AppsFlyer servers
                // to let customer add extra parameters to the payload
                .setSubscriptionPurchaseEventDataSource(purchaseEvents -> {
                    Map<String, Object> map = new HashMap<String, Object>();
                    map.put("somekey", "value");
                    map.put("type", "Subscription");
                    return map;
                })
                // In Apps Purchase Event Data source listener. Invoked before sending data to AppsFlyer servers
                // to let customer add extra parameters to the payload
                .setInAppPurchaseEventDataSource(purchaseEvents -> {
                    Map<String, Object> map = new HashMap<String, Object>();
                    map.put("somekey", "value");
                    map.put("type", "InApps");
                    return map;
                })
                // Subscriptions Purchase Validation listener. Invoked after getting response from AppsFlyer servers
                // to let customer know if purchase was validated successfully
                .setSubscriptionValidationResultListener(new PurchaseClient.SubscriptionPurchaseValidationResultListener() {
                    @Override
                    public void onResponse(@Nullable Map<String, ? extends SubscriptionValidationResult> result) {
                        if (result == null) {
                            return;
                        }
                        result.forEach((k, v) -> {
                            if (v.getSuccess()) {
                                Log.d(TAG, "[PurchaseConnector]: Subscription with ID " + k + " was validated successfully");
                                SubscriptionPurchase subscriptionPurchase = v.getSubscriptionPurchase();
                                Log.d(TAG, subscriptionPurchase.toString());
                            } else {
                                Log.d(TAG, "[PurchaseConnector]: Subscription with ID " + k + " wasn't validated successfully");
                                ValidationFailureData failureData = v.getFailureData();
                                Log.d(TAG, failureData.toString());
                            }
                        });
                    }

                    @Override
                    public void onFailure(@NonNull String result, @Nullable Throwable error) {
                        Log.d(TAG, "[PurchaseConnector]: Validation fail: " + result);
                        if (error != null) {
                            error.printStackTrace();
                        }
                    }
                })
                // In Apps Purchase Validation listener. Invoked after getting response from AppsFlyer servers
                // to let customer know if purchase was validated successfully
                .setInAppValidationResultListener(new PurchaseClient.InAppPurchaseValidationResultListener() {
                    @Override
                    public void onResponse(@Nullable Map<String, ? extends InAppPurchaseValidationResult> result) {
                        if (result == null) {
                            return;
                        }
                        result.forEach((k, v) -> {
                            if (v.getSuccess()) {
                                Log.d(TAG, "[PurchaseConnector]: Product with Purchase Token " + k + " was validated successfully");
                                ProductPurchase productPurchase = v.getProductPurchase();
                                Log.d(TAG, productPurchase.toString());
                            } else {
                                Log.d(TAG, "[PurchaseConnector]: Subscription with Purchase Token " + k + " wasn't validated successfully");
                                ValidationFailureData failureData = v.getFailureData();
                                Log.d(TAG, failureData.toString());
                            }
                        });
                    }

                    @Override
                    public void onFailure(@NonNull String result, @Nullable Throwable error) {
                        Log.d(TAG, "[PurchaseConnector]: Validation fail: " + result);
                        if (error != null) {
                            error.printStackTrace();
                        }
                    }
                })
                // Build the client
                .build();

        // Start the SDK instance to observe transactions.
        afPurchaseClient.startObservingTransactions();
    }
 
```
