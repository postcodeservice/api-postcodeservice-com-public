# Postcode Service API 6.1.0 Release

This is a major version release of the Postcode Service API containing new endpoints, features and
some UX/UC improvements.

## 🇳🇱 Netherlands

- Added support for the latest Netherlands V6 API. This API is faster and more accurate than the
  previous V5 API. The Netherlands V5 API is still supported, but will be deprecated in the future.
- Added geo precision to the Netherlands V6 API, which returns the geo precision of the address
  result. This can be used to determine the accuracy of the address result. The values can be
  approximate, geometric_center, rooftop, range_interpolated or unknown. See the documentation for
  more information: https://developers.postcodeservice.com/#netherlands-api

```text
"geo_precision": "rooftop",
"latitude": 52.39267104872,
"longitude": 4.8465930696013,
```

- Added address function information, which returns the function of the address result. The values
  can be accommodation, detention, education, healthcare, industry, multipurpose, office,
  residential, shop, sports, storage or unknown. See the documentation for more
  information see the response fields results
  array: https://developers.postcodeservice.com/#netherlands-api

```text
"address_function": [
  "office"
],
```

- Added postal label information, which returns the official accepted and preferred formatted of the
  postal label of the address result. For example:

```text
"postal_address": {
                "line_1": "Kabelweg 21L",
                "line_2": "1014BA AMSTERDAM",
                "line_3": "Netherlands"
                  }
```

- Added Postbus indicator, which returns a boolean value indicating if the address is a Postbus
  address. See the documentation for more
  information: https://developers.postcodeservice.com/#netherlands-api

```text
"is_postbus": false,
``` 

- Added Wadden Eilanden indicator, which returns a boolean value indicating if the address is on
  one of the Wadden Eilanden. See the documentation for more
  information: https://developers.postcodeservice.com/#netherlands-api
- This can be used to calulate a surplus shipping costs or extra delivery day for example.

```text
"is_on_wadden_islands": false,
```

- Added Municipality information, which returns the municipality of the address result. See the
  documentation for more information: https://developers.postcodeservice.com/#netherlands-api

```text
"municipality": "Amsterdam",
```

- Added house letter validation, set by a boolean value. By default, the address is validated (based
  on the input parameters) except
  for the house letter. When set to true, the house letter will also be
  validated on official Government records. See the documentation for more
  information: https://developers.postcodeservice.com/#netherlands-api.
  Be careful when using this parameter, because it is prone to user error. For example: B and Bis,
  or I and 1 are not considered the same house letter. Also, user unofficial additions like 'hoek'
  or 'achter' will result in an 'address does not exist' response.

Example with house letter validation:

```text
https://api.postcodeservice.com/nl/v6/address-validation?zipcode=1014BA&house_number=21&house_letter=L&house_letter_validation=1&only_status=0&page=1&page_limit=10
```

- The response data will contain a house_letter_validation field with a boolean value to make clear
  whether the house letter was validated or not.

```text
"house_letter_validation": true,
```

- Added only status parameter (only_status), set by a boolean value. When set to true, the response
  will only be a HTTP status code (204) or (418). Status 204 is returned when the address is found,
  418 when the
  address does not exist. This makes it possible to check if an address exists without having to
  parse the response data.

For example:

```text
https://api.postcodeservice.com/nl/v6/address-validation?zipcode=1014BA&house_number=21&house_letter=L&house_letter_validation=0&only_status=1&page=1&page_limit=10
```

### New address-validation endpoint

- This endpoint is a combination of the address-suggestions and address-streetid endpoints. It
  returns address details based on a zipcode, house number and house letter. This endpoint is
  faster and more accurate than the previous 'find' endpoint. In addition, the only_status parameter
  is added to this endpoint, which makes it possible to check if an address exists without having to
  parse the response data. Furthermore, the house_letter_validation parameter is added to this
  endpoint. This parameter is set by a boolean value. When set to true, the house letter will be
  validated.
