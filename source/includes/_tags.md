# Tags

It is possible to view a list of the tags in your Shiptheory account as well as view, add, update and delete individual tags.

<!-- Add Tags -->
## Add Tags

```php
$data = json_encode(
  [
    "name" => "Tag A",
    "background_colour" => "#000",
    "text_colour" => "#FFF",
  ]
);

$ch = curl_init('https://api.shiptheory.com/v1/tags/1');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Accept: application/json',
    'Content-Type: application/json',
    'Authorization: Bearer AasLK190ALhaLDSj1nal92Ja...',
    'Content-Length: ' . strlen($data)
]);

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
const request = require('request');
const options = {
  url: 'https://api.shiptheory.com/v1/tags/1',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiOiJKV1Q...'
  },
  form: {
    id: 1,
    name: "Tag A",
    background_colour: "#000",
    text_colour: "#FFF"
  }
};

request.post(options, (err, httpResponse, body) => {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});

> A successful request will return JSON structured like this:

```json
{
  "id": 1,
  "name": "Tag A",
  "background_colour": "#FFF",
  "text_colour": "#000"
}
```

> An unsuccessful request will return JSON structured in one of these formats:

```json
{
  "error": {
    "sku": {
      "unique": "A tag with this name already exists"
    }
  }
}

// or

{
  "message": "You do not have access to the requested resource",
  "url": "/v1/tags/5",
  "code": 403
}
```


Add a single tag, with a unique name, to your Shiptheory account.


### HTTP Request

`POST https://api.shiptheory.com/v1/tags`

### Parameters

Parameter         | Required | Description
----------------- | -------- | -------------------------------------------------------------
name              | yes      | Tag name. Max 50 characters. Must be unique to your account.
background_colour | no       | Background colour of the tag. Must be a valid hex colour.
text_colour       | no       | Text colour of the tag. Must be a valid hex colour.


<!-- View Tag -->
## View Tag

```php
$ch = curl_init('https://api.shiptheory.com/v1/tags/1');

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
]);

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
const request = require('request');
const options = {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
};

request.get('https://api.shiptheory.com/v1/tags/1', options, (err, httpResponse, body) => {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "id": 1,
  "name": "Tag A",
  "background_colour": "#000",
  "text_colour": "#FFF"
}
```
> An unsuccessful request will return JSON structured like this:

```json
{
  "message": "You do not have access to the requested resource",
  "url": "/v1/tags/1",
  "code": 403
}
```

Get a single tag by its ID.


### HTTP Request

`GET https://api.shiptheory.com/v1/tags/<Tag ID>`


<!-- List Tags -->
## List Tags

```php
$ch = curl_init('https://api.shiptheory.com/v1/tags');

curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
]);

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
const request = require('request');
const options = {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
};

request.get('https://api.shiptheory.com/v1/tags', options, (err, httpResponse, body) => {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns JSON structured like this:

```json
{
  "tags": [
    {
      "id": 1,
      "name": "Tag A",
      "background_colour": "#000",
      "text_colour": "#FFF"
    },
    {
      "id": 2,
      "name": "Tag B",
      "background_colour": "#FA8072",
      "text_colour": "#708090"
    }
  ],
  "pagination": {
    "page": 1,
    "pages": 1,
    "results": 2,
    "results_per_page": 25,
    "limit": false
  }
}
```


Returns a list of the products in your account.

### HTTP Request

`GET https://api.shiptheory.com/v1/tags`


<aside class="notice">
This method supports paging. See [How to use paging](https://shiptheory.com/developer/index.html#pagination).
</aside>

The maximum limit of results per page for this call is 100. The limit can be passed in the URL as `?limit=25`, for example.

The following URL parameters can be used to `sort` the data:

### Sorting Parameters

Field             | Description
----------------- | -----------------------------
name              | Tag name
background_colour | Background colour of the tag
text_colour       | Text colour of the tag


<!-- Update Tag -->
## Update Tag

```php
$data = json_encode(
  [
    "name" => "Tag A",
    "background_colour" => "#000",
    "text_colour" => "#FFF",
  ]
);

$ch = curl_init('https://api.shiptheory.com/v1/tags/1');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Accept: application/json',
    'Content-Type: application/json',
    'Authorization: Bearer AasLK190ALhaLDSj1nal92Ja...',
    'Content-Length: ' . strlen($data)
]);

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
const request = require('request');
const options = {
  url: 'https://api.shiptheory.com/v1/tags/1',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiOiJKV1Q...'
  },
  form: {
    id: 1,
    name: "Tag A",
    background_colour: "#000",
    text_colour: "#FFF"
  }
};

request.put(options, (err, httpResponse, body) => {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> A successful request will return JSON structured like this:

```json
{
  "id": 1,
  "name": "Tag A",
  "background_colour": "#000",
  "text_colour": "#FFF"
}
```

> An unsuccessful request will return JSON structured in one of these formats:

```json
{
  "error": {
    "sku": {
      "unique": "A tag with this name already exists"
    }
  }
}

// or

{
  "message": "You do not have access to the requested resource",
  "url": "/v1/tags/5",
  "code": 403
}
```


Update single tag by ID.


### HTTP Request

`PUT|PATCH https://api.shiptheory.com/v1/tags/<Tag ID>`

### Parameters

Parameter         | Required | Description
----------------- | -------- | -------------------------------------------------------------
name              | yes      | Tag name. Max 50 characters. Must be unique to your account.
background_colour | no       | Background colour of the tag. Must be a valid hex colour.
text_colour       | no       | Text colour of the tag. Must be a valid hex colour.


<!-- Delete Tag -->
## Delete Tag

```php
$ch = curl_init('https://api.shiptheory.com/v1/tags/1');

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Authorization: bearer asd1AdiJKV1QiLCJhbGciOiJIUzI1NiJ9.dafMTasdsa..',
    'Accept: application/json'
]);

$result = curl_exec($ch);
curl_close($ch);

echo $result;
```

```javascript
// make sure you have installed the request module (npm install request)
const request = require('request');
const options = {
  headers: {
    'Accept': 'application/json'
  },
  auth: {
    'bearer': 'eyJ0eXAiO....'
  }
};

request.del('https://api.shiptheory.com/v1/tags/1', options, (err, httpResponse, body) => {
  if (err) {
    return console.error('Request Failed:', err);
  }
  console.log(body);
});
```

> The above code returns an empty body on 200.

> An unsuccessful request will return JSON structured like this:

```json
{
  "message": "You do not have access to the requested resource",
  "url": "/v1/tags/1",
  "code": 403
}
```


Delete a single tag by its ID.


### HTTP Request

`DELETE https://api.shiptheory.com/v1/tags/<Tag ID>`
