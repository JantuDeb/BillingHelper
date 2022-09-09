# BillingHelper
Wrapper around Google Play Billing Library, simplifying its use. 
Handles client connection, querying product details, owned purchase, different purchase types, acknowledging purchases etc.

## How does it work?
Make sure your Activity/Fragment implements BillingListener and initializes BillingHelper
``` kotlin
class MainActivity: AppCompatActivity(), BillingListener {

    lateinit var billing: BillingHelper

    override fun onCreate(savedInstanceState: Bundle?, persistentState: PersistableBundle?) {
        super.onCreate(savedInstanceState, persistentState)

        // Construct helper - by default, connection will be initialized immediately with product details 
        // and owned purchases queried. All events are reported via billingListener.
        // At least one of productInAppPurchases or productSubscriptions is required.
        // For more configuration options, check BillingHelper constructor parameters.
        billing = BillingHelper(
            context = this, 
            productInAppPurchases = listOf("inAppPurchaseSkuName1", "inAppPurchaseSkuName2"),
            productSubscriptions = listOf("subscriptionSkuName"),
            billingListener = this
        )
    }

    override fun onDestroy() {
        super.onDestroy()
        // make sure to clean it up when you're done
        billing.endClientConnection()
    }

    override fun onBillingEvent(event: BillingEvent, message: String?) {
        // receive an event based on calls to billing
    }
}
```

<br/>Use any of its public methods or attributes, BillingHelper will do all the heavy lifting and always report changes via BillingListener
``` kotlin
fun consumePurchase(purchase: Purchase)
fun endClientConnection()
fun getPurchaseForProductName(productName: String): Purchase?
fun getProductDetails(productName: String): ProductDetails?
fun isPurchased(productName: String): Boolean
fun launchPurchaseFlow(activity: Activity, productName: String)
fun launchPriceChangeConfirmationFlow(activity: Activity, productDetails: ProductDetails)
fun initClientConnection(queryForProductDetailsOnConnected: Boolean, queryForOwnedPurchasesOnConected: Boolean)
fun initQueryOwnedPurchases()
fun initQueryProductDetails()
fun acknowledgePurchases(purchases: List<Purchase>)
fun isFeatureSupported(feature: String)
fun addBillingListener(listener: BillingListener)
fun removeBillingListener(listener: BillingListener)

var queryProductDetailsOnConnected: Boolean
var queryOwnedPurchasesOnConnected: Boolean
var autoAcknowledgePurchases: Boolean
val billingReady: Boolean
val connectionState: Int
val purchasesQueried: Boolean
val productDetailsQueried: Boolean
val isConnectionFailure: Boolead
val purchasesQueriedOrConnectionFailure: Boolean
```

<br/>BillingEvent includes all of the things you might be interested in, served via BillingListener 
``` kotlin
enum class BillingEvent {
    BILLING_CONNECTED,
    BILLING_CONNECTION_FAILED,
    BILLING_DISCONNECTED,
    QUERY_PRODUCT_DETAILS_COMPLETE,
    QUERY_PRODUCT_DETAILS_FAILED,
    QUERY_OWNED_PURCHASES_COMPLETE,
    QUERY_OWNED_PURCHASES_FAILED,
    PURCHASE_COMPLETE,
    PURCHASE_FAILED,
    PURCHASE_CANCELLED,
    PURCHASE_ACKNOWLEDGE_SUCCESS,
    PURCHASE_ACKNOWLEDGE_FAILED,
    CONSUME_PURCHASE_SUCCESS,
    CONSUME_PURCHASE_FAILED
}
```

## Great! How do I get started?
Make sure root build.gradle repositories include JitPack
``` gradle
allprojects {
    repositories {
        maven { url 'https://jitpack.io' }
    }
}
```

And BillingHelper dependency is added to app build.gradle
``` gradle
dependencies {
    implementation "com.github.mvojtkovszky:BillingHelper:$latest_version"
}
```