- The response data will contain the supplied paramaters for easy reference. See the documentation
  response fields for more
  information: https://developers.postcodeservice.com/#netherlands-api-GETnl-v6-address-validation

Older V5 address validation response (deprecated, but still supported):

https://developers.postcodeservice.com/deprecated/#deprecated-netherlands-api-GETnl-v5-find

```json
{
  "street": "Kabelweg",
  "city": "Amsterdam",
  "region": "Noord-Holland",
  "residential": "no",
  "latitude": 52.39267104872,
  "longitude": 4.8465930696013
}
```

New V6 address validation response:

https://developers.postcodeservice.com/#netherlands-api-GETnl-v6-address-validation

```json
{
  "results": [
    {
      "street": "Kabelweg",
      "street_language": "nl",
      "street_id": "st_81586623457798",
      "house_number": 21,
      "house_letter": "L",
      "house_letter_validated": false,
      "zipcode": "1014BA",
      "city": "Amsterdam",
      "municipality": "Amsterdam",
      "province": "Noord-Holland",
      "is_po_box": false,
      "is_on_wadden_islands": false,
      "address_function": [
        "office"
      ],
      "geo_precision": "rooftop",
      "latitude": 52.39267104872,
      "longitude": 4.8465930696013,
      "postal_address": {
        "line_1": "Kabelweg 21L",
        "line_2": "1014BA AMSTERDAM",
        "line_3": "Netherlands"
      }
    }
  ],
  "error_code": null,
  "errors": [],
  "pagination": {
    "current_page": 1,
    "results_per_page": 10,
    "is_last_page": true
  }
}
```

### New address-suggestions endpoint

- Added new endpoint 'address-suggestions' to the Netherlands V6 API, which returns a list of
  addresses based on a part of the streetname. This endpoint is faster and more accurate than the
  previous 'find' endpoint.

```text
V6 address suggestions:
https://api.postcodeservice.com/nl/v6/address-suggestions?street=Kab&city=Amsterdam&page=1&page_limit=10
```

### New address-streetid endpoint

- Added new endpoint 'address-streetid' to the Netherlands V6 API, which returns details of an
  address based on a streetid. This endpoint can, but not required, used in combination with the '
  address-suggestions' endpoint to give a complete autocomplete experience to end-users.

```text
V6 address suggestions:
https://api.postcodeservice.com/nl/v6/address-streetid?street_id=st_81586623457798&house_number=21&house_letter=L&house_letter_validation=0&only_status=0&page=1&page_limit=10
```

### New address-streetcity endpoint

- Added new endpoint 'address-streetcity' to the Netherlands V6 API, which returns details of an
  address based on a streetname and city. This endpoint can be used to do a reversed look-up when a
  user has the streetname, house number and city already.

> Note that some Dutch addresses, like Beatrixstraat 1 in Rossum, may return multiple results due to
> identical street and identical city names. To validate a unique address, use the
> address-validation
> endpoint, as each zipcode with a house number is unique in the Netherlands. See the
> documentation https://developers.postcodeservice.com/#netherlands-api-GETnl-v6-address-streetcity.
> This endpoint is more accurate because of the multiple results feature in edge cases as the above
> than other reversed
> look-up services on the market.

### Changed zipcode-location endpoint

- The zipcode-location endpoint is expanded with geo precision, province and 'is on Wadden Island'
  flag. Also the search zipcode is returned in the results array. See documenation
  at https://developers.postcodeservice.com/#netherlands-api-GETnl-v6-zipcode-location

Old V5:

```json 
{
  "latitude": 52.395421328766,
  "longitude": 4.8463096822221
}
```

New V6:

```json
{
  "results": [
    {
      "zipcode": "1014BA",
      "province": "Noord-Holland",
      "is_on_wadden_islands": false,
      "geo_precision": "geometric_center",
      "latitude": 52.395421328766,
      "longitude": 4.8463096822221
    }
  ],
  "error_code": null,
  "errors": [],
  "pagination": {
    "current_page": 1,
    "results_per_page": 10,
    "is_last_page": true
  }
}
```

