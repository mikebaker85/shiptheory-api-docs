# Introduction

Shiptheory provodes a REST API that allows you to communicate directly with all of our [supported carriers](https://shiptheory.com/#integrations). Currently the following methods are available.

* Book Shipment

Book a shipment over the API using Shiptheory's automatic shipping rules or specify a specific carrier directly.

* Get Shipping Labels

Get any shipping labels attached to an existing shipment.

* Get Tracking Number

Get the tracking number of an existing shipment.

* Get Services

List all Carriers, delivery services, formats and enhancements available on your Shiptheory account.

## Testing with Postman

Postman is a great app that allows you to send make HTTP requests and is useful when developing API integrations.

We have published a collection of Shiptheory API test calls on Postman that may save you some time during development.

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/12b536df2bbb90822086)

# Authentication

> To obtain an authentication token:

```php
$data = json_encode(
    array(
        "email" => "you@youremail.com",
        "password" => "password"
    )
);

$ch = curl_init('https://shiptheory.com/api/token');

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
// make sure you have installed the reques module (npm install request)
var request = require('request');

var options = {
  url: 'https://shiptheory.com/api/token',
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

<aside class="notice">
Authentication tokens <code>expire after 10 minutes</code>.
</aside>

# Shipment

## Book

```php
$data = json_encode(
    array(
        "reference" => "1001",
        "reference2" => "ORDER1001",
        "shipment_detail" => array(
            "weight" => 1.25,
            "parcels" => 1,
            "value" => 150
        ),
        "recipient" => array(
            "company" => "Beard Supplies Co",
            "firstname" => "William",
            "lastname" => "Riker",
            "address_line_1" => "123 Southpaw Lane",
            "city" => "Bristol",
            "postcode" => "bs2 3ap",
            "telephone": "01171231234",
            "country" => "GB"
        ),
        "products" => array(
            array(
                "name" => "USS Beard Trimmer 500",
                "sku" => "ussbeard500",
                "value" => 35.20,
                "weight" => 25.00
            ),
            array(
                "name" => "Earl Grey Gift Set",
                "sku" => "earlgrey",
                "value" => 49.99,
                "weight" => 10.00
            )            
        )
    )
);

$ch = curl_init('https://shiptheory.com/api/shipments');

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

`POST https://shiptheory.com/api/shipments`

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
reference | Yes | Your unique reference for this shipment
reference2 | Yes | A second non-unique reference
delivery_service | No | The Shiptheory delivery service id. See Delivery Services. If this is not provided or you pass a delivery service that does not exist, the shipment will be subject to any shipping rules setup on your account
shipment_detail | |
weight | Yes | The weight of the shipment in Kilograms to two decimal places
parcels | Yes | The number of boxes or labels as a full number
value | Yes | The monetary shipment cost to the customer
enhancement_id | No | Carrier enhancement, only used when passing a delivery_service. See GET Services for ID's
format_id | No | Carrier format, only used when passing a delivery_service. See GET Services for ID's
instructions | No | Delivery instructions
channel_shipservice_name | No | A description of the shipping option chosen by the customer at order time
recipient | |
company | No | The recipient company name
first name | Yes | First name of the receiver
last name | Yes | The last name of the receiver
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
products | |
name | Yes | Name of the product being sent
sku | Yes | SKU of the product being sent
value | Yes | The price of the product to the customer
weight | Yes | The weight of the product in Kg, 2 decimal places
commodity_code  | No | UN Commodity Code, used for shipping internationally
commodity_description | No | Commodity Description, used for shipping internationally
commodity_manucountry | No | Country of Manufacture, used for shipping internationally

<aside class="success">
Product data allows Shiptheory to provide greater Shipping Rule accuracy
</aside>

## Get Details

```php
$reference = 12345;

$ch = curl_init('https://shiptheory.com/api/shipments/'.$reference);

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
  "shipment": {
    "channel_reference_id": "1001",
    "channel_reference_id_2": "ORDER1001",
    "created": "2016-04-27T20:42:57+0000",
    "modified": "2016-04-27T20:44:03+0000",
    "status": "Complete"
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

<aside class="notice">Shipping label PDF's are returned as a <code>base64 encoded string</code>.</aside>

### HTTP Request

`GET https://shiptheory.com/api/shipments/<REFERENCE>`

### URL Parameters

Parameter | Description
--------- | -----------
REFERENCE | The unique reference used when creating the shipment. This is usually the Shipment ID or Order ID.

### Response Parameters

Refer to the Errors section of the documentation for HTTP Error Codes used. Successful GET requests will result in any combination of the following parameters.

Parameter | Description
--------- | -----------
shipment | |
channel_reference_id | The unique reference used when creating the shipment. This is usually the Shipment ID or Order ID.
channel_reference_id | A second non-unique reference
created  | The datetime the shipment was created
modified | The last datetime the shipment was updated
status | The status of the shipment now. <a href="http://support.shiptheory.com/support/solutions/articles/12400-shiptheory-statuses-explained" target="_blank">See statuses</a>
label | Shipping label, where available as a base64 encoded PDF 
messages | |
message | Activity reported by Shiptheory
created | Datetime of activity
type | Type of activity. Error, Info or Success

# Delivery Services

```php
$ch = curl_init('https://shiptheory.com/api/services');

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

request.get('https://shiptheory.com/api/services', {
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

When booking a shipment, if you pass a ``delivery_service`` your shipment will skip shipping rules and book the shipment with the carrier that owns the specified delivery service.

The ``delivery_service`` field relates directly to the ``id`` of the Delivery Service in Shiptheory. You can get a list of the Delivery Services available to your Shiptheory account by calling the Services API endpoint.

### HTTP Request

`GET https://shiptheory.com/api/services`
