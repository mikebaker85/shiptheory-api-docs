# Errors

Calls to the ``book`` endpoint will result in various validation errors, returned as JSON when data sent does not validate.

In addition to validation errors, the following HTTP Codes will be returned.

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request sucks
401 | Unauthorized -- Your API username, password or API token is invalid
403 | Forbidden -- The resource requested is not available to you
404 | Not Found -- The specified shipment could not be found
405 | Method Not Allowed -- You tried to access a shipment with an invalid method
406 | Not Acceptable -- You requested a format that isn't json or xml
429 | Too Many Requests -- You're requesting too many shipments! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarially offline for maintanance. Please try again later.