## 🇧🇪 Belgium

Added V4 support for Belgium. The V3 API is still supported, but will be deprecated in the future.
Version 4 introduces the new uniform API structure and new features like pagination, geo precision,
house letter validation and only status. See the documentation for more information:
https://developers.postcodeservice.com/#belgium-api-GETbe-v4-address-validation

In particular, the Belgium Postal address label is added to the response data including the for
Belgium specific `bus` field besides the house letter field.

```text
"postal_address": {
"line_1": "Aalststraat 2A bus 10",
"line_2": "9000 OUDENAARDE",
"line_3": "Belgium"
}
```

In addition, the following new endpoints are added:

### New address-validation endpoint

This endpoint is a combination of the address-suggestions and address-streetid endpoints. It
returns address details based on a zipcode, house number and house letter. This endpoint is
faster and more accurate than the previous 'find' endpoint. In addition, the only_status parameter
is added to this endpoint, which makes it possible to check if an address exists without having to
parse the response data. Furthermore, the house_letter_validation parameter is added to this
endpoint. This parameter is set by a boolean value. When set to true, the house letter will be
validated.

### New address-suggestions endpoint

Added new endpoint 'address-suggestions' to the Belgium V4 API, which returns a list of addresses
based on a part of the streetname. This endpoint is faster and more accurate than the previous '
find' endpoint.

### New address-streetid endpoint

Added new endpoint 'address-streetid' to the Belgium V4 API, which returns details of an address
based on the street_id. This endpoint can, but not required, used in combination with the '
address-suggestions' endpoint to give a complete autocomplete experience to end-users.

### Removal of the deprecated Belgium V2 'find' endpoint

⚠ The Belgium V2 'find' endpoint is not supported in the new API v3 and replaced with the new
Belgium V4
address-suggestions endpoint as the functions were are almost similar. Please update your code when
switching to the new API.

### New underlying abbreviations search and support

Both the `address-validation` and `address-suggestions` endpoints have automatic abbreviations
search. Meaning that if the endpoints are called with streetnames that have officially supported
abbrevations, for example 'St.' for 'Sint', the search takes this into account in the address
verification
and address suggestions list. This is a new feature in the Belgium API.

## 🇩🇪 Germany

Added V2 support for Germany. The V1 API is still supported, but will be deprecated in the future.

Version 2 introduces the new uniform API structure and new features like pagination, geo precision,
house letter validation and only status in line with the Netherlands and Belgium endpoints.

### New address-validation endpoint

This endpoint is a combination of the address-suggestions and address-streetid endpoints. It returns
address details based on a zipcode, house number and house letter.
See the documentation for more information:
https://developers.postcodeservice.com/#germany-api-GETde-v2-address-validation

### New address-suggestions endpoint

Added new endpoint 'address-suggestions' to the Germany V2 API, which returns a list of addresses  
based on a part of the streetname. This endpoint can be used with the address-streetid endpoint to
quickly find a complete address based on a part of the streetname, giving the user a complete
autocomplete experience.
See the documentation for more information:
https://developers.postcodeservice.com/#germany-api-GETde-v2-address-suggestions

### New address-streetid endpoint

Added new endpoint 'address-streetid' to the Germany V2 API, which returns details of an address
based on the street_id.
This endpoint can, but not required, used in combination with the 'address-suggestions' endpoint to
give a complete autocomplete experience to end-users.
See the documentation for more information:
https://developers.postcodeservice.com/#germany-api-GETde-v2-address-streetid

### Changes to the existing zipcode-find endpoint

The zipcode-find endpoint is expanded with geo precision field.

Old German V1:

```json
{
  "zipcode": "10115",
  "city": "Berlin"
}
```

New German V2:

