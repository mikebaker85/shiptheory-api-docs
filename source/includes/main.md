# Introduction

Shiptheory provides a REST API that allows you to communicate directly with all of our [supported carriers](https://shiptheory.com/#integrations). Currently the following methods are available.

This documentation provides information on how to integrate with Shiptheory from other applications and covers creating and searching shipments, printing shipping labels and return labels as well as returning lists of available delivery services and package sizes.

## Testing with Postman and Insomnia

We recommend using a REST client during development. Two of our favourites are [Insomnia](https://insomnia.rest/) and [Postman](https://www.postman.com/).

## Partner Tag

If you are developing an application that will be used by more than 1 company, or an application that you intend to distribute in anyway, you must include the `Shiptheory-Partner-Tag` http request header in **all** of your requests.

```php
[
    'Accept: application/json',
    'Content-Type: application/json',
    'Content-Length: ' . strlen($data)),
    'Shiptheory-Partner-Tag: ExamplePartner1'
];
```

```javascript
  headers: {
    'Accept': 'application/json',
    'Shiptheory-Partner-Tag': 'ExamplePartner1'
  },
```

Please contact Shiptheory support to obtain a partner tag. There is no charge for this, tags are used to provide better support to customers and partners.

## Pagination

Some API methods support pagination. A blue notice will show on the document for those that do. In the response data returned by methods that will support pagination, you will see pagination data as per the code panel to your right and described in the table below.

Field            | Description
---------------- | -------
page             | Current page number
pages            | Total number of pages with results
results          | Total results across all pages
results_per_page | Results per page
limit            | Limit of results to return

Methods that support pagination can be `sorted`, `limited` and `paged` by passing URL parameters.

For example: `https://api.shiptheory.com/v1/packages/sizes?sort=id&direction=desc&page=1&limit=10` will return page 1 of the first 10 package sizes sorted on the *id* with the biggest first. Each method has different sorting options and limitations, see the documentation on each method.

### Pagination Parameters

Parameter        | Description
---------------- | -------
sort             | Field to sort data by. See method documentation for supported fields
direction        | Ascending (asc) or Descending (desc)
page             | Page number to return. 404 means you have specified a page number without results
limit            | Limit of results to return

```json
{
  ...
  "pagination": {
    "page": 1, //current page
    "pages": 3, //total number of pages with results
    "results": 75, //total results across all pages
    "results_per_page": 25, //results per page
    "limit": 100 //limit
  }
}
```



# Authentication

> To obtain an authentication token:

```php
$data = json_encode(
    array(
        "email" => "you@youremail.com",
        "password" => "password"
    )
);

$ch = curl_init('https://api.shiptheory.com/v1/token');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Accept: application/json',
    'Content-Type: application/json',
    'Content-Length: ' . strlen($data))
);

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

var options = {
  url: 'https://api.shiptheory.com/v1/token',
  headers: {
    'Accept': 'application/json'
  },
  form: {
      email: 'you@youremail.com',
      password: 'password'
  }
};

request.post(options, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```
> The above code returns JSON structured like this:

```json
{
   "success":true,
   "data":{
      "token":"asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..."
   }
}
```

All requests to Shiptheory must contain an authentication bearer token. Use your regular [Shiptheory account](https://shiptheory.com/login) username and password to request a token.

The authentication bearer token must be included in the header of all subsequent requests as per below.

`Authorization: Bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..`

To request an authentication bearer token, make a POST request, providing your Shiptheory email and password.

Make sure that the authentication request (and all subsequent requests) provide `application/json` under the `Accept` and `Content-Type` Headers.

### HTTP Request

`POST https://api.shiptheory.com/v1/token`

### Parameters

Parameter | Required | Description
--------- | -------  | -----------
email     | Yes      | The email address you use to login to Shiptheory
password  | Yes      | The password you use to login to Shiptheory

<aside class="notice">
Authentication tokens <code>expire after 60 minutes</code>.
</aside>

# Shipment

## Book

```php
$data = json_encode(
    array(
        "reference" => "S1234",
        "reference2" => "ORDER1001",
        "shipment_detail" => array(
            "weight" => 2.5,
            "parcels" => 1,
            "value" => 135.18,
            "shipping_price" => 3.99,
            "reference3" => "ORDERREF3",
            "sales_source" => "eBay",
            "ship_date": "2021-04-20",
            "rules_metadata": "custom string",
            "duty_tax_number": "IM123456789",
            "duty_tax_number_type": "IOSS"
        ),
        "recipient" => array(
            "company" => "Beard Supplies Co",
            "firstname" => "William",
            "lastname" => "Riker",
            "address_line_1" => "123 Southpaw Lane",
            "city" => "Bristol",
            "postcode" => "bs2 3ap",
            "telephone": "01171231234",
            "country" => "GB",
            "tax_number" => "GB123456"
        ),
        "sender" => array(
            "company" => "Hair Wholesaler Co.",
            "firstname" => "Julian",
            "lastname" => "Bashir",
            "address_line_1" => "65 Horfield Retail Park",
            "city" => "Bath",
            "postcode" => "ba1 2jw",
            "telephone": "0117123111",
            "country" => "GB"
        ),
        "products" => array(
            array(
                "name" => "USS Beard Trimmer 500",
                "sku" => "ussbeard500",
                "qty" => 1,
                "value" => 35.20,
                "weight" => 0.5
            ),
            array(
                "name" => "Earl Grey Gift Set",
                "sku" => "earlgrey",
                "qty" => 2,
                "value" => 49.99,
                "weight" => 1
            )
        ),
        "packages" => array(
            array(
                "id" => 100001,
                "weight" => 10.50
            ),
            array(
                "id" => 100001,
                "weight" => 10
            )
        )
    )
);

$ch = curl_init('https://api.shiptheory.com/v1/shipments');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Accept: application/json',
    'Content-Type: application/json',
    'Authorization: Bearer AasLK190ALhaLDSj1nal92Ja...',
    'Content-Length: ' . strlen($data))
);

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

var options = {
  url: 'https://api.shiptheory.com/v1/shipments',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiOiJKV1Q...'
  },
  form: {
      reference: 'S1234',
      reference2: 'ORDER1001',
      shipment_detail: {
          weight: 2.5,
          parcels: 1,
          value: 135.18,
          shipping_price: 3.99,
          reference3: 'ORDERREF3',
          sales_source: 'eBay',
          ship_date: '2021-04-20',
          rules_metadata: 'custom string',
          duty_tax_number: 'IM123456789',
          duty_tax_number_type: 'IOSS'
      },
      recipient: {
          company: 'Beard Supplies Co',
          firstname: 'William',
          lastname: 'Riker',
          address_line_1: '123 Southpaw Lane',
          city: 'Bristol',
          postcode: 'BS2 3AP',
          telephone: '01161231245',
          country: 'GB',
          tax_number: 'GB123456'
      },
      sender: {
          company: 'Hair Wholersaler Co.',
          firstname: 'Julian',
          lastname: 'Bashir',
          address_line_1: '65 Horfield Retail Park',
          city: 'Bath',
          postcode: 'BA1 2JW',
          telephone: '01161231211',
          country: 'GB'
      },
      products: [{
          name: 'USS Beard Trimmer 500',
          sku: 'ussbeard500',
          qty: 1,
          value: 32.50,
          weight: 0.5
      },
      {
          name: 'Earl Grey Gift Set',
          sku: 'earlgrey',
          qty: 2,
          value: 49.99,
          weight: 1
      }],
      packages: [{
          id: 10001,
          weight: 100
      },
      {
          id: 10002,
          weight: 10.39
      }]
    }
};

request.post(options, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "success": true,
  "carrier_result": {
    "status": "success",
    "tracking": "2887123123",
    "carrier": "DHL",
    "no_tracking": null
  },
  "message": "DHL tracking reference received: 2887123123"
}
```

This endpoint makes it possible for you to add a shipment to Shiptheory and book it with a carrier.

### HTTP Request

`POST https://api.shiptheory.com/v1/shipments`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
reference | Yes | Your unique reference for this shipment. We use this when a carrier needs a unique identifier for your shipment, if this is longer than 25 characters the shipment may be rejected
reference2 | Yes | A second non-unique reference
delivery_service | No | The Shiptheory delivery service ID. See Delivery Services. If this is not provided or you pass a delivery service that does not exist, the shipment will be subject to any shipping rules setup on your account
increment | No | Only supported alongside `delivery_service`, used when an account has multiple instances of the same carrier. See <a href="https://support.shiptheory.com/support/solutions/articles/24000060014-finding-your-carrier-increment" target="_blank">Finding Your Carrier Increment</a>
| |
**shipment_detail** | |
weight | Yes | The weight of the shipment in kilograms, to three decimal places
parcels | Yes | The number of boxes or labels as a full number
value | Yes | The monetary shipment cost to the customer
shipping_price | No | The price the end customer paid for shipping
reference3 | No | A third reference
enhancement_id | No | Carrier enhancement, only used when passing a delivery_service. See GET Services for IDs
format_id | No | Carrier format, only used when passing a delivery_service. See GET Services for IDs
instructions | No | Delivery instructions
gift_message | No | Gift message
channel_shipservice_name | No | A description of the shipping option chosen by the customer at order time
currency_code | No | Specify the currency that this order has been paid in, must be a 3 letter currency code
sales_source | No | Where the sale originated, eBay, Amazon, Shopify, Etsy, BigCommerce, OnBuy.
ship_date | No | YYYY-MM-DD Important: To understand how this field is used, please refer to <a href="https://support.shiptheory.com/support/solutions/articles/24000067542-ship-date-usage-with-the-shiptheory-api" target="_blank">ship_date Usage</a>.
rules_metadata | No | Custom free text field, max 50 characters. Useful to map shipping rules.
duty_tax_number | No | Duty Tax number
duty_tax_number_type | No | Duty Tax number type, one of (ABN, IOSS, IRD, OSS, VOEC)
| |
**recipient** | |
company | No | The recipient company name
first name | Yes | First name of the receiver
last name | Yes | Last name of the receiver
address_line_1 | Yes | Delivery address line 1
address_line_2 | No | Delivery address line 2
address_line_3 | No | Delivery address line 3
city | Yes | Delivery city
county | No | Delivery county or state
country | Yes | 2 character delivery country ISO code
postcode | Yes | The delivery postal or zip code
telephone | No | Delivery contact telephone number
mobile | No | Delivery contact mobile number
email | No | Delivery contact email address
tax_number | No | Tax number of the customer
| |
**tax_numbers** | No | Optional array of recipient tax numbers
tax_number | Cond | Recipient tax number value. Required if tax_number_type is specified
tax_number_type | Cond | "VAT" or "EORI". Required if tax_number is specified
| |
| |
**sender** | | If not sent, defaults to your default shipping location
company | No | The sender company name
first name | Cond. | First name of the sender
last name | Cond. | Last name of the sender
address_line_1 | Yes | Sender address line 1
address_line_2 | No | Sender address line 2
address_line_3 | No | Sender address line 3
city | Cond. | Sender city
county | No | Sender county or state
country | Cond. | 2 character sender country ISO code
postcode | Cond. | The sender postal or zip code
telephone | No | Sender contact telephone number
mobile | No | Sender contact mobile number
email | No | Sender contact email address
tax_number | No | Tax number of the sender
| |
**products** | |
name | Yes | Name of the product being sent
sku | Yes | SKU of the product being sent
qty | No | Quantity of the product being sent
value | Yes | The price of the product to the customer
weight | Yes | The weight of the product in kilograms, to three decimal places
commodity_code  | No | UN Commodity Code, used for shipping internationally
commodity_description | No | Commodity Description, used for shipping internationally
commodity_manucountry | No | Country of Manufacture, used for shipping internationally
| |
**packages** | | Optional. See [Using Package Sizes](https://support.shiptheory.com/support/solutions/articles/24000026829-using-package-sizes)
id | Cond. | The ID of the Package. See [Package Sizes](https://shiptheory.com/developer/index.html#packages)
weight | Cond. | Weight of the package, in Kg, to two decimal places

<aside class="notice">
If you do not send a Senders address, the default shipping location from your Shiptheory account will be used.
</aside>

<aside class="success">
Product data allows Shiptheory to provide greater Shipping Rule accuracy
</aside>

## View Shipment

```php
$reference = 12345;

$ch = curl_init('https://api.shiptheory.com/v1/shipments/'.$reference);

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

request.get('https://api.shiptheory.com/v1/shipments/S1234', {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiOiJKV1Q...'
  }
}, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "shipment": {
    "channel_reference_id": "S1234",
    "channel_reference_id_2": "ORDER1001",
    "created": "2016-04-27T20:42:57+0000",
    "modified": "2016-04-27T20:44:03+0000",
    "status": "Complete",
    "tracking_number": "TT324201212GB",
    "carrier": "Royal Mail",
    "channel_reference_id_3": "ORDERREF3",
    "duty_tax_number": "IM123456789",
    "duty_tax_number_type": "IOSS"
  },
  "messages": [
    {
      "message": "Shipment added over API by test@testweb.com (82.45.13.184)",
      "created": "2016-04-27T20:42:57+0000",
      "type": "info"
    },
    {
      "message": "To be dispatched with DHL's Domestic Economy",
      "created": "2016-04-27T20:42:57+0000",
      "type": "info"
    },
    {
      "message": "DHL tracking reference received: 2887435806",
      "created": "2016-04-27T14:43:06+0000",
      "type": "success"
    },
    {
      "message": "Printed & Archived by test@testweb.com (API)",
      "created": "2016-04-27T20:47:59+0000",
      "type": "success"
    }
  ],
  "label": "JVBERi0xLjQKMyAwIG9iago8PC9UeXBlIC9QYWdlCi9QYXJlbnQgMSAwIFIKL01lZG5ID..."
}
```

This endpoint retrieves the status, created and last modified datetime, tracking number and label data (where available) of an existing shipment.

<aside class="notice">Shipping label PDFs are returned as a <code>base64 encoded string</code>.</aside>

### HTTP Request

`GET https://api.shiptheory.com/v1/shipments/<REFERENCE>`

### URL Parameters

Parameter | Description
--------- | -----------
REFERENCE | The unique reference used when creating the shipment. This is usually the Shipment ID or Order ID.

### Response Parameters

Refer to the Errors section of the documentation for HTTP Error Codes used. Successful GET requests will result in any combination of the following parameters.

Parameter | Description
--------- | -----------
**shipment** | |
channel_reference_id | The unique reference used when creating the shipment. This is usually the Shipment ID or Order ID.
channel_reference_id_2 | A second non-unique reference
channel_reference_id_3 | A third non-unique reference
created  | The datetime the shipment was created
modified | The last datetime the shipment was updated
status | The status of the shipment now. <a href="http://support.shiptheory.com/support/solutions/articles/12400-shiptheory-statuses-explained" target="_blank">See statuses</a>
label | Shipping label, where available as a base64 encoded PDF
| |
**messages** | |
message | Activity reported by Shiptheory
created | Datetime of activity
type | Type of activity. Error, Info or Success


## List Shipments

```php
/*
 * The below example query lists for all Royal Mail shipments
 * created from BigCommerce on the 14th of November 2020
 */
$query = "created=2020-11-14&Couriers.couriername=Royal Mail&channel_name=Bigcommerce";

$ch = curl_init('https://api.shiptheory.com/v1/shipments/list?'.$query);

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

> The above code returns JSON structured like this:

```json
{
    "shipments": [
        {
            "created": "2019-09-20T13:39:54+0000",
            "modified": "2019-09-20T13:39:54+0000",
            "channel_reference_id": "ST0000069",
            "channel_reference_id_2": "ST0000064",
            "channel_name": "Manual",
            "status": "Failed",
            "courier": {
                "couriername": "Parcelforce"
            },
            "courierservice": {
                "id": 24,
                "serviceName": "express48"
            },
            "shipment_detail": {
                "tracking_number": null,
                "weight": 0.5,
                "grams": 500,
                "gift_message": "Happy birthday!",
                "instructions": "Please leave behind the gate",
                "shipping_price": 3.99,
                "value": 1,
                "parcels": 1,
                "width": null,
                "height": null,
                "depth": null,
        "channel_reference_id_3": "ORDERREF3",
        "ship_date": "2021-04-25T00:00:00+0000"
            },
            "delivery_address": {
                "company": "Shiptheory",
                "address_line_1": "20, Berkeley House",
                "address_line_2": "Charlotte Street",
                "address_line_3": null,
                "city": "Bristol",
                "county": "Somerset",
                "postcode": "BA1 2WA",
                "telephone": "0776666666",
                "mobile": "",
                "email": "test@test1.com",
                "firstname": "Peter",
                "lastname": "Pan",
                "tax_number": null,
                "country": {
                    "country": "United Kingdom",
                    "code": "GBR"
                }
            },
            "latest_public_reference": null
        },
        {
            "created": "2019-09-20T13:39:51+0000",
            "modified": "2019-09-20T13:39:51+0000",
            "channel_reference_id": "ST0000068",
            "channel_reference_id_2": "ST0000064",
            "channel_name": "Magento",
            "status": "Failed",
            "courier": {
                "couriername": "FedEx"
            },
            "courierservice": null,
            "shipment_detail": {
                "tracking_number": null,
                "weight": 0.5,
                "grams": 500,
                "shipping_price": null,
                "gift_message": "Happy birthday!",
                "instructions": "Please leave behind the gate",
                "value": 1,
                "parcels": 1,
                "width": null,
                "height": null,
                "depth": null,
                    "channel_reference_id_3": "ORDERREF6",
                 "ship_date": "2021-04-25T00:00:00+0000"
            },
            "delivery_address": {
                "company": "Shiptheory",
                "address_line_1": "20, Berkeley House",
                "address_line_2": "Charlotte Street",
                "address_line_3": null,
                "city": "Bristol",
                "county": "Somerset",
                "postcode": "BA1 2WA",
                "telephone": "0776666666",
                "mobile": "",
                "email": "test@test1.com",
                "firstname": "Peter",
                "lastname": "Pan",
                "tax_number": null,
                "country": {
                    "country": "United Kingdom",
                    "code": "GBR"
                }
            },
            "latest_public_reference": null
        },
        {
            "created": "2019-09-20T13:38:23+0000",
            "modified": "2019-09-20T13:38:23+0000",
            "channel_reference_id": "ST0000063",
            "channel_reference_id_2": "ST0000063",
            "channel_name": "TradeGecko",
            "status": "Failed",
            "courier": null,
            "courierservice": null,
            "shipment_detail": {
                "tracking_number": null,
                "weight": 0,
                "value": 1,
                "grams": 500,
                "gift_message": "Happy birthday!",
                "instructions": "Please leave behind the gate",
                "shipping_price": null,
                "parcels": 1,
                "width": null,
                "height": null,
                "depth": null,
                    "channel_reference_id_3": "ORDERREF8",
                 "ship_date": "2021-04-25T00:00:00+0000"
            },
            "delivery_address": {
                "company": "Shiptheory",
                "address_line_1": "20, Berkeley House",
                "address_line_2": "Charlotte Street",
                "address_line_3": null,
                "city": "Bristol",
                "county": "Somerset",
                "postcode": "BA1 2WA",
                "telephone": "0776666666",
                "mobile": "",
                "email": "test@test1.com",
                "firstname": "Peter",
                "lastname": "Pan",
                "tax_number": null,
                "country": {
                    "country": "United Kingdom",
                    "code": "GBR"
                }
            },
            "latest_public_reference": null
        }
    ],
  "pagination": {
    "page": 1,
    "pages": 1,
    "results": 3,
    "results_per_page": 100,
    "limit": 100
  }
}
```

This endpoints makes it possible to search shipments in a Shiptheory account by specifying a number of GET parameters, documented below.

### HTTP Request

`GET https://api.shiptheory.com/v1/shipments/list`

### Parameters

Parameter | Required | Description
--------- | -------- | -----------
created | No | YYYY-MM-DD. The shipment created date (the date the shipment was received by Shiptheory)
modified | No | YYYY-MM-DD. The shipment last modified date (the date the shipment was last updated by Shiptheory).
status | No | Options: *'Failed', 'Complete', 'Ready', 'Ignored', 'Not Ready', 'Print Error', 'Waiting', 'PROCESSING'*. Case sensitive. <a href="http://support.shiptheory.com/support/solutions/articles/12400-shiptheory-statuses-explained" target="_blank">Shiptheory Statuses Explained</a>.
channel_name | No | The name of the sales channel in Shiptheory. For example: *Magento*.
channel_reference_id | No | Unique reference from the sales channel used when creating the shipment. This is usually the Shipment ID
channel_reference_id_2 | No | A second sales channel reference. Usually Order ID
ShipmentDetails.parcels | No | The number of *boxes* on a shipment
ShipmentDetails.weight | No | The weight of the shipment
ShipmentDetails.value | No | The monetary value of the shipment
ShipmentDetails.duty_tax_number | No | Duty Tax number of a shipment
ShipmentDetails.duty_tax_number_type | No | Duty Tax Number Type, one of ABN, IOSS, IRD, OSS, VOEC
Couriers.couriername | No | Case sensitive carrier name. You can get valid carrier names from the <A href="https://shiptheory.com/developer/index.html#outgoing-services" target="_blank">GET Services Method</a>
Countries.code | No | 3 Character country ISO code
DeliveryAddress.address_line_1 | No | Delivery Address Line 1
DeliveryAddress.address_line_2 | No | Delivery Address Line 2
DeliveryAddress.address_line_3 | No | Delivery Address Line 3
DeliveryAddress.city | No | Delivery City
DeliveryAddress.county | No | Delivery County/State
DeliveryAddress.postcode | No | Delivery Postcode/Zipcode
DeliveryAddress.telephone | No | Delivery Telephone Number
DeliveryAddress.email | No | Delivery Email Address
DeliveryAddress.company | No | Delivery Company Name
DeliveryAddress.firstname | No | Delivery First Name
DeliveryAddress.lastname | No | Delivery Surname
DeliveryAddress.tax_number | No | Tax number, or EORI number of the customer

<aside class="notice">
This method supports paging. See [How to use paging](https://shiptheory.com/developer/index.html#pagination).
</aside>

The maximum limit of results per page for this call is 100. The limit can be passed in the URL as `?limit=25`, for example.

The following URL parameters can be used to `sort` the data:

### Sorting Parameters

Field          | Description
-------------- | -------
created        | The date the shipment was first received into Shiptheory
modified       | The last date the shipment was modified in Shiptheory



## Search Shipments

```php
/*
 * The below example query searches for all shipments with a delivery postcode of "ba1 2wa"
 * created in September 2019, with a status of Failed
 */
$query = "created_from=2019-09-01&created_to=2019-09-30&DeliveryAddress.postcode=ba1 2wa&status=Failed&include_products=1";

$ch = curl_init('https://api.shiptheory.com/v1/shipments/search?'.$query);

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

> The above code returns JSON structured like this:

```json
{
    "shipments": [
        {
            "created": "2019-09-20T13:39:54+0000",
            "modified": "2019-09-20T13:39:54+0000",
            "channel_reference_id": "ST0000069",
            "channel_reference_id_2": "ST0000064",
            "channel_name": "Manual",
            "status": "Failed",
            "courier": {
                "couriername": "Parcelforce"
            },
            "courierservice": {
                "id": 24,
                "serviceName": "express48"
            },
            "shipment_detail": {
                "tracking_number": null,
                "weight": 0.5,
                "value": 1,
                "grams": 500,
                "shipping_price": 3.99,
                "gift_message": "Happy birthday!",
                "instructions": "Please leave behind the gate",
                "parcels": 1,
                "width": null,
                "height": null,
                "depth": null,
                    "channel_reference_id_3": "ORDERREF3",
                 "ship_date": "2021-04-25T00:00:00+0000"
            },
            "delivery_address": {
                "company": "Shiptheory",
                "address_line_1": "20, Berkeley House",
                "address_line_2": "Charlotte Street",
                "address_line_3": null,
                "city": "Bristol",
                "county": "Somerset",
                "postcode": "BA1 2WA",
                "telephone": "0776666666",
                "mobile": "",
                "email": "test@test1.com",
                "firstname": "Peter",
                "lastname": "Pan",
                "tax_number": null,
                "country": {
                    "country": "United Kingdom",
                    "code": "GBR"
                }
            },
            "products": [
                {
                    "qty": 2,
                    "sku": "20grammer",
                    "name": "20 gram product",
                    "value": "1.00",
                    "catalog_price": "1.00",
                    "barcode": null,
                    "weight": "0.02",
                    "grams": "200",
                    "length": "25",
                    "width": "25",
                    "height": "25",
                    "commodity_description": null,
                    "commodity_code": null,
                    "commodity_manucountry": null,
                    "commodity_composition": null
                },
                {
                    "qty": 20,
                    "sku": "cap1",
                    "name": "Baseball Cap",
                    "value": "4.00",
                    "weight": "11.00",
                    "length": "0",
                    "width": "0",
                    "height": "0",
                    "commodity_description": null,
                    "commodity_code": null,
                    "commodity_manucountry": null,
                    "commodity_composition": null
                }
            ],
            "latest_public_reference": null
        },
        {
            "created": "2019-09-20T13:39:51+0000",
            "modified": "2019-09-20T13:39:51+0000",
            "channel_reference_id": "ST0000068",
            "channel_reference_id_2": "ST0000064",
            "channel_name": "Magento",
            "status": "Failed",
            "courier": {
                "couriername": "FedEx"
            },
            "courierservice": null,
            "shipment_detail": {
                "tracking_number": null,
                "weight": 0.5,
                "value": 1,
                "grams": 500,
                "shipping_price": null,
                "gift_message": "Happy birthday!",
                "instructions": "Please leave behind the gate",
                "parcels": 1,
                "width": null,
                "height": null,
                "depth": null,
                    "channel_reference_id_3": "ORDERREF5",
                 "ship_date": "2021-04-25T00:00:00+0000"
            },
            "delivery_address": {
                "company": "Shiptheory",
                "address_line_1": "20, Berkeley House",
                "address_line_2": "Charlotte Street",
                "address_line_3": null,
                "city": "Bristol",
                "county": "Somerset",
                "postcode": "BA1 2WA",
                "telephone": "0776666666",
                "mobile": "",
                "email": "test@test1.com",
                "firstname": "Peter",
                "lastname": "Pan",
                "tax_number": null,
                "country": {
                    "country": "United Kingdom",
                    "code": "GBR"
                }
            },
            "products": [
                {
                    "qty": 2,
                    "sku": "20grammer",
                    "name": "20 gram product",
                    "value": "1.00",
                    "catalog_price": "1.00",
                    "barcode": null,
                    "weight": "0.02",
                    "grams": "200",
                    "length": "25",
                    "width": "25",
                    "height": "25",
                    "commodity_description": null,
                    "commodity_code": null,
                    "commodity_manucountry": null,
                    "commodity_composition": null
                },
                {
                    "qty": 20,
                    "sku": "cap1",
                    "name": "Baseball Cap",
                    "value": "4.00",
                    "weight": "11.00",
                    "length": "0",
                    "width": "0",
                    "height": "0",
                    "commodity_description": null,
                    "commodity_code": null,
                    "commodity_manucountry": null,
                    "commodity_composition": null
                }
            ],
            "latest_public_reference": null
        },
        {
            "created": "2019-09-20T13:38:23+0000",
            "modified": "2019-09-20T13:38:23+0000",
            "channel_reference_id": "ST0000063",
            "channel_reference_id_2": "ST0000063",
            "channel_name": "TradeGecko",
            "status": "Failed",
            "courier": null,
            "courierservice": null,
            "shipment_detail": {
                "tracking_number": null,
                "weight": 0,
                "value": 1,
                "grams": 500,
                "shipping_price": 10,
                "gift_message": "Happy birthday!",
                "instructions": "Please leave behind the gate",
                "parcels": 1,
                "width": null,
                "height": null,
                "depth": null,
                    "channel_reference_id_3": "ORDERREF8",
                 "ship_date": "2021-04-25T00:00:00+0000"
            },
            "delivery_address": {
                "company": "Shiptheory",
                "address_line_1": "20, Berkeley House",
                "address_line_2": "Charlotte Street",
                "address_line_3": null,
                "city": "Bristol",
                "county": "Somerset",
                "postcode": "BA1 2WA",
                "telephone": "0776666666",
                "mobile": "",
                "email": "test@test1.com",
                "firstname": "Peter",
                "lastname": "Pan",
                "tax_number": null,
                "country": {
                    "country": "United Kingdom",
                    "code": "GBR"
                }
            },
            "products": [],
            "latest_public_reference": null
        }
    ]
}
```

This endpoints makes it possible to search shipments in a Shiptheory account by specifying a number of GET parameters, documented below.

### HTTP Request

`GET https://api.shiptheory.com/v1/shipments/search`

### Parameters

Parameter | Required | Description
--------- | -------- | -----------
created_from | Yes | YYYY-MM-DD. Date range start date. This is the shipment created date (the date the shipment was received by Shiptheory)
created_to | Yes | YYYY-MM-DD. Date range end date. This is the shipment created date (the date the shipment was received by Shiptheory). Max date range is 31 days
limit | No | How many results to return? Allowed range is 1-100 shipments. Defaults to 25
include_products | No | If set to "1", shipment product data will be attached to the response
status | No | Options: *'Failed', 'Complete', 'Ready', 'Ignored', 'Not Ready', 'Print Error', 'Waiting', 'PROCESSING'*. Case sensitive. <a href="http://support.shiptheory.com/support/solutions/articles/12400-shiptheory-statuses-explained" target="_blank">Shiptheory Statuses Explained</a>.
channel_name | No | The name of the sales channel in Shiptheory. For example: *Magento*.
channel_reference_id | No | Unique reference from the sales channel used when creating the shipment. This is usually the Shipment ID
channel_reference_id_2 | No | A second sales channel reference. Usually Order ID
ShipmentDetails.parcels | No | The number of *boxes* on a shipment
ShipmentDetails.weight | No | The weight of the shipment
ShipmentDetails.value | No | The monetary value of the shipment
ShipmentDetails.duty_tax_number | No | Duty Tax number of a shipment
ShipmentDetails.duty_tax_number_type | No | Duty Tax Number Type, one of ABN, IOSS, IRD, OSS, VOEC
Couriers.couriername | No | Case sensitive carrier name. You can get valid carrier names from the <A href="https://shiptheory.com/developer/index.html#outgoing-services" target="_blank">GET Services Method</a>
Countries.code | No | 3 Character country ISO code
DeliveryAddress.address_line_1 | No | Delivery Address Line 1
DeliveryAddress.address_line_2 | No | Delivery Address Line 2
DeliveryAddress.address_line_3 | No | Delivery Address Line 3
DeliveryAddress.city | No | Delivery City
DeliveryAddress.county | No | Delivery County/State
DeliveryAddress.postcode | No | Delivery Postcode/Zipcode
DeliveryAddress.telephone | No | Delivery Telephone Number
DeliveryAddress.email | No | Delivery Email Address
DeliveryAddress.company | No | Delivery Company Name
DeliveryAddress.firstname | No | Delivery First Name
DeliveryAddress.lastname | No | Delivery Surname
DeliveryAddress.tax_number | No | Tax number or EORI number of customer


# Return Labels

Currently, limited carriers support Returns within Shiptheory.

See our list of <a href="http://support.shiptheory.com/support/solutions/articles/24000045115" target="_blank">Supported Returns Carriers</a> for more information.

## Automatic Shipping Rules

If you would like to generate Returns Labels automatically to put into your outgoing parcels, you can add return services to your shipping rules.

![alt text](/images/rules_return_example.png)

Once you have set this, not passing the `delivery_service` parameter when making a `book` request and subsequent requests to `Get Details` will include the Returns Labels specified in your shipping rule within the PDF `label` binary string.

## Creating a Return Label

This method allows you to create a return label from an existing shipment. All of the address, order and product data will be taken automatically from the existing shipment.

To make this call, you need to know the `Reference` and outgoing `Postcode` on the original shipment.

A label and return tracking number will be returned, or an error message. While messages and errors are stored in the shipment history, the label will not be stored against the shipment, so you should save the label returned from the API. Subsequent calls to create a return label will produce a new label.

Please note: Any other data sent with this request will be ignored. If you need to change shipment data on the return label, you should create a Headless Return label.

```php
$data = json_encode(
    array(
        "outgoing_reference" => "S0001001",
        "delivery_postcode" => "BS1 4TW",
        "return_service" => 200,
        "expiry" => "2019-10-23"
    )
);

$ch = curl_init('https://api.shiptheory.com/v1/returns');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Accept: application/json',
    'Content-Type: application/json',
    'Authorization: Bearer AasLK190ALhaLDSj1nal92Ja...',
    'Content-Length: ' . strlen($data))
);

$result = curl_exec($ch);
curl_close($ch);

echo $result;

```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

var options = {
  url: 'https://api.shiptheory.com/v1/returns',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiOiJKV1Q...'
  },
  form: {
      outgoing_reference: 'S0001001',
      delivery_postcode: 'BS1 4TW',
      return_service: '200',
      expiry: '2019-10-23'
    }
};

request.post(options, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> A successful request will return JSON structured like this:

```json
{
  "tracking": "ST123456789X",
  "label": "JVBERi0xLjQKMyAwIG9iago8PC9UeXBlIC9QYWdlCi9QYXJlbnQgMSAwIFIKL01lZG5ID..."
}
```

> An unsuccessful request will return JSON structured like this:

```json
{
   "error": {
      "expiry": {
         "expiry_length": "Your returns label expiry is not allowed. The label must expire within 30 days"
         }
      }
}
```

> Or

```
{
   "error": "Unable to find a shipment with the reference 'ST0000040' and postcode 'bs1 x2w'"
}
```

### HTTP Request

`POST https://api.shiptheory.com/v1/returns`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
outgoing_reference | Yes | The unique reference on the shipment. See <a href="#book">Book Shipment</a>.
delivery_postcode | Yes | A original outgoing postcode/zip code on the shipment
return_service | Yes | The return service `id`. See <a href="#incoming-services-returns-">GET Incoming Services</a>.
expiry | Yes | YYYY-MM-DD. The date the returns label expires and can no longer be used. Maximum 30 days into the future.

## Headless Returns

If you would like to create returns labels that are not attached to any existing shipment in Shiptheory, please contact support.


# Delivery Services

## Outgoing Services

```php
$ch = curl_init('https://api.shiptheory.com/v1/services');

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

request.get('https://api.shiptheory.com/v1/services', {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
}, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "delivery_services": {
    "DPD": {
      "Parcel - Homecall": {
        "id": "37"
      },
      "Parcel - Two Day": {
        "id": "38"
      },
      "Parcel - Next Day": {
        "id": "39"
      }
     }
   }
}
```


Performing a GET on `/services` returns a list of outgoing delivery services available on your Shiptheory account.

When booking a shipment, if you pass a ``delivery_service`` your shipment will skip shipping rules and book the shipment with the carrier that owns the specified delivery service.

The ``delivery_service`` field relates directly to the ``id`` of the Delivery Service in Shiptheory. You can get a list of the Delivery Services available to your Shiptheory account by calling the Services API endpoint.

### HTTP Request

`GET https://api.shiptheory.com/v1/services`

## Incoming Services (Returns)


```php
$ch = curl_init('https://api.shiptheory.com/v1/services/incoming');

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

request.get('https://api.shiptheory.com/v1/services/incoming', {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
}, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
    "return_services": {
        "Royal Mail": [
            {
                "name": "Tracked 48 Returns",
                "id": 100
            },
            {
                "name": "Tracked 24 Returns",
                "id": 200
            }
        ],
        "InPost": [
            {
                "name": "InPost Medium Locker Return",
                "id": 300
            }
        ]
    }
}
```


Performing a GET on `/services/incoming` returns a list of incoming (return) delivery services available on your Shiptheory account.

The format of incoming services looks a little different to that of outgoing services.

The `id` field is what you need to pass in the `return_service` field when making Return Label POST request.

### HTTP Request

`GET https://api.shiptheory.com/v1/services/incoming`

# Packages

Some carriers, typically pallet, freight and international carriers, support specifying multiple differing package sizes when creating a shipment. For those carriers that support it, it is possible to specify the package size in the [`Book Shipment` POST request](https://shiptheory.com/developer/index.html#book).

## Sizes

```php
$ch = curl_init('https://api.shiptheory.com/v1/packages/sizes');

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

request.get('https://api.shiptheory.com/v1/packages/sizes', {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
}, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "package_sizes": [
    {
      "id": 11110,
      "name": "Quarter Pallet",
      "length": 1,
      "width": 1,
      "height": 1,
      "active": true,
      "created": "2016-05-18T14:05:04+0000",
      "modified": "2016-05-18T14:05:04+0000"
    },
    {
      "id": 11120,
      "name": "Small Box",
      "length": 2,
      "width": 2,
      "height": 1,
      "active": true,
      "created": "2017-09-12T09:37:37+0000",
      "modified": "2017-09-12T09:37:37+0000"
    }
  ],
  "pagination": {
    "page": 1,
    "pages": 1,
    "results": 2,
    "results_per_page": 2,
    "limit": 25
  }
}
```



Performing a GET on `/packages/sizes` returns a list of the package sizes setup on your Shiptheory account. For more information on how to use package sizes, or if your package size API response is empty, please refer ti our guide on [Using Package Sizes](https://support.shiptheory.com/support/solutions/articles/24000026829-using-package-sizes).

The `id` returned from this call can be used to specify the package size in the `Book Shipment` POST request, [documented above](https://shiptheory.com/developer/index.html#book).

### HTTP Request

`GET https://api.shiptheory.com/v1/packages/sizes`


<aside class="notice">
This method supports paging. See [How to use paging](https://shiptheory.com/developer/index.html#pagination).
</aside>

The maximum limit of results per page for this call is 100. The limit can be passed in the URL as `?limit=25`, for example.

The following URL parameters can be used to `sort` the data:

### Sorting Parameters

Field          | Description
-------------- | -------
id             | The ID of the package size, to use in the Book Shipment request
name           | Name given in the UI to the package size
length         | Length specified in the UI
width          | Width specified in the UI
height         | Height specified in the UI
active         | Enabled or disabled in the UI



# Products

It is possible to view a list of the products in your Shiptheory account as well as view, update and add individual products.

<aside class="warning">
When adding a shipment via the `Book Shipment` POST request [documented above](https://shiptheory.com/developer/index.html#book), any product data that is different (except the SKU), will be updated in your catalogue. The same happens if you download orders from a third party sales channel, such as Magento, or BigCommerce; when product data is seen by Shiptheory that differs from what is on file, the latest data is used, overwriting existing data.
</aside>

## Add Product


```php
$data = json_encode(
    array(
        "sku" => "hat1",
        "name" => "Ladies Wedding Hat",
        "price" => 45,
        "weight" => 3.50,
        "commodity_code" => "123456",
        "commodity_description" => "Ladies hat 90% cotton 20% mixed",
        "commodity_manucountry" => "GBR",
        "commodity_composition" => "Cotton",
        "barcode" => "ABC123123"
    )
);

$ch = curl_init('https://api.shiptheory.com/v1/products');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Accept: application/json',
    'Content-Type: application/json',
    'Authorization: Bearer AasLK190ALhaLDSj1nal92Ja...',
    'Content-Length: ' . strlen($data))
);

$result = curl_exec($ch);
curl_close($ch);

echo $result;

```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

var options = {
  url: 'https://api.shiptheory.com/v1/products',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiOiJKV1Q...'
  },
  form: {
      sku: 'hat1',
      name: 'Ladies Wedding Hat',
      price: 45,
      weight: 3.50',
      commodity_code: '123456',
      commodity_description: 'Ladies hat 90% cotton 20% mixed',
      commodity_manucountry: 'GBR',
      commodity_composition: 'Cotton',
      barcode: 'ABC123123',
    }
};

request.post(options, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> A successful request will return JSON structured like this:

```json
{
  "success": true,
  "product": {
    "sku": "hat1",
    "name": "Ladies Wedding Hat",
    "price": 45,
    "weight": 3.50,
    "barcode": "ABC123123",
    "commodity_code": "123456",
    "commodity_description": "Ladies hat 90% cotton 20% mixed",
    "commodity_manucountry": "GBR",
    "commodity_composition": "Cotton",
    "created": "2020-11-23T14:05:02+0000",
    "modified": "2020-11-23T14:05:02+0000"
  }
}
```

> An unsuccessful request will return JSON structured like this:

```json
{
  "error": {
    "sku": {
      "unique": "A product with this SKU already exists"
    }
  }
}
```


Add a single product, with a unique SKU, to your Shiptheory account.


### HTTP Request

`POST https://api.shiptheory.com/v1/products`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
sku | Yes | Your unique product SKU. Max 100 characters
name | No | Product name. Max 255 characters
price | No | Price of the product. Between 0 and 9999999.99
weight | No | Weight of the product in Kg. Between 0 and 9999999.99
barcode | No | Product barcode. Max 15 characters
commodity_code | No | Commodity HS Tariff code. Max 100 characters
commodity_description | No | Commodity HS Tariff code. Max 100 characters
commodity_manucountry | No | Commodity country of manufacture. Max 100 characters
commodity_composition | No | Commodity composition. Max 100 characters
length | No | Product length. Between 0 and 9999999.99
width | No | Product width. Between 0 and 9999999.99
height | No | Product height. Between 0 and 9999999.99

## View Product

```php
$ch = curl_init('https://api.shiptheory.com/v1/products/view/hat1');

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

request.get('https://api.shiptheory.com/v1/products/view/hat1', {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
}, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "product": {
    "sku": "hat1",
    "name": "Wedding Hat",
    "price": 41,
    "weight": 4,
    "barcode": "",
    "commodity_code": "123456",
    "commodity_description": "Ladies hat 90% cotton 20% mixed",
    "commodity_manucountry": "GBR",
    "commodity_composition": "Cotton",
    "length": 10,
    "width": 10,
    "height": 5,
    "created": "2020-11-23T00:00:00+0000",
    "modified": "2020-11-23T00:00:00+0000"
  }
}
```


Returns a single product based on the SKU.


### HTTP Request

`GET https://api.shiptheory.com/v1/products/view/<SKU>`



## List Products

```php
$ch = curl_init('https://api.shiptheory.com/v1/products');

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
));

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

request.get('https://api.shiptheory.com/v1/products', {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
}, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "products": [
    {
      "sku": "basecap1",
      "name": "Baseball Cap",
      "price": 4,
      "weight": 1,
      "barcode": "",
      "commodity_code": "123456",
      "commodity_description": "Sports cap",
      "commodity_manucountry": "GBR",
      "commodity_composition": "Cotton",
      "length": 0,
      "width": 0,
      "height": 0,
      "created": "2020-11-23T00:00:00+0000",
      "modified": "2020-11-23T00:00:00+0000"
    },
    {
      "sku": "hat1",
      "name": "Ladies Wedding Hat",
      "price": 10,
      "weight": 3.50,
      "barcode": "",
      "commodity_code": "123457",
      "commodity_description": "Ladies hat 90% cotton 20% mixed",
      "commodity_manucountry": "GBR",
      "commodity_composition": "Cotton",
      "length": 0,
      "width": 0,
      "height": 0,
      "created": "2020-11-23T00:00:00+0000",
      "modified": "2020-11-23T00:00:00+0000"
    }
  ],
  "pagination": {
    "page": 1,
    "pages": 1,
    "results": 2,
    "results_per_page": 2,
    "limit": 25
  }
}
```


Returns a list of the products in your account.

### HTTP Request

`GET https://api.shiptheory.com/v1/products`


<aside class="notice">
This method supports paging. See [How to use paging](https://shiptheory.com/developer/index.html#pagination).
</aside>

The maximum limit of results per page for this call is 100. The limit can be passed in the URL as `?limit=25`, for example.

The following URL parameters can be used to `sort` the data:

### Sorting Parameters

Field          | Description
-------------- | -------
sku            | The SKU of the product size
name           | Name given in to the product
price          | Price
weight         | Weight
created        | Date the product was first seen by Shiptheory
modified       | Date the product was last updated in Shiptheory


## Update Product


```php
$data = json_encode(
    array(
        "sku" => "hat1",
        "name" => "Ladies Wedding Hat",
        "price" => 45,
        "weight" => 3.50,
        "commodity_code" => "123456",
        "commodity_description" => "Ladies hat 90% cotton 20% mixed",
        "commodity_manucountry" => "GBR",
        "commodity_composition" => "Cotton",
        "barcode" => "ABC123123"
    )
);

$ch = curl_init('https://api.shiptheory.com/v1/products/update/hat1');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    'Accept: application/json',
    'Content-Type: application/json',
    'Authorization: Bearer AasLK190ALhaLDSj1nal92Ja...',
    'Content-Length: ' . strlen($data))
);

$result = curl_exec($ch);
curl_close($ch);

echo $result;

```

```javascript
// make sure you have installed the request module (npm install request)
var request = require('request');

var options = {
  url: 'https://api.shiptheory.com/v1/products/update/hat1',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiOiJKV1Q...'
  },
  form: {
      sku: 'hat1',
      name: 'Ladies Wedding Hat',
      price: 45,
      weight: 3.50',
      commodity_code: '123456',
      commodity_description: 'Ladies hat 90% cotton 20% mixed',
      commodity_manucountry: 'GBR',
      commodity_composition: 'Cotton',
      barcode: 'ABC123123',
    }
};

request.post(options, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> A successful request will return JSON structured like this:

```json
{
  "success": true,
  "product": {
    "sku": "hat1",
    "name": "Ladies Wedding Hat",
    "price": 45,
    "weight": 3.50,
    "barcode": "ABC123123",
    "commodity_code": "123456",
    "commodity_description": "Ladies hat 90% cotton 20% mixed",
    "commodity_manucountry": "GBR",
    "commodity_composition": "Cotton",
    "created": "2020-11-23T14:05:02+0000",
    "modified": "2020-11-23T14:05:02+0000"
  }
}
```

> An unsuccessful request will return JSON structured like this:

```json
{
  "error": {
    "sku": {
      "unique": "A product with this SKU already exists"
    }
  }
}
```


Update single product by SKU.


### HTTP Request

`PUT https://api.shiptheory.com/v1/products/update/<SKU>`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
sku | Yes | Your unique product SKU. Max 100 characters
name | No | Product name. Max 255 characters
price | No | Price of the product. Between 0 and 9999999.99
weight | No | Weight of the product in Kg. Between 0 and 9999999.99
barcode | No | Product barcode. Max 15 characters
commodity_code | No | Commodity HS Tariff code. Max 100 characters
commodity_description | No | Commodity HS Tariff code. Max 100 characters
commodity_manucountry | No | Commodity country of manufacture. Max 100 characters
commodity_composition | No | Commodity composition. Max 100 characters
length | No | Product length. Between 0 and 9999999.99
width | No | Product width. Between 0 and 9999999.99
height | No | Product height. Between 0 and 9999999.99
