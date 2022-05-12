<img src="https://massets.appsflyer.com/wp-content/uploads/2018/06/20092440/static-ziv_1TP.png"  width="400" > 

# Android Purchase Connector

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

🛠 In order for us to provide optimal support, we would kindly ask you to submit any issues to
support@appsflyer.com

> *When submitting an issue please specify your AppsFlyer sign-up (account) email , your app ID , production steps, logs, code snippets and any additional relevant information.*

### <a id="plugin-build-for"> This module is built for

- Android AppsFlyer SDK **6.6.0**

### 📲 Adding the SDK to your project

Add to your build.gradle file:

```
implementation 'com.appsflyer:purchase-connector:0.1.0'
implementation 'com.android.billingclient:billing:$play_billing_version'
```

where play_billing_version is 3.x.x. Note, version 4 of Billing Client is not supported!

### 🚀 Basic integration of the SDK

```
new PurchaseClient.Builder(
                // context
                this,
                // Store enum
                Store.GOOGLE)
                // Enable subs auto logging
                .logSubscriptions(true)
                // Set sandbox (false by default)
                .setSandbox(true)
                // Purchase Event Data source listener. Invoked before sending data to AF servers
                // to let customer add extra parameters to the payload
                .setPurchaseEventDataSource(list -> {
                    Map<String, Object> additionalParams = new HashMap<>();
                    Log.d(LOG_TAG, "[PurchaseConnector]: Data Source invoked");
                    for (PurchaseEvent p : list) {
                        Log.d(LOG_TAG, "SKU = " + p.getSku());
                        additionalParams.put("customParam", p.getSku());
                    }
                    return additionalParams;
                })
                // Purchase Validation listener. Invoked after getting response from AF servers
                // to let customer know if purchase was validated successfully
                .setValidationResultListener(new PurchaseClient.ValidationResultListener() {
                    @Override
                    public void onSuccess(String s) {
                        Log.d(LOG_TAG, "[PurchaseConnector]: Validation success: " + s);
                    }

                    @Override
                    public void onFail(@NonNull String s, @Nullable Throwable throwable) {
                        Log.d(LOG_TAG, "[PurchaseConnector]: Validation fail: " + s);
                        if (throwable != null) {
                            throwable.printStackTrace();
                        }
                    }
                })
                // Build the client and automatically starts it
                .build();
```