```json
{
  "results": [
    {
      "zipcode": "10115",
      "city": "Berlin",
      "geo_precision": "geometric_center",
      "latitude": 52.5323,
      "longitude": 13.3846
    }
  ],
  "error_code": null,
  "errors": [],
  "pagination": {
    "current_page": 1,
    "results_per_page": 10,
    "is_last_page": true
  }
}
```

### postcode-find is removed

This endpoint is removed as the new zipcode-find has similar functionality and make it more uniform
with the other endpoints.

### housen-find and houseno-match endpoints are changed

The houseno-find and houseno-match endpoints return
the `house_number`, `house_letter`, `geo_precision` fields in the response.

Old Germany V1 houseno-find:

```json
[
  {
    "zipcode": 40724,
    "city": "Hilden",
    "street": "Kalstert",
    "houseno": 1,
    "addon": "",
    "streetlang": "de"
  },
  {
    "zipcode": 40724,
    "city": "Hilden",
    "street": "Kalstert",
    "houseno": 104,
    "addon": "",
    "streetlang": "de"
  },
  ...
]
```

New Germany V2 houseno-find:

```json
{
  "results": [
    {
      "street": "Tucherweg",
      "street_language": "de",
      "street_id": "st_407247266946847986",
      "house_number": 48,
      "house_letter": "A",
      "zipcode": 40724,
      "city": "Hilden",
      "geo_precision": "rooftop",
      "latitude": 51.1722315,
      "longitude": 6.9402763
    }
  ],
  "error_code": null,
  "errors": [],
  "pagination": {
    "current_page": 1,
    "results_per_page": 10,
    "is_last_page": true
  }
}
```

### Removal of the deprecated Germany V1 'find' endpoint

The Germany V1 `find` endpoint is not supported in the new API v2 and replaced with
the `address-suggestions` endpoint.

## 🇫🇷 France (New)

France is added to the supported country list, containing over 26M up-to-date addresses.
For V1 for France, the following endpoints are available:

### New address-validation endpoint

See the documentation
at https://developers.postcodeservice.com/#france-api-GETfr-v1-address-validation

### New address-suggestions endpoint

See the documentation
at https://developers.postcodeservice.com/#france-api-GETfr-v1-address-suggestions

### New address-streetid endpoint

See the documentation
at https://developers.postcodeservice.com/#france-api-GETfr-v1-address-streetid

### Postal-address label

For France, the specific postal address formatting label is added to the response data.

Although the French postal address label leaves some standards open, we
choose the most common in use address label, supported by the official French postal service.
In addition, we choose to add the house number addition, for example 'bis', 'ter' or 'quater' to
lower case and followed directly by the house number (although officially not required by the Postal
Service) so that it cannot give any confusion with the preferred B by the postal service that also
stands for 'bis' and
the house letter 'B' that means something different. Therefore, all house letters are in upper case,
all the additions are in lower case.

> House number additions such as appartement or entrance are not included in the postal
> address but should be added before line_1 from our response in your application when creating
> address labels.

```text
"postal_address": {
                "line_1": "1bis Allée de Challes",
                "line_2": "01000 BOURG-EN-BRESSE",
                "line_3": "France"
            }
```

## 🍰 Overall improvements

### Pagination

Added pagination support to all endpoints, allowing fetching more than 10 results per call. You can
use the
parameters 'page' and 'page_limit' to control the pagination. See the documentation for more
information: https://developers.postcodeservice.com

For example V6 Netherlands address suggestions endpoint:
https://api.postcodeservice.com/nl/v6/address-suggestions?street=Kab&city=Amsterdam&page=1&page_limit=10

```text
page   integer  optional. Specify the page number. Default: 1. Example: 1
page_limit   integer  optional. Specify the number of results per page. Default: 10. Maximum: 25. Example: 10
```

Response fields:

```text
"pagination": {
    "current_page": 1,
    "results_per_page": 10,
    "is_last_page": true
}
```

> The is_last_page boolean value indicates whether the current page is the last page or not. If the
> boolean is false, you can fetch the next page by increasing the page parameter by 1 until the last
> page is reached (is_last_page = true).

