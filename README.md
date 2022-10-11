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

- Android AppsFlyer SDK **6.8.0** and above

## <a id="install-connector">  Adding The Connector To Your Project

Add to your build.gradle file:

```
implementation 'com.appsflyer:purchase-connector:1.0.0'
implementation 'com.android.billingclient:billing:$play_billing_version'
```

where `play_billing_version` is 4.x.x. </br>


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
        // init - Make sure to save a reference to the built object. If the object is not saved, 
        // it could lead to unexpected behavior and memory leaks.
        val afPurchaseClient = PurchaseClient.Builder(context, Store.GOOGLE)
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
            // In Apps Purchase Validation listener. Invoked after getting response from AppsFlyer servers
            // to let customer know if purchase was validated successfully
            .setInAppValidationResultListener(object :
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
            // Build the client
            .build()

        // Start the SDK instance to observe transactions.
        afPurchaseClient.startObservingTransactions()

```

* Java
```java
        // init - Make sure to save a reference to the built object. If the object is not saved,
        // it could lead to unexpected behavior and memory leaks.
        PurchaseClient afPurchaseClient = new PurchaseClient.Builder(context, Store.GOOGLE)
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
```
