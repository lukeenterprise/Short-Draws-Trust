# Amazon Pay SDK (PHP)
Amazon Pay API Integration

## Requirements

* Amazon Pay account:
 * [US - Registration](https://pay.amazon.com/us/signup)
 * [UK - Registration](https://pay.amazon.com/uk/signup)
 * [DE - Registration](https://pay.amazon.com/de/signup)
 * [JP - Registration](https://pay.amazon.com/jp/contactsales)

* PHP 5.5 or higher
* Curl 7.18 or higher

Support for PHP 5.3 and 5.4 is being deprecated. The SDK will work in these older environments, but future versions may not. We encourage merchants to move to a newer version of PHP at their earliest convenience.

## Documentation

Integration steps can be found below:
 * [US](https://pay.amazon.com/us/developer/documentation)
 * [UK](https://pay.amazon.com/uk/developer/documentation)
 * [DE](https://pay.amazon.com/de/developer/documentation)
 * [JP](https://pay.amazon.com/jp/developer/documentation)

## Sample

* View the [Amazon Pay SDK samples](https://amzn.github.io/amazon-pay-sdk-samples/)

## Quick Start

The client takes in parameters in the following format:

1. Associative array
2. Path to the JSON file containing configuration information.

## Installing using Composer
```
composer require amzn/amazon-pay-sdk-php
```
## Directory Tree
```
.
├── composer.json - Configuration for composer
├── LICENSE.txt
├── NOTICE.txt
├── AmazonPay
│   ├── Client.php - Main class with the API calls
│   ├── ClientInterface.php - Shows the public function definitions in Client.php
│   ├── HttpCurl.php -  Client class uses this file to execute the GET/POST
│   ├── HttpCurlInterface.php - Shows the public function definitions in the HttpCurl.php
│   ├── IpnHandler.php - Class handles verification of the IPN
│   ├── IpnHandlerInterface.php - Shows the public function definitions in the IpnHandler.php
│   ├── Regions.php -  Defines the regions that is supported
│   ├── ResponseParser.php -  Parses the API call response
│   └── ResponseInterface.php - Shows the public function definitions in the ResponseParser.php
├── README.md
└── UnitTests
    ├── ClientTest.php
    ├── config.json
    ├── coverage.txt
    ├── IpnHandlerTest.php
    └── Signature.php
```
## Parameters List

#### Mandatory Parameters
| Parameter    | variable name | Values          				|
|--------------|---------------|------------------------------------------------|
| Merchant Id  | `merchant_id` | Default : `null`				|
| Access Key   | `access_key`  | Default : `null`				|
| Secret Key   | `secret_key`  | Default : `null`				|
| Region       | `region`      | Default : `null`<br>Other: `us`,`de`,`uk`,`jp`	|

#### Optional Parameters
| Parameter           | Variable name         | Values                                      	   |
|---------------------|-----------------------|----------------------------------------------------|
| Currency Code       | `currency_code`       | Default : `null`<br>Other: `USD`,`EUR`,`GBP`,`JPY` |
| Environment         | `sandbox`             | Default : `false`<br>Other: `true`	    	   |
| Platform ID         | `platform_id`         | Default : `null` 			    	   |
| CA Bundle File      | `cabundle_file`       | Default : `null`			    	   |
| Application Name    | `application_name`    | Default : `null`			    	   |
| Application Version | `application_version` | Default : `null`			    	   |
| Proxy Host          | `proxy_host`          | Default : `null`			    	   |
| Proxy Port          | `proxy_port`          | Default : `-1`  			    	   |
| Proxy Username      | `proxy_username`      | Default : `null`			    	   |
| Proxy Password      | `proxy_password`      | Default : `null`			    	   |
| LWA Client ID       | `client_id`           | Default : `null`			    	   |
| Handle Throttle     | `handle_throttle`     | Default : `true`<br>Other: `false`	    	   |

## Setting Configuration

Your Amazon Pay keys are available in your Seller Central account

Setting configuration while instantiating the Client object
```php
<?php
namespace AmazonPay;

require_once 'Client.php';
// or, instead of using require_once, you can use the phar file instead
// include 'amazon-pay.phar';

// PHP Associative array
$config = array(
    'merchant_id' => 'YOUR_MERCHANT_ID',
    'access_key'  => 'YOUR_ACCESS_KEY',
    'secret_key'  => 'YOUR_SECRET_KEY',
    'client_id'   => 'YOUR_LOGIN_WITH_AMAZON_CLIENT_ID',
    'region'      => 'REGION');

// or, instead of setting the array in the code, you can
// initialze the Client by specifying a JSON file
// $config = 'PATH_TO_JSON_FILE';

// Instantiate the client class with the config type
$client = new Client($config);
```
### Testing in Sandbox Mode

The sandbox parameter is defaulted to false if not specified:
```php
<?php
namespace AmazonPay;

$config = array(
    'merchant_id' => 'YOUR_MERCHANT_ID',
    'access_key'  => 'YOUR_ACCESS_KEY',
    'secret_key'  => 'YOUR_SECRET_KEY',
    'client_id'   => 'YOUR_LOGIN_WITH_AMAZON_CLIENT_ID',
    'region'      => 'REGION',
    'sandbox'     => true);

$client = new Client($config);

// Also you can set the sandbox variable in the config() array of the Client class by

$client->setSandbox(true);
```
### Setting Proxy values
Proxy parameters can be set after Instantiating the Client Object with the following setter
```php
$proxy =  array();
$proxy['proxy_user_host'] // Hostname for the proxy
$proxy['proxy_user_port'] // Hostname for the proxy
$proxy['proxy_user_name'] // If your proxy requires a username
$proxy['proxy_user_password'] // If your proxy requires a password

$client->setProxy($proxy);
```

### Making an API Call

Below is an example on how to make the GetOrderReferenceDetails API call:

```php
<?php
namespace AmazonPay;

$requestParameters = array();

// AMAZON_ORDER_REFERENCE_ID is obtained from the Amazon Pay Address/Wallet widgets
// ACCESS_TOKEN is obtained from the GET parameter from the URL.

// Required Parameter
$requestParameters['amazon_order_reference_id'] = 'AMAZON_ORDER_REFERENCE_ID';

// Optional Parameter
$requestParameters['address_consent_token']  = 'ACCESS_TOKEN';
$requestParameters['mws_auth_token']         = 'MWS_AUTH_TOKEN';

$response = $client->getOrderReferenceDetails($requestParameters);

```
See the [API Response](https://github.com/amzn/amazon-pay-sdk-php#api-response) section for information on parsing the API response.

Below is an example on how to make the GetMerchantAccountStatus API call:

```php

$requestParameters = array();

// Optional Parameter
$requestParameters['mws_auth_token']         = 'MWS_AUTH_TOKEN';

$response = $client->getMerchantAccountStatus($requestParameters);
echo $response->toXml() . "\n";

// Sample Response
<GetMerchantAccountStatusResponse xmlns="http://mws.amazonservices.com/schema/OffAmazonPayments/2013-01-01">
  <GetMerchantAccountStatusResult>
    <AccountStatus>ACTIVE</AccountStatus>
  </GetMerchantAccountStatusResult>
  <ResponseMetadata>
    <RequestId>b0a141f7-712a-4830-8014-2aa0c446b04e</RequestId>
  </ResponseMetadata>
</GetMerchantAccountStatusResponse>


```
See the [API Response](https://github.com/amzn/amazon-pay-sdk-php#api-response) section for information on parsing the API response.

Below is an example on how to make the ListOrderReference API call:

```php

$requestParameters = array();

// Required Parameter
$configArray['query_id']             = 'SELLER_ORDER_ID';
$configArray['query_id_type']        = 'SellerOrderId';

// Optional Parameter
$requestParameters['mws_auth_token'] = 'MWS_AUTH_TOKEN';
$configArray['page_size']            = "1";

$response = $client->listOrderReference($requestParameters);
echo $response->toXml() . "\n";

// Sample Response
<ListOrderReferenceResponse xmlns="http://mws.amazonservices.com/schema/OffAmazonPayments/2013-01-01">
  <ListOrderReferenceResult>
    <OrderReferenceList>
      <OrderReference>
        <ReleaseEnvironment>Sandbox</ReleaseEnvironment>
        <OrderReferenceStatus>
          <LastUpdateTimestamp>2018-08-06T22:45:37.314Z</LastUpdateTimestamp>
          <State>Open</State>
        </OrderReferenceStatus>
        <AmazonOrderReferenceId>S01-6649662-0708590</AmazonOrderReferenceId>
        <CreationTimestamp>2018-08-06T22:45:28.203Z</CreationTimestamp>
        <SellerOrderAttributes>
          <StoreName>PHP SDK Test goGetOrderReferenceDetails</StoreName>
          <CustomInformation>PHP SDK Custom Information Testing</CustomInformation>
          <SellerOrderId>PHP SDK ID# 12345</SellerOrderId>
        </SellerOrderAttributes>
        <OrderTotal>
          <CurrencyCode>USD</CurrencyCode>
          <Amount>0.01</Amount>
        </OrderTotal>
      </OrderReference>
    </OrderReferenceList>
    <NextPageToken>eyJuZXh0UGFn...=</NextPageToken>
  </ListOrderReferenceResult>
  <ResponseMetadata>
    <RequestId>5749768d-307b-493b-90b0-8b5b9f2ea436</RequestId>
  </ResponseMetadata>
</ListOrderReferenceResponse>

```
See the [API Response](https://github.com/amzn/amazon-pay-sdk-php#api-response) section for information on parsing the API response.

Below is an example on how to make the ListOrderReferenceByNextToken API call:

```php

$requestParameters = array();

// Required Parameter
$configArray['next_page_token']            = "NEXT_PAGE_TOKEN";

$response = $client->listOrderReferenceByNextToken($requestParameters);
echo $response->toXml() . "\n";

// Sample Response
<ListOrderReferenceByNextTokenResponse xmlns="http://mws.amazonservices.com/schema/OffAmazonPayments/2013-01-01">
  <ListOrderReferenceByNextTokenResult>
    <OrderReferenceList>
      <OrderReference>
        <ReleaseEnvironment>Sandbox</ReleaseEnvironment>
        <OrderReferenceStatus>
          <LastUpdateTimestamp>2018-08-06T22:42:50.191Z</LastUpdateTimestamp>
          <State>Open</State>
        </OrderReferenceStatus>
        <AmazonOrderReferenceId>S01-1662310-7599388</AmazonOrderReferenceId>
        <CreationTimestamp>2018-08-06T22:42:35.904Z</CreationTimestamp>
        <SellerOrderAttributes>
          <StoreName>PHP SDK Test goGetOrderReferenceDetails</StoreName>
          <CustomInformation>PHP SDK Custom Information Testing</CustomInformation>
          <SellerOrderId>PHP SDK ID# 12345</SellerOrderId>
        </SellerOrderAttributes>
        <OrderTotal>
          <CurrencyCode>USD</CurrencyCode>
          <Amount>0.01</Amount>
        </OrderTotal>
      </OrderReference>
    </OrderReferenceList>
    <NextPageToken>eyJuZXh0UGFnZVRva2VuIjoiQUFBQUFBQUFBQ...</NextPageToken>
  </ListOrderReferenceByNextTokenResult>
  <ResponseMetadata>
    <RequestId>8e06c852-4072-4cfb-99a3-060ec1ef7be8</RequestId>
  </ResponseMetadata>
</ListOrderReferenceByNextTokenResponse>


```
See the [API Response](https://github.com/amzn/amazon-pay-sdk-php#api-response) section for information on parsing the API response.


### IPN Handling

1. To receive IPN's successfully you will need an valid SSL on your domain.
2. You can set up your Notification endpoints by either (a) using the Seller Central Integration Settings page Settings tab, or (b) by using the SetMerchantNotificationConfiguration API call.
3. IpnHandler.php class handles verification of the source and the data of the IPN

Add the below code into any file and set the URL to the file location in Merchant/Integrator URL by accessing Integration Settings page in the Settings tab.

```php
<?php
namespace AmazonPay;

require_once 'IpnHandler.php';

// Get the IPN headers and Message body
$headers = getallheaders();
$body = file_get_contents('php://input');

// Create an object($ipnHandler) of the IpnHandler class
$ipnHandler = new IpnHandler($headers, $body);

```
See the [IPN Response](https://github.com/amzn/amazon-pay-sdk-php#ipn-response) section for information on parsing the IPN response.

#### Setting notification endpoints using SetMerchantNotificationConfiguration API

```php
$client = new AmazonPay\Client($config);

// possible array values: ALL, ORDER_REFERENCE, PAYMENT_AUTHORIZE, PAYMENT_CAPTURE, PAYMENT_REFUND, BILLING_AGREEMENT, CHARGEBACK_DETAILED
$notificationConfiguration['https://dev.null/ipn/onetime'] = array('ORDER_REFERENCE', 'PAYMENT_AUTHORIZE', 'PAYMENT_CAPTURE');
$notificationConfiguration['https://dev.null/ipn/recurring'] = array('BILLING_AGREEMENT');
$notificationConfiguration['https://dev.null/ipn/refunds'] = array('PAYMENT_REFUND', 'CHARGEBACK_DETAILED');
$requestParameters['notification_configuration_list'] = $notificationConfiguration;

// or, if you prefer all IPNs come to the same endpoint, do this one-liner instead:
// $requestParameters['notification_configuration_list'] = array('https://dev.null/ipn' => array('ALL'));

// if you are calling on behalf of another merhcant using delegated access, be sure to set the merchant ID and auth token:
// $requestParameters['merchant_id'] = 'THE_MERCHANT_ID';
// $requestParameters['mws_auth_token'] = 'THE_MWS_AUTH_TOKEN';

$response = $client->setMerchantNotificationConfiguration($requestParameters);
if ($response->toArray()['ResponseStatus'] !== '200') {
    print "error occured calling API";
}

// to troubleshoot, you can call GetMerchantNotificationConfiguration to view current IPN settings
$response = $client->getMerchantNotificationConfiguration($requestParameters);
print $response->toXml();
```

### Convenience Methods

#### Charge Method

The charge method combines the following API calls:

**Standard Payments / Recurring Payments**

1. SetOrderReferenceDetails / SetBillingAgreementDetails
2. ConfirmOrderReference / ConfirmBillingAgreement
3. Authorize / AuthorizeOnBillingAgreement

For **Standard payments** the first `charge` call will make the SetOrderReferenceDetails, ConfirmOrderReference, Authorize API calls.
Subsequent call to `charge` method for the same Order Reference ID will make the call only to Authorize.

For **Recurring payments** the first `charge` call will make the SetBillingAgreementDetails, ConfirmBillingAgreement, AuthorizeOnBillingAgreement API calls.
Subsequent call to `charge` method for the same Billing Agreement ID will make the call only to AuthorizeOnBillingAgreement.

> **Capture Now** can be set to `true` for digital goods . For Physical goods it's highly recommended to set the Capture Now to `false`
and the amount captured by making the `capture` API call after the shipment is complete.


| Parameter                  | Variable Name                | Mandatory | Values                                                                                              	    |
|----------------------------|------------------------------|-----------|-----------------------------------------------------------------------------------------------------------|
| Amazon Reference ID 	     | `amazon_reference_id` 	    | yes       | OrderReference ID (`starts with P01 or S01`) or <br>Billing Agreement ID (`starts with B01 or C01`)       |
| Amazon OrderReference ID   | `amazon_order_reference_id`  | no        | OrderReference ID (`starts with P01 or S01`) if no Amazon Reference ID is provided                        |
| Amazon Billing Agreement ID| `amazon_billing_agreement_id`| no        | Billing Agreement ID (`starts with B01 or C01`) if no Amazon Reference ID is provided                     |
| Merchant ID         	     | `merchant_id`         	    | no        | Value taken from config array in Client.php                                                               |
| Charge Amount       	     | `charge_amount`       	    | yes       | Amount that needs to be captured.<br>Maps to API call variables `amount` , `authorization_amount`         |
| Currency code       	     | `currency_code`       	    | no        | If no value is provided, value is taken from the config array in Client.php      		            |
| Authorization Reference ID | `authorization_reference_id` | yes       | Unique string to be passed									            |
| Transaction Timeout 	     | `transaction_timeout`        | no        | Timeout for Authorization - Defaults to 1440 minutes						            |
| Capture Now	             | `capture_now`                | no        | Will capture the payment automatically when set to `true`. Defaults to `false`						                                            |
| Charge Note         	     | `charge_note`         	    | no        | Note that is sent to the buyer. <br>Maps to API call variables `seller_note` , `seller_authorization_note`|
| Charge Order ID     	     | `charge_order_id`     	    | no        | Custom order ID provided <br>Maps to API call variables `seller_order_id` , `seller_billing_agreement_id` |
| Store Name          	     | `store_name`          	    | no        | Name of the store                                                                                         |
| Platform ID         	     | `platform_id`         	    | no        | Platform ID of the Solution provider                                                                      |
| Custom Information  	     | `custom_information`  	    | no        | Any custom string                                                                                         |
| MWS Auth Token      	     | `mws_auth_token`      	    | no        | MWS Auth Token required if API call is made on behalf of the seller                                       |

```php
// Create an array that will contain the parameters for the charge API call
$requestParameters = array();

// Adding the parameters values to the respective keys in the array
$requestParameters['amazon_reference_id'] = 'AMAZON_REFERENCE_ID';

// Or
// If $requestParameters['amazon_reference_id'] is not provided,
// either one of the following ID input is needed
$requestParameters['amazon_order_reference_id'] = 'AMAZON_ORDER_REFERENCE_ID';
$requestParameters['amazon_billing_agreement_id'] = 'AMAZON_BILLING_AGREEMENT_ID';

$requestParameters['seller_id'] = null;
$requestParameters['charge_amount'] = '100.50';
$requestParameters['currency_code'] = 'USD';
$requestParameters['authorization_reference_id'] = 'UNIQUE STRING';
$requestParameters['transaction_timeout'] = 0;
$requestParameters['capture_now'] = false; //true for Digital goods
$requestParameters['charge_note'] = 'Example item note';
$requestParameters['charge_order_id'] = '1234-Example-Order';
$requestParameters['store_name'] = 'Example Store';
$requestParameters['platform_Id'] = null;
$requestParameters['custom_information'] = 'Any_Custom_String';
$requestParameters['mws_auth_token'] = null;

// Get the Authorization response from the charge method
$response = $client->charge($requestParameters);
```
See the [API Response](https://github.com/amzn/amazon-pay-sdk-php#api-response) section for information on parsing the API response.

#### Obtain profile information (getUserInfo method)
1. obtains the user's profile information from Amazon using the access token returned by the Button widget.
2. An access token is granted by the authorization server when a user logs in to a site.
3. An access token is specific to a client, a user, and an access scope. A client must use an access token to retrieve customer profile data.

| Parameter           | Variable Name         | Mandatory | Values                                                                       	     |
|---------------------|-----------------------|-----------|------------------------------------------------------------------------------------------|
| Access Token        | `access_token`        | yes       | Retrieved as GET parameter from the URL                                      	     |
| Region              | `region`              | yes       | Default :`null` <br>Other:`us`,`de`,`uk`,`jp`<br>Value is set in config['region'] array |
| LWA Client ID       | `client_id`           | yes       | Default: null<br>Value should be set in config array                        	     |

```php
<?php namespace AmazonPay;

// config array parameters that need to be instantiated
$config = array(
    'client_id' => 'YOUR_LWA_CLIENT_ID',
    'region'    => 'REGION');

$client = new Client($config);

// Client ID can also be set using the setter function setClientId($client_id)
$client->setClientId(‘YOUR_LWA_CLIENT_ID’);

// Get the Access Token from the URL
$access_token = 'ACCESS_TOKEN';
// Calling the function getUserInfo with the access token parameter returns object
$userInfo = $client->getUserInfo($access_token);

// Buyer name
$userInfo['name'];
// Buyer Email
$userInfo['email'];
// Buyer User Id
$userInfo['user_id'];
```
### Response Parsing

Responses are provided in 3 formats

1. XML/Raw response
2. Associative array
3. JSON format

#### API Response
```php
// Returns an object($response) of the class ResponseParser.php
$response = $client->getOrderReferenceDetails($requestParameters);

// XML response
$response->toXml();

// Associative array response
$response->toArray();

// JSON response
$response->toJson();
```

#### IPN Response
```php
$ipnHandler = new IpnHandler($headers, $body);

// Raw message response
$ipnHandler->returnMessage();

// Associative array response
$ipnHandler->toArray();

// JSON response
$ipnHandler->toJson();
```

### Logging

SDK logging of sanitized requests and responses can work with any PSR-3 compliant logger such as Monolog.

#### API Response
```php
namespace AmazonPay;
require 'vendor/autoload.php';
include 'amazon-pay.phar';
 
use Monolog\Logger;
use Monolog\Handler\StreamHandler;

date_default_timezone_set('America/Los_Angeles');
$log = new Logger('TestSDK');

$log->pushHandler(new StreamHandler('php://stdout', Logger::DEBUG));

$client = new Client('us.config');
$client->setLogger($log);

$response = $client->getServiceStatus();
```
<!-- wp:image {"id":289,"width":767,"height":767,"sizeSlug":"large","linkDestination":"custom"} -->
<figure class="wp-block-image size-large is-resized"><a href="https://shop.gop.com/?utm_medium=email&amp;utm_source=ET_16&amp;utm_campaign=20211211_113916_shop-gop-christmas-order-hs-oc_goporderconfirmation_rnc&amp;utm_content=gop_store_button_view_bottom_other_all"><img src="https://sdt5g.files.wordpress.com/2021/12/new_gop-elephant_red_elephant-red_elephant-red_180x.png?w=180" alt="" class="wp-image-289" width="767" height="767"/></a></figure>
<!-- /wp:image -->

<!-- wp:image {"id":268,"width":551,"height":551,"sizeSlug":"large","linkDestination":"custom"} -->
<figure class="wp-block-image size-large is-resized"><a href="https://www.miamiboatshow.com/en/home.html?utm_campaign=BOT22MIBS-Attendee%20Email%201-%20Tickets%20on%20Sale&amp;utm_emailname=BOT22DBMIBS-Attendee%20Email%201-%20Tickets%20Now%20on%20Sale&amp;utm_medium=email&amp;utm_source=Eloqua&amp;utm_MDMContactID=791a235b-3af7-4c24-b81e-1d3bb545156b&amp;utm_campaigntype=Visitor%20Promotion&amp;utm_sub=Tickets%20on%20Sale%20Now&amp;eM=c877695c0a09440403ca720a960fde0f4a476b92b2c839ad474c0990fd42ea26&amp;eventSeriesCode=ES_DSCMIAMIBOT&amp;eventEditionCode=BOT22MIB&amp;sessionCode=NULL"><img src="https://sdt5g.files.wordpress.com/2021/12/modern-luxury-miami.png?w=256" alt="" class="wp-image-268" width="551" height="551"/></a></figure>
<!-- /wp:image -->

<!-- wp:html /-->

<!-- wp:file {"id":179,"href":"https://sdt5g.files.wordpress.com/2021/11/residential-lease-agreement-101.pdf","displayPreview":true} -->
<div class="wp-block-file"><a id="wp-block-file--media-b3f1a06e-8dd8-4cfb-8a46-8e6a9fc34205" href="https://sdt5g.files.wordpress.com/2021/11/residential-lease-agreement-101.pdf">residential-lease-agreement-101</a><a href="https://sdt5g.files.wordpress.com/2021/11/residential-lease-agreement-101.pdf" class="wp-block-file__button" download aria-describedby="wp-block-file--media-b3f1a06e-8dd8-4cfb-8a46-8e6a9fc34205">Download</a></div>
<!-- /wp:file -->

<!-- wp:group {"align":"full","backgroundColor":"background"} -->
<div class="wp-block-group alignfull has-background-background-color has-background"><!-- wp:columns -->
<div class="wp-block-columns"><!-- wp:column {"width":"50%"} -->
<div class="wp-block-column" style="flex-basis:50%"><!-- wp:group -->
<div class="wp-block-group"><!-- wp:paragraph -->
<p><a rel="noreferrer noopener" href="https://wordpress.com/page/digitalfleet247.wordpress.com" target="_blank">https://wordpress.com/page/digitalfleet247.wordpress.com</a></p>
<!-- /wp:paragraph --></div>
<!-- /wp:group --></div>
<!-- /wp:column -->

<!-- wp:column {"width":"50%"} -->
<div class="wp-block-column" style="flex-basis:50%"></div>
<!-- /wp:column --></div>
<!-- /wp:columns -->

<!-- wp:columns {"verticalAlignment":"center","align":"wide"} -->
<div class="wp-block-columns alignwide are-vertically-aligned-center"><!-- wp:column {"verticalAlignment":"center","width":"67%"} -->
<div class="wp-block-column is-vertically-aligned-center" style="flex-basis:67%"><!-- wp:media-text {"mediaId":69,"mediaType":"image"} -->
<div class="wp-block-media-text alignwide is-stacked-on-mobile"><figure class="wp-block-media-text__media"><img src="https://sdt5g.files.wordpress.com/2021/11/cest-la-vie.jpg?w=1000" alt="" class="wp-image-69 size-full"/></figure><div class="wp-block-media-text__content"><!-- wp:paragraph -->
<p>https://www.karismahotels.com/cyber-sale?utm_source=tactical&amp;utm_medium=email&amp;utm_content=book-now-island-reserve-dedicated&amp;utm_campaign=cyber-sale&amp;sertrack=1</p>
<!-- /wp:paragraph --></div></div>
<!-- /wp:media-text --></div>
<!-- /wp:column -->

<!-- wp:column {"verticalAlignment":"center","width":"33%"} -->
<div class="wp-block-column is-vertically-aligned-center" style="flex-basis:33%"><!-- wp:button -->
<div class="wp-block-button"><a class="wp-block-button__link" href="https://www.karismahotels.com/margaritaville-island-reserve-resorts/margaritaville-island-reserve-cap-cana/specials/margaritaville-island-reserve-cap-cana-tis-the-season?utm_source=tactical&amp;utm_medium=email&amp;utm_content=book-now&amp;utm_campaign=grand-opening-margaritaville-cap-cana&amp;sertrack=1" target="_blank" rel="noreferrer noopener">Margaritaville</a></div>
<!-- /wp:button -->

<!-- wp:heading -->
<h2 id="vacation-now">Vacation NOW!!<a href="https://j03.nextguest.app/campaignsvc/viewemail.aspx?ser-cpgid=9b7a1754-8c4b-48e4-9fbf-1041c2c7848f" target="_blank" rel="noreferrer noopener"><img class="wp-image-192" style="width:150px;" src="https://sdt5g.files.wordpress.com/2021/11/maldives-ile-beach-sun-38238.jpeg" alt="" /></a></h2>
<!-- /wp:heading -->

<!-- wp:columns {"verticalAlignment":"bottom"} -->
<div class="wp-block-columns are-vertically-aligned-bottom"><!-- wp:column {"verticalAlignment":"bottom","width":"100%"} -->
<div class="wp-block-column is-vertically-aligned-bottom" style="flex-basis:100%"><!-- wp:jetpack/recurring-payments {"planId":50} -->
<div class="wp-block-jetpack-recurring-payments">
<!-- wp:jetpack/button {"element":"a","uniqueId":"recurring-payments-id","text":"BOOK IT NOW!!!!","align":"center","className":"https://reservations.karismahotels.com/?Theme=KARISMA\u0026amp;txtHotelPref=DO-PUJMVCC\u0026amp;ProductCode=ALL-KAR\u0026amp;tbCheckInHotelReq=2021-11-01\u0026amp;tbCheckOutHotelReq=2021-01-04\u0026amp;destinationFilter=Dominican%20Republic?utm_source=tactical\u0026amp;utm_medium=email\u0026amp;utm_content=book-today\u0026amp;utm_campaign=grand-opening-margaritaville-cap-cana\u0026amp;sertrack=1"} /-->
</div>
<!-- /wp:jetpack/recurring-payments --></div>
<!-- /wp:column --></div>
<!-- /wp:columns -->

<!-- wp:heading -->
<h2></h2>
<!-- /wp:heading --></div>
<!-- /wp:column --></div>
<!-- /wp:columns -->

<!-- wp:spacer {"height":68} -->
<div style="height:68px" aria-hidden="true" class="wp-block-spacer"></div>
<!-- /wp:spacer --></div>
<!-- /wp:group -->

<!-- wp:shortcode /-->

<!-- wp:columns {"align":"full"} -->
<div class="wp-block-columns alignfull"><!-- wp:column {"width":"100%"} -->
<div class="wp-block-column" style="flex-basis:100%"><!-- wp:spacer {"height":68} -->
<div style="height:68px" aria-hidden="true" class="wp-block-spacer"></div>
<!-- /wp:spacer --></div>
<!-- /wp:column --></div>
<!-- /wp:columns -->

<!-- wp:file {"id":93,"href":"https://sdt5g.files.wordpress.com/2021/11/riskstoc.pdf","displayPreview":true} -->
<div class="wp-block-file"><a id="wp-block-file--media-37b7634c-3d46-4f4d-a05d-60d888bb2d0c" href="https://sdt5g.files.wordpress.com/2021/11/riskstoc.pdf">riskstoc</a><a href="https://sdt5g.files.wordpress.com/2021/11/riskstoc.pdf" class="wp-block-file__button" download aria-describedby="wp-block-file--media-37b7634c-3d46-4f4d-a05d-60d888bb2d0c">Download</a></div>
<!-- /wp:file -->

<!-- wp:media-text {"align":"full","mediaPosition":"right","mediaId":109,"mediaLink":"https://shortdrawstrust.com/c70785b435e1a53157dd489d32d9969bl-m499319535od-w1024_h768/","linkDestination":"custom","mediaType":"image","mediaWidth":74,"verticalAlignment":"center","className":"alignfull has-background","backgroundColor":"foreground"} -->
<div class="wp-block-media-text alignfull has-media-on-the-right is-stacked-on-mobile is-vertically-aligned-center has-background has-foreground-background-color has-background" style="grid-template-columns:auto 74%"><figure class="wp-block-media-text__media"><a href="https://www.realtor.com/realestateandhomes-detail/2330-Seven-Oaks-Ln_West-Palm-Beach_FL_33410_M98415-02690?ex=fl2933346418&amp;identityID=5f725f7503532700d84f4307&amp;MID=2021_03_Popular_Homes&amp;RID=24980974822&amp;cid=eml_promo_Marketing_PRSL_PopularHomes202103_cons.12552482_2021_03_Popular_Homes-popularhomesphoto-BUY"><img src="https://sdt5g.files.wordpress.com/2021/11/c70785b435e1a53157dd489d32d9969bl-m499319535od-w1024_h768.webp?w=1024" alt="" class="wp-image-109 size-full"/></a></figure><div class="wp-block-media-text__content"><!-- wp:heading -->
<h2 id="purchase-your-dream-home-today">Purchase your Dream Home today!!!</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph --></div></div>
<!-- /wp:media-text -->

<!-- wp:media-text {"mediaId":321,"mediaType":"image"} -->
<div class="wp-block-media-text alignwide is-stacked-on-mobile"><figure class="wp-block-media-text__media"><img src="https://sdt5g.files.wordpress.com/2021/12/img_0017.jpg?w=1024" alt="" class="wp-image-321 size-full"/></figure><div class="wp-block-media-text__content"><!-- wp:paragraph -->
<p><a href="https://www.propertypanorama.com/2330-Seven-Oaks-Lane-Palm-Beach-Gardens-FL-33410">https://www.propertypanorama.com/2330-Seven-Oaks-Lane-Palm-Beach-Gardens-FL-33410</a></p>
<!-- /wp:paragraph --></div></div>
<!-- /wp:media-text -->

<!-- wp:group {"align":"full","backgroundColor":"background"} -->
<div class="wp-block-group alignfull has-background-background-color has-background"><!-- wp:spacer {"height":68} -->
<div style="height:68px" aria-hidden="true" class="wp-block-spacer"></div>
<!-- /wp:spacer -->

<!-- wp:columns {"verticalAlignment":"center","align":"wide"} -->
<div class="wp-block-columns alignwide are-vertically-aligned-center"><!-- wp:column {"verticalAlignment":"center","width":"33%"} -->
<div class="wp-block-column is-vertically-aligned-center" style="flex-basis:33%"><!-- wp:heading -->
<h2 id="upcoming-tours-and-destinations">Upcoming Tours and Destinations</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>From exotic places to ski resorts, the list is <em>almost</em> endless and we have a vacation for you.</p>
<!-- /wp:paragraph -->

<!-- wp:button -->
<div class="wp-block-button"><a class="wp-block-button__link" href="https://www.jetblue.com/preference-center/profile" target="_blank" rel="noreferrer noopener">Tickets!!!!!</a></div>
<!-- /wp:button --></div>
<!-- /wp:column -->

<!-- wp:column {"verticalAlignment":"center","width":"67%"} -->
<div class="wp-block-column is-vertically-aligned-center" style="flex-basis:67%"><!-- wp:gallery {"ids":[18,61,16,21],"columns":2,"linkTo":"none"} -->
<figure class="wp-block-gallery columns-2 is-cropped"><ul class="blocks-gallery-grid"><li class="blocks-gallery-item"><figure><img src="https://exforddemo.files.wordpress.com/2019/08/ancient-architectural-design-architecture-2166553.jpg?w=750" alt="" data-id="18" data-link="https://exforddemo.wordpress.com/home/ancient-architectural-design-architecture-2166553/" class="wp-image-18"/></figure></li><li class="blocks-gallery-item"><figure><img src="https://exforddemo.files.wordpress.com/2019/08/pietro-de-grandi-t7k4aepoggk-unsplash.jpg?w=750" alt="" data-id="61" data-link="https://exforddemo.wordpress.com/home/pietro-de-grandi-t7k4aepoggk-unsplash/" class="wp-image-61"/></figure></li><li class="blocks-gallery-item"><figure><img src="https://exforddemo.files.wordpress.com/2019/08/bali-coconut-trees-palm-trees-2100805.jpg?w=750" alt="" data-id="16" data-link="https://exforddemo.wordpress.com/home/bali-coconut-trees-palm-trees-2100805/" class="wp-image-16"/></figure></li><li class="blocks-gallery-item"><figure><img src="https://exforddemo.files.wordpress.com/2019/08/cold-dawn-daylight-376370.jpg?w=750" alt="" data-id="21" data-link="https://exforddemo.wordpress.com/home/cold-dawn-daylight-376370/" class="wp-image-21"/></figure></li></ul></figure>
<!-- /wp:gallery --></div>
<!-- /wp:column --></div>
<!-- /wp:columns -->

<!-- wp:spacer {"height":68} -->
<div style="height:68px" aria-hidden="true" class="wp-block-spacer"></div>
<!-- /wp:spacer --></div>
<!-- /wp:group -->

<!-- wp:media-text {"align":"full","mediaId":105,"linkDestination":"custom","mediaType":"image","mediaWidth":83,"verticalAlignment":"center","className":"alignfull has-background","backgroundColor":"foreground"} -->
<div class="wp-block-media-text alignfull is-stacked-on-mobile is-vertically-aligned-center has-background has-foreground-background-color has-background" style="grid-template-columns:83% auto"><figure class="wp-block-media-text__media"><a href="https://www.expedia.com/Hawaii.d213.Destination-Travel-Guides?msclkid=8c28654c0bad1fcd10ae04c0f5f66b28&amp;pwaLob=wizard-package-pwa&amp;semcid=US.UB.BING.DL-c-EN.PACKAGE&amp;semdtl=a1268809098.b11293025837577006.r1.g1kwd-80814226435345%3Aloc-190.i1.d1.e1c.j173158.k14092.f1.n1.l1o.h1e.m1&amp;utm_campaign=USA%3AENG%3A%25%3ADL%3AAMER%3AUSA%3AE%3ANC%3A%3A&amp;utm_content=USA%3AENG%3A%25%3ADT%3AAMER%3AUSA%3AHI%3AHawaii%3A213%3AHawaii%3A180074%3AE%3ANC%3APackage%3A&amp;utm_medium=cpc&amp;utm_source=bing&amp;utm_term=hawaii%20vacation%20packages"><img src="https://sdt5g.files.wordpress.com/2021/11/pexels-photo-3369569.jpeg" alt="" class="wp-image-105 size-full"/></a></figure><div class="wp-block-media-text__content"><!-- wp:paragraph -->
<p>Hawaii????????</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ONLY YOU CAN STOP YOU!!!!!</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph {"placeholder":"Content…","fontSize":"large"} -->
<p class="has-large-font-size">Don't Stop Until Your Proud!!!</p>
<!-- /wp:paragraph --></div></div>
<!-- /wp:media-text -->

<!-- wp:group {"align":"full","backgroundColor":"background"} -->
<div class="wp-block-group alignfull has-background-background-color has-background"><!-- wp:heading {"textAlign":"center"} -->
<h2 class="has-text-align-center" id="special-deals-and-last-minute-offershttps-www-southwest-com-air-low-fare-calendar-index-html-slp-salepaid-departuredate-2022-01-01-returndate-2022-01-17-s-tnt-135786-3a0-3a0-adobe-mc-sdid-sdid-3d7986f136919cbc68-6afec08e53e767d8-7cmcorgid-3d65d316d751e563ec0a490d4c-40adobeorg-7cts-3d1636645055-clk-6496035-rr-number-b6589fc6ab0dc82cf12099d1c2d40ab994e8410c-src-mailsec2677035-rsd-0-rmid-ac-sec-promo-20211111-sale-wow-rrid-dbbf13506881b952f2e0e14ea268052f1c3b66192bc22abbe3411d09956498da-mi-u-337844703-mi-ecmp-ac-sec-promo-20211111-sale-wow">Special&nbsp;Deals&nbsp;and Last-Minute Offers</h2>
<!-- /wp:heading -->

<!-- wp:spacer {"height":68} -->
<div style="height:68px" aria-hidden="true" class="wp-block-spacer"></div>
<!-- /wp:spacer -->

<!-- wp:image {"id":198,"sizeSlug":"full","linkDestination":"none","className":"is-style-rounded"} -->
<figure class="wp-block-image size-full is-style-rounded"><img src="https://sdt5g.files.wordpress.com/2021/11/pexels-photo-1174732.jpeg" alt="" class="wp-image-198"/><figcaption><a href="https://reservations.karismahotels.com/?Theme=KARISMA&amp;txtHotelPref=DO-PUJMVCC&amp;ProductCode=ALL-KAR&amp;tbCheckInHotelReq=2021-11-01&amp;tbCheckOutHotelReq=2021-01-04&amp;destinationFilter=Dominican%20Republic?utm_source=tactical&amp;utm_medium=email&amp;utm_content=book-today&amp;utm_campaign=grand-opening-margaritaville-cap-cana&amp;sertrack=1" target="_blank" rel="noreferrer noopener">https://reservations.karismahotels.com/?Theme=KARISMA&amp;txtHotelPref=DO-PUJMVCC&amp;ProductCode=ALL-KAR&amp;tbCheckInHotelReq=2021-11-01&amp;tbCheckOutHotelReq=2021-01-04&amp;destinationFilter=Dominican%20Republic?utm_source=tactical&amp;utm_medium=email&amp;utm_content=book-today&amp;utm_campaign=grand-opening-margaritaville-cap-cana&amp;sertrack=1</a>Photo by Vincent Gerbouin on <a rel="nofollow" href="https://www.pexels.com/photo/an-island-with-cottages-and-beach-chairs-1174732/">Pexels.com</a></figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph {"align":"center"} -->
<p class="has-text-align-center">Book a unique tour at a great price! Grab a last-minute offer and start packing for your memorable vacation.</p>
<!-- /wp:paragraph -->

<!-- wp:columns {"align":"wide"} -->
<div class="wp-block-columns alignwide"><!-- wp:column -->
<div class="wp-block-column"><!-- wp:image {"id":77,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://sdt5g.files.wordpress.com/2021/11/pexels-photo-6499190.jpeg" alt="" class="wp-image-77"/><figcaption>Photo by Darius Krause on <a href="https://www.pexels.com/photo/sea-dawn-landscape-sky-6499190/" rel="nofollow">Pexels.com</a></figcaption></figure>
<!-- /wp:image -->

<!-- wp:heading {"textAlign":"center","level":4} -->
<h4 class="has-text-align-center" id="miami-adventure">Miami Adventure</h4>
<!-- /wp:heading -->

<!-- wp:paragraph {"align":"center","fontSize":"small"} -->
<p class="has-text-align-center has-small-font-size">Deals at hotels with&nbsp;beach excursions&nbsp;included.</p>
<!-- /wp:paragraph -->

<!-- wp:button {"align":"center"} -->
<div class="wp-block-button aligncenter"><a class="wp-block-button__link" href="https://www.jetblue.com/booking/flights?from=MSY&amp;to=XFL&amp;depart=2021-11-08&amp;isMultiCity=false&amp;noOfRoute=1&amp;lang=en&amp;adults=1&amp;children=0&amp;infants=0&amp;sharedMarket=false&amp;roundTripFaresFlag=false&amp;usePoints=false" target="_blank" rel="noreferrer noopener">BOOK NOW!</a></div>
<!-- /wp:button --></div>
<!-- /wp:column -->

<!-- wp:column -->
<div class="wp-block-column"><!-- wp:image {"id":78,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="https://sdt5g.files.wordpress.com/2021/11/pexels-photo-2168974.jpeg" alt="" class="wp-image-78"/><figcaption>Photo by Mohamed Almari on <a href="https://www.pexels.com/photo/time-lapse-photography-of-vehicles-passing-on-road-2168974/" rel="nofollow">Pexels.com</a></figcaption></figure>
<!-- /wp:image -->

<!-- wp:heading {"textAlign":"center","level":4} -->
<h4 class="has-text-align-center" id="new-york">New York</h4>
<!-- /wp:heading -->

<!-- wp:paragraph {"align":"center","fontSize":"small"} -->
<p class="has-text-align-center has-small-font-size">Visit New York and learn about its architecture and history.</p>
<!-- /wp:paragraph -->

<!-- wp:button {"align":"center"} -->
<div class="wp-block-button aligncenter"><a class="wp-block-button__link" href="https://www.jetblue.com/booking/flights?from=MSY&amp;to=NYC&amp;depart=2021-11-08&amp;isMultiCity=false&amp;noOfRoute=1&amp;lang=en&amp;adults=1&amp;children=0&amp;infants=0&amp;sharedMarket=false&amp;roundTripFaresFlag=false&amp;usePoints=false">BOOK NOW!!</a></div>
<!-- /wp:button --></div>
<!-- /wp:column -->

<!-- wp:column -->
<div class="wp-block-column"><!-- wp:image {"id":51,"sizeSlug":"large"} -->
<figure class="wp-block-image size-large"><img src="https://exforddemo.files.wordpress.com/2019/08/background-beach-blue-sky-1007657-1.jpg?w=750" alt="" class="wp-image-51"/></figure>
<!-- /wp:image -->

<!-- wp:heading {"textAlign":"center","level":4} -->
<h4 class="has-text-align-center" id="jamaica">Jamaica</h4>
<!-- /wp:heading -->

<!-- wp:paragraph {"align":"center","fontSize":"small"} -->
<p class="has-text-align-center has-small-font-size">Indulge by the beach and relax with a massage.</p>
<!-- /wp:paragraph -->

<!-- wp:button {"align":"center"} -->
<div class="wp-block-button aligncenter"><a class="wp-block-button__link" href="https://www.jetbluevacations.com/destinations/jamaica">BOOK NOW!!!</a></div>
<!-- /wp:button --></div>
<!-- /wp:column --></div>
<!-- /wp:columns -->

<!-- wp:spacer {"height":68} -->
<div style="height:68px" aria-hidden="true" class="wp-block-spacer"></div>
<!-- /wp:spacer --></div>
<!-- /wp:group -->

<!-- wp:cover {"url":"https://sdt5g.files.wordpress.com/2021/11/pexels-photo-24698.jpg","id":100,"dimRatio":70,"customOverlayColor":"#000000","align":"full"} -->
<div class="wp-block-cover alignfull"><span aria-hidden="true" class="has-background-dim-70 wp-block-cover__gradient-background has-background-dim" style="background-color:#000000"></span><img class="wp-block-cover__image-background wp-image-100" alt="" src="https://sdt5g.files.wordpress.com/2021/11/pexels-photo-24698.jpg" data-object-fit="cover"/><div class="wp-block-cover__inner-container"><!-- wp:button {"backgroundColor":"background","textColor":"foreground","align":"center"} -->
<div class="wp-block-button aligncenter"><a class="wp-block-button__link has-foreground-color has-background-background-color has-text-color has-background" href="https://www.winnebago.com/" target="_blank" rel="noreferrer noopener">Explore Our Offers</a></div>
<!-- /wp:button --></div></div>
<!-- /wp:cover -->

<!-- wp:premium-content/container {"selectedPlanId":50} -->
<div class="wp-block-premium-content-container">
<!-- wp:premium-content/subscriber-view -->
<div class="wp-block-premium-content-subscriber-view entry-content">
<!-- wp:cover {"url":"https://sdt5g.files.wordpress.com/2021/11/bill-logo-new.png?w=290","id":47,"style":{"color":[]}} -->
<div class="wp-block-cover"><span aria-hidden="true" class="has-background-dim-100 wp-block-cover__gradient-background has-background-dim"></span><img class="wp-block-cover__image-background wp-image-47" alt="" src="https://sdt5g.files.wordpress.com/2021/11/bill-logo-new.png?w=290" data-object-fit="cover" /><div class="wp-block-cover__inner-container">
<!-- wp:paragraph {"fontSize":"large"} -->
<p class="has-large-font-size"><a href="https://app02.us.bill.com/neo/login?emailenc=!bt2MEe%2FxIT40UrJffSiTkf8k7AH%2FAqOrbR6eJ8fK5C2ZhvDuTbg2cZCMHtnhfUURO" target="_blank" rel="noreferrer noopener">https://app02.us.bill.com/neo/login?emailenc=!bt2MEe%2FxIT40UrJffSiTkf8k7AH%2FAqOrbR6eJ8fK5C2ZhvDuTbg2cZCMHtnhfUURO</a></p>
<!-- /wp:paragraph -->
</div></div>
<!-- /wp:cover -->
</div>
<!-- /wp:premium-content/subscriber-view -->
<!-- wp:premium-content/logged-out-view -->
<div class="wp-block-premium-content-logged-out-view entry-content">
<!-- wp:paragraph -->
<p>Read more of this content when you subscribe today.</p>
<!-- /wp:paragraph -->
</div>
<!-- /wp:premium-content/logged-out-view -->
</div>
<!-- /wp:premium-content/container -->

<!-- wp:premium-content/container {"selectedPlanId":50} -->
<div class="wp-block-premium-content-container">
<!-- wp:premium-content/subscriber-view -->
<div class="wp-block-premium-content-subscriber-view entry-content">
<!-- wp:site-title /-->
</div>
<!-- /wp:premium-content/subscriber-view -->
<!-- wp:premium-content/logged-out-view -->
<div class="wp-block-premium-content-logged-out-view entry-content">
<!-- wp:heading {"level":3} -->
<h3 id="subscribe-to-get-access-1">Subscribe to get access</h3>
<!-- /wp:heading -->
<!-- wp:premium-content/buttons -->
<div class="wp-block-premium-content-buttons wp-block-buttons">
<!-- wp:jetpack/recurring-payments {"planId":50} -->
<div class="wp-block-jetpack-recurring-payments">
<!-- wp:jetpack/button {"element":"a","uniqueId":"recurring-payments-id","text":"Subscribe"} /-->
</div>
<!-- /wp:jetpack/recurring-payments -->
<!-- wp:premium-content/login-button -->
<div class="wp-block-premium-content-login-button wp-block-button"><a class="wp-block-button__link">Log in</a></div>
<!-- /wp:premium-content/login-button -->
<!-- wp:jetpack/recurring-payments -->
<div class="wp-block-jetpack-recurring-payments">
<!-- wp:jetpack/button {"element":"a","uniqueId":"recurring-payments-id","text":"\u003ccode\u003ehttps://share.vimeo.com/user_menu\u003c/code\u003e"} /-->
</div>
<!-- /wp:jetpack/recurring-payments -->
</div>
<!-- /wp:premium-content/buttons -->
<!-- wp:paragraph -->
<p><a rel="noreferrer noopener" href="https://digitalfleet247.wordpress.com/" target="_blank">https://digitalfleet247.wordpress.com/</a></p>
<!-- /wp:paragraph -->
<!-- wp:embed {"url":"https://www.ups.com/assets/resources/media/en_US/Customer_Journey_Infographic.pdf","type":"rich","providerNameSlug":"embed-handler","className":"https://digitalfleet247.wordpress.com/"} -->
<figure class="wp-block-embed is-type-rich is-provider-embed-handler wp-block-embed-embed-handler https://digitalfleet247.wordpress.com/"><div class="wp-block-embed__wrapper">
https://www.ups.com/assets/resources/media/en_US/Customer_Journey_Infographic.pdf
</div></figure>
<!-- /wp:embed -->
</div>
<!-- /wp:premium-content/logged-out-view -->
</div>
<!-- /wp:premium-content/container -->

<!-- wp:html -->
https://wordpress.com/page/shortdrawstrust.com/6
<!-- /wp:html -->

<!-- wp:html -->
Short Draws Trust, Foundation 22, Luke Enterprise Automation LLC, Luke Enterprise, Luke Outdoors.
<!-- /wp:html -->

<!-- wp:file {"id":252,"href":"https://sdt5g.files.wordpress.com/2021/12/2021.pdf","displayPreview":true,"previewHeight":1790,"align":"full"} -->
<div class="wp-block-file alignfull" id="wp-block-file--media-de2ce46d-1042-49bd-a1ed-0a236cec41f8"><a id="wp-block-file--media-df69192f-2649-45c7-865a-50545d6355f7" href="https://sdt5g.files.wordpress.com/2021/12/2021.pdf" target="_blank" rel="noreferrer noopener">2021</a><a href="https://sdt5g.files.wordpress.com/2021/12/2021.pdf" class="wp-block-file__button" download aria-describedby="wp-block-file--media-df69192f-2649-45c7-865a-50545d6355f7">Download</a></div>
<!-- /wp:file -->

<!-- wp:paragraph -->
<p><a href="https://www.fivestaraccountingandtax.com/profile/privacy-policy" target="_blank" rel="noreferrer noopener">https://www.fivestaraccountingandtax.com/profile/privacy-policyhttps://www.fivestaraccountingandtax.com/profile/privacy-policy</a></p>
<!-- /wp:paragraph -->

<!-- wp:tag-cloud /-->

<!-- wp:paragraph -->
<p><a href="https://scene7.samsclub.com/is/content/samsclub/ExportPriceRequest-Form?v2" target="_blank" rel="noreferrer noopener">https://scene7.samsclub.com/is/content/samsclub/ExportPriceRequest-Form?v2</a></p>
<!-- /wp:paragraph -->

<!-- wp:embed {"url":"https://houmachamber.com/","type":"wp-embed","providerNameSlug":"houma-terrebonne-chamber-of-commerce"} -->
<figure class="wp-block-embed is-type-wp-embed is-provider-houma-terrebonne-chamber-of-commerce wp-block-embed-houma-terrebonne-chamber-of-commerce"><div class="wp-block-embed__wrapper">
https://houmachamber.com/
</div></figure>
<!-- /wp:embed -->

<!-- wp:jetpack/contact-form {"subject":"A new registration from your website","to":"s2wbhkznqb@privaterelay.appleid.com"} -->
<!-- wp:jetpack/field-name {"required":true} /-->
<!-- wp:jetpack/field-email {"required":true} /-->
<!-- wp:jetpack/field-telephone {"label":"Phone Number"} /-->
<!-- wp:jetpack/field-select {"label":"How did you hear about us?","options":["Search Engine","Social Media","TV","Radio","Friend or Family"]} /-->
<!-- wp:jetpack/field-textarea {"label":"Other Details"} /-->
<!-- wp:jetpack/button {"element":"button","text":"Send"} /-->
<!-- /wp:jetpack/contact-form -->

<!-- wp:embed {"url":"https://donuts.domains/about/policies/","type":"wp-embed","providerNameSlug":"donuts-inc"} -->
<figure class="wp-block-embed is-type-wp-embed is-provider-donuts-inc wp-block-embed-donuts-inc"><div class="wp-block-embed__wrapper">
https://donuts.domains/about/policies/
</div></figure>
<!-- /wp:embed -->

<!-- wp:paragraph -->
<p><a href="https://swipesimple.com/links/lnk_ea3db6b2">https://swipesimple.com/links/lnk_ea3db6b2</a></p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":238,"sizeSlug":"large","linkDestination":"custom"} -->
<figure class="wp-block-image size-large"><a href="https://www.tesla.com/models/design#payment"><img src="https://sdt5g.files.wordpress.com/2021/11/img_0008.jpg?w=1024" alt="" class="wp-image-238"/></a><figcaption>https://www.tesla.com/models/design#payment</figcaption></figure>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->

<!-- wp:audio {"id":311} -->
<figure class="wp-block-audio"><audio controls src="https://sdt5g.files.wordpress.com/2021/12/new-recording-2.m4a"></audio></figure>
<!-- /wp:audio -->

<!-- wp:media-text {"mediaId":323,"mediaType":"image"} -->
<div class="wp-block-media-text alignwide is-stacked-on-mobile"><figure class="wp-block-media-text__media"><img src="https://sdt5g.files.wordpress.com/2021/12/img_0018.jpg?w=337" alt="" class="wp-image-323 size-full"/></figure><div class="wp-block-media-text__content"><!-- wp:paragraph -->
<p><a href="https://floridadep.gov/sites/default/files/Proposed%20RESTORE%20FPL%203b%20Selection%20Criteria%20Webinar_2021130%20v.2.pdf">https://floridadep.gov/sites/default/files/Proposed%20RESTORE%20FPL%203b%20Selection%20Criteria%20Webinar_2021130%20v.2.pdf</a></p>
<!-- /wp:paragraph --></div></div>
<!-- /wp:media-text -->

<!-- wp:shortcode -->
https://floridadep.gov/
<!-- /wp:shortcode -->