### URL endpoint structure & Json response uniformity

Added new endpoint structure for all API endpoints and countries, making the API more uniform and
easier to use. The new endpoint structure is as follows:

Endpoint URL structure:

```text
https://api.postcodeservice.com/{country}/{version}/{endpoint}
```

Results uniformity data structure:

```json
{
  "results": [
    {
      "street": "etc."
    }
  ],
  "error_code": null,
  "errors": [],
  "pagination": {
    "current_page": 1,
    "results_per_page": 10,
    "is_last_page": true
  }
}
```

### Uniform error handling

We made the API more robust by adding uniform validation and error handling and converting all
responses to Json format.

Example of an error response:

```json
{
  "results": [],
  "error_code": 400,
  "errors": [
    "The zipcode parameter is required."
  ],
  "pagination": {
    "current_page": 1,
    "results_per_page": 10,
    "is_last_page": true
  }
}
```

### Load balancers uniform error handling

Added load balancers proxy uniform error handling support. For example, 429 rate limiting, which
will return a json response instead of plain text showing a message to the end-user
when the rate limit is exceeded. The error handler is uniform for all countries and in line with
the new general API json response structure.

Old format:

```json
{
  "success": false,
  "error_code": 429,
  "error": "Request rate limit exceeded",
  "error_message": "Request rate limit exceeded V3. Please slow down your request rate or increase your limit by contacting your account manager at +31 20 218 1024."
}
```

New format:

```json
{
  "results": [],
  "error_code": 429,
  "errors": [
    {
      "error": "Exceeded request rate limit",
      "error_message": "Exceeded request rate limit V6. Please slow down your request rate or increase your limit by contacting your account manager at +31 20 218 1024."
    }
  ],
  "pagination": {
    "current_page": 1,
    "results_per_page": 0,
    "is_last_page": true
  }
}
```

### New error handling and test possibilities

With this release new error codes are added to the API. The error codes are uniform for all
countries and in line with the new general API json response structure.

You can test your error handling code with the old and new error responses by calling the specific
error handler endpoint:

For example for Unauthorized access, V3 Netherlands:

```text
curl --request GET \
    --get "https://api.postcodeservice.com/nl/v3/401?client_id=1177&secure_code=IncorrectSecureCode"
```

Returns:

```json
{
  "success": false,
  "error_code": 401,
  "error": "Unauthorized access",
  "error_message": "Unauthorized access V3. Please verify your Client ID and Secure Code. For assistance, contact our support team at support@postcodeservice.com."
}
```

For example for Unauthorized access, V6 Netherlands:

```text
curl --request GET \
    --get "https://api.postcodeservice.com/nl/v6/401?client_id=1177&secure_code=IncorrectSecureCode"
```

Returns:

```json
{
  "results": [],
  "error_code": 401,
  "errors": [
    {
      "error": "Unauthorized Access",
      "error_message": "Unauthorized access V6. Please verify your Client ID and Secure Code. For assistance, contact our support team at support@postcodeservice.com."
    }
  ],
  "pagination": {
    "current_page": 1,
    "results_per_page": 0,
    "is_last_page": true
  }
}
```

> The `error_code` field is added to the error response to make it easier to identify the error
> type.
> Besides the `error_code` in the response, the HTTP status code is also returned. The HTTP status
> code
> is the same as the `error_code`, but in integer format. For example, the error_code `401` is also
> returned as HTTP status code `401`.

In addition to the above, the new error handler also checks for additional unused parameters and
returns an error message when the parameters are not supported by the endpoint. For example, when
the house_number_validation parameter is added to the address-suggestions endpoint (where it cannot
be used):

```json
{
  "results": [],
  "error_code": 422,
  "errors": [
    {
      "error": "The parameter house_number_validation is incorrect and should not be included.",
      "error_message": "The parameter house_number_validation is incorrect and should not be included."
    }
  ],
  "pagination": {
    "current_page": 1,
    "results_per_page": 0,
    "is_last_page": true
  }
}
```

