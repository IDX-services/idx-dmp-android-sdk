# IDX Data Manager Provider Android SDK

This guide provides detailed instructions on how to integrate and use the IDX Data Manager Provider SDK in your Android project.

This SDK is designed to automatically add user audiences (targeting parameters) to ad requests. There are two approaches for audience calculation - native and via WebView connector.

1. The native approach is suitable when you display your content directly in the mobile application and know the required targeting parameters such as title, description, author, and others.

2. The WebView connector helps when you display your content inside a `WebView` and your content resides on a web page. In this case, the content data will be automatically processed by the Web SDK (which must be installed on the page you want to display in the WebView) and passed to the `DMPWebViewConnector`. This way, you can use this data on the native side of your application.

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Initialization](#initialization)
- [Usage](#usage)
- [Tracking Pageview Events](#tracking-pageview-events)
- [Targeting Google Ads](#targeting-google-ads)
- [Support](#support)
- [License](#license)

## Requirements

To integrate this SDK into your project, you need:

- Android 7.0 (API level 24) or above
- A ProviderId

## Installation

Incorporate the Data Manager Provider SDK into your project by adding the following line to the `dependencies` section of your `app/build.gradle` file:

```gradle
implementation 'com.dxmdp.android:datamanagerprovider:2.4.12'
implementation 'com.dxmdp.android:adbuilder:2.4.12'
```

## Initialization DataManagerProvider

The SDK requires initialization with a valid `providerId` from the IDX before usage.

Start by importing the necessary classes:

```java
import com.dxmdp.android.adbuilder.DMPAdBuilder;
import com.dxmdp.android.requests.event.EventRequestProperties;
```

Then, initialize the SDK within the `onCreate` method of your `MainActivity` class:

```java
public class MainActivity extends AppCompatActivity {
  private DMPAdBuilder dmpAdBuilder;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    String providerId = "Your Provider ID goes here";
    dmpAdBuilder = new DMPAdBuilder(
        this,
        providerId,
        "My app name",
        "1.0.0" // My app version
    );
  }
}
```

> **Note:** Replace `"Your Provider ID goes here"` with your actual `providerId` obtained from IDX representative.

## Usage

### Tracking Pageview Events

Here's an example of tracking pageview events within the `onResume` method of an Activity:

```java
@Override
protected void onResume() {
    super.onResume();

    EventRequestProperties eventRequestProperties = new EventRequestProperties();

    // Set the relevant properties
    eventRequestProperties.setUrl("/examplePage"); // Replace with the specific page URL or identifier
    eventRequestProperties.setTitle("Example Page Title"); // Replace with the specific page title
    eventRequestProperties.setDomain("your-domain.com"); // Replace with your domain
    eventRequestProperties.setAuthor("author"); // Replace with the author of the page
    eventRequestProperties.setCategory("category"); // Replace with the category of the page
    eventRequestProperties.setDescription("This is an example page."); // Replace with the description of the page
    eventRequestProperties.setTags(Arrays.asList("tag1", "tag2", "tag3")); // Replace with the tags related to the page

    // Send the event
    dmpAdBuilder.sendEventRequest(eventRequestProperties);
}
```

This sends a pageview event each time a user visits the specified page in your application.

## Initialization DMPWebViewConnector

```java
import com.dxmdp.android.DMPWebViewConnector;
```

Then, initialize the Connector within the `onCreate` method of your `MainActivity` class.

If you have multiple webviews, you need to create a separate DMPWebViewConnector for each of them. DMPWebViewConnector is just a small bridge, so it has practically no overhead:

```java
public class MainActivity extends AppCompatActivity {
  private String userId = "";
  private String definitionIds = "";

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    
    DMPWebViewConnector webViewConnector1 = new DMPWebViewConnector(
      getApplicationContext(),
      "My app name",
      "1.0.0" // My app version
    );

    DMPWebViewConnector webViewConnector2 = new DMPWebViewConnector(
      getApplicationContext(),
      "My app name",
      "1.0.0" // My app version
    );

    WebView webview1 = new WebView(getApplicationContext());

    webview1.getSettings().setJavaScriptEnabled(true);
    webview1.getSettings().setDomStorageEnabled(true);
    webview1.addJavascriptInterface(webViewConnector1, DMPWebViewConnector.CONNECTOR_NAME);

    WebView webview2 = new WebView(getApplicationContext());

    webview2.getSettings().setJavaScriptEnabled(true);
    webview2.getSettings().setDomStorageEnabled(true);
    webview2.addJavascriptInterface(webViewConnector2, DMPWebViewConnector.CONNECTOR_NAME);
  }
}
```

And build your Ad

```java
AdManagerAdRequest.Builder builder = new AdManagerAdRequest.Builder();

Map<String, String> customParameters1 = webViewConnector1.getCustomAdTargeting();
Map<String, String> customParameters2 = webViewConnector2.getCustomAdTargeting();
```

Then, using `builder.addCustomTargeting(key, value)`, add all key-values from `customParameters` to `builder`

## Targeting Google Ads

In order to make an ad request targeting IDX audience, you need to create an `AdManagerAdRequest.Builder` object. Then, using the `dmpAdBuilder`, add IDX Audiences to the builder.

Below are the steps to create an ad request using our SDK:

```java
// Step 1: Create an AdManagerAdRequest.Builder
AdManagerAdRequest.Builder builder = new AdManagerAdRequest.Builder();

// Step 2: Add IDX audiences using DMP SDK
// This will enhance your ad targeting by including the audiences obtained from the IDX.
AdManagerAdRequest adRequest = dmpAdBuilder
      .addIDXAudiences(builder)
      .build();

// Step 3: Retrieve and log the audiences added to the request
val audiences = adRequest.customTargeting.getString("dxseg");
Log.d("Audiences", audiences ?: "No audiences found");
```

This process results in an `AdManagerAdRequest` object which includes IDX audiences for ad targeting. You can then pass this `adRequest` object when loading an ad, as shown below:

```java
adView.loadAd(adRequest);
```

The ads loaded by Google Ads will now be targeted based on the IDX audiences.

## Support

For support, report issues in the issue tracker or reach out through our designated support channels.

## License

The Data Manager Provider SDK is licensed under the MIT License.

```
MIT License
Copyright (c) 2024 IDX LTD

Permission is granted to freely use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of this software, subject to including the above copyright notice and this permission notice in all copies or substantial portions of the Software.

The software is provided "AS IS", without warranty of any kind. Refer to the [LICENSE](LICENSE) file for full details.