### Authentication via URL parameters

Although deprecated, legacy client_id and secure code authentication via the URL instead of header
authentication is still supported. Support for the legacy authentication is retrospectively added
to all newer endpoints:
Netherlands V4 and up, Belgium V3 and up, Germany V1 and up, French V1 and up.

Legacy authentication:

```text
https://api.postcodeservice.com/nl/v6/find?zipcode=4201KB&houseno=63&client_id=123&secure_code=123
```

Recommended authentication:

```text
https://api.postcodeservice.com/nl/v6/find?zipcode=4201KB&houseno=63

Via http headers (see curl example at https://developers.postcodeservice.com/#authentication):
client_id: 123
secure_code: 123
```

This authentication method is more secure as it is not logged in server log files and recommended
for new integrations.

### Houseno parameter renamed to house_number

⚠ Changed the `houseno` parameter to `house_number` in the Netherlands V6 API, Belgium V4, German
V2 to make it more uniform and support for new countries. Please update your code when you switch to
the new API version.

For example older Netherlands V5 call with houseno parameter (deprecated, but still supported) for
Version 5 and below:

```text
https://api.postcodeservice.com/nl/v5/address-validation?zipcode=1014BA&houseno=21
```

New V6:

```text
https://api.postcodeservice.com/nl/v6/address-validation?zipcode=1014BA&house_number=21
```

## Developers documentation

* The developers documentation at https://developers.postcodeservice.com has been updated to reflect
  the new API structure and new features.
* We have removed the deprecated version endpoints documentation from the main documentation
  location. The deprecated endpoints documentation is moved
  to https://developers.postcodeservice.com/deprecated/.
* We have added links to the System status https://postcodeservice.statuspage.io/ and release
  history pages (here, yeah).
* The Postman Collection has been updated to reflect the new API structure and new features
  at https://developers-staging.postcodeservice.com/collection.json
* The Netherlands V1, V2 and V3 legacy documentation is added to the deprecated endpoints
  documentation with this release for reference purposes. Speaking about Marty McFly, we are going
  back to the future with this V1..V3 added documentation.

## 🐠 UX/UC improvements

- The navigation scroll delay at https://developers.postcodeservice.com has been removed for better
  UX experience.
- Added new feature request form to the Postcode Service API documentation, allowing users to submit
  feature requests directly from the documentation. https://postcodeservice.com/feature-request/
- Added new support and bug report form to the Postcode Service API documentation, allowing users to
  submit bug reports directly from the documentation. https://postcodeservice.com/support/

## 🐞 Fixed bugs

- Fixed HTTP code 500 server response when users added certain Unicode characters (tropical fish,
  etc.) to the parameters
  of the endpoints.
- Fixed an HTTP code 500 server response for the Belgium houseno-match endpoint when supplying
  incorrect parameters.
- Fixes in German data for villages that are on the border where some street names were also
  available in Chech language.
- Cleaned up Belgium redundant street names in the data with human error mistyped street names.

## ✨ Behind the scenes

* Black friday had a record number of requests per second, but the API's handled the load without
  any issues.
* We handled about 319 support tickets in the last 6 months, mostly about integration challenges,
  Magento related questions and some feature requests. We are always happy to help you with your
  integration and feature requests. Please contact us at support@postcodeservice.com
* We expanded our storage systems to handle the reversed backwards compatibility support and
  ever-growing data. Also, we have upgraded and expanded our network infrastructure to support the
  new API structure and new features.
* The nodes behind the load balancers are increased in numbers to handle the increasing usage of the
  API's. We are 24/7 monitoring response times and load to make sure the APIs are fast and reliable
  using independent monitoring SaaS solutions.

## 🐰 Release credits

- Thanks go out to the following external people for contributing to this release: Peter S. and Tim S.,
  Viktoria S.
