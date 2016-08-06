---
layout: post
title:  "Sellect API v1 Documentation"
date:   2016-08-05 17:34:21 -0400
categories: sellect documentation v1
---

1. [Authentication](#authentication)
2. [Errors](#errors)
3. [Orders](#orders)
4. [Refunds](#refunds)
5. [Inventory](#inventory)

## Authentication
Authenticate your account when using the API by including your secret API key in the request. You will be provided a test and live api key by the Sellect Team. Your API keys carry many privileges, so be sure to keep them secret! Do not share your secret API keys in publicly accessible areas such GitHub, client-side code, and so forth.

Authentication to the API is performed via HTTP Basic Auth. Provide your API key as the basic auth username value. You do not need to provide a password.
All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail.

**Example Request:**

```
$ curl https://some-online-store.com/sellect/v1/orders \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

## Errors
Sellect uses conventional HTTP response codes to indicate the success or failure of an API request. In general, codes in the 2xx range indicate success, codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a charge failed, etc.), and codes in the 5xx range indicate an error with Sellect's servers (these are rare).
Not all errors map cleanly onto HTTP response codes, however. When a request is valid but does not complete successfully (e.g., a card is declined), we return a 402 error code.

**HTTP status code summary**

|code              |explanation                                                                                                       |
|------------------|------------------------------------------------------------------------------------------------------------------|
|200               |OK  Everything worked as expected.                                                                                |
|400               |Bad Request The request was unacceptable, often due to missing a required parameter.                              |
|401               |Unauthorized  No valid API key provided.                                                                          |
|402               |Request Failed  The parameters were valid but the request failed.                                                 |
|404               |Not Found The requested resource doesn't exist.                                                                   |
|409               |Conflict  The request conflicts with another request (perhaps due to using the same idempotent key).              |
|429               |Too Many Requests Too many requests hit the API too quickly. We recommend an exponential backoff of your requests.|
|500, 502, 503, 504|Server Errors  Something went wrong on Sellect's end. (These are rare.)                                           |

## Orders

### Retrieve an order
Retrieves the details of an order. Supply the unique order number and Sellect will return the corresponding order information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/orders/{ORDER_NUMBER}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/orders/R123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/order": {
    "number": "R123456789",
    "state": "complete",
    "ordered_at": "2016-08-06T00:01:17.000Z",
    "currency": "usd",
    "gift_message": nil,
    "invoice_url": "http://test.sellect.com/orders/TEST101206324/invoice/4df790c48bbf867f2713a5f8c0696265",
    "email": "example2@example.com",
    "tax": 0.0,
    "shipping_discount": 0.0,
    "item_discount": 0.0,
    "warehouse": "uk-eu",
    "shipping_address": {
      "first_name": "Bob",
      "last_name": "Uecker",
      "address1": "123 Testing St",
      "address2": "Floor 17 1/2",
      "city": "New York City",
      "state_name": "New York",
      "zipcode": "12345",
      "phone": "+15555555555",
      "country": "US"
    },
    "billing_address": {
      "first_name": "Bob",
      "last_name": "Uecker",
      "address1": "123 Testing St",
      "address2": "Floor 17 1/2",
      "city": "New York City",
      "state_name": "New York",
      "zipcode": "12345",
      "phone": "+15555555555",
      "country": "US"
    },
    "shipping_method": {
      "name": "FedEx",
      "carrier": nil,
      "rate": 10.0
    },
    "line_items": [
      {
        "price": "100.0",
        "quantity": 1,
        "upc": "123123121",
        "tax": 0,
        "vat": 0
      },
      {
        "price": "100.0",
        "quantity": 1,
        "upc": "123123122",
        "tax": 0,
        "vat": 0
      }
    ]
  }
}
```

### Retrieve multiple orders
Retrieves the details of a collection of orders during a given time range.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|start_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|yes|
|end_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|yes|
|state|string|shipping, complete, canceled|complete|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/orders
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/orders \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d start_date="2016-01-31 12:00:00 EDT" \
   -d end_date="2016-01-31 12:00:00 EDT" \
   -d state="shipping"
```

**Example Response**

```
{
  "sellect/orders": [
    {
      "number": "R123456789",
      "state": "complete",
      "ordered_at": "2016-08-06T00:01:17.000Z",
      "currency": "usd",
      "gift_message": nil,
      "invoice_url": "http://test.sellect.com/orders/TEST101206324/invoice/4df790c48bbf867f2713a5f8c0696265",
      "email": "example2@example.com",
      "tax": 0.0,
      "shipping_discount": 0.0,
      "item_discount": 0.0,
      "warehouse": "uk-eu",
      "shipping_address": {
        "first_name": "Bob",
        "last_name": "Uecker",
        "address1": "123 Testing St",
        "address2": "Floor 17 1/2",
        "city": "New York City",
        "state_name": "New York",
        "zipcode": "12345",
        "phone": "+15555555555",
        "country": "US"
      },
      "billing_address": {
        "first_name": "Bob",
        "last_name": "Uecker",
        "address1": "123 Testing St",
        "address2": "Floor 17 1/2",
        "city": "New York City",
        "state_name": "New York",
        "zipcode": "12345",
        "phone": "+15555555555",
        "country": "US"
      },
      "shipping_method": {
        "name": "FedEx",
        "carrier": nil,
        "rate": 10.0
      },
      "line_items": [
        {
          "price": "100.0",
          "quantity": 1,
          "upc": "123123121",
          "tax": 0,
          "vat": 0
        },
        {
          "price": "100.0",
          "quantity": 1,
          "upc": "123123122",
          "tax": 0,
          "vat": 0
        }
      ]
    }
  ]
}
```

## Refunds

### Retrieve a refund
Retrieves the details of a refund. Supply the unique id for the refund and Sellect will return the corresponding refund information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/refunds/{REFUND_ID}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/refunds/456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/transaction/refund": {
    "id": 5,
    "rma_number": "556226021",
    "order_number": "TEST808828460",
    "total_returned": 200.0,
    "credit": 0.0,
    "shipping_credit": 0.0,
    "tax_credit": 0.0,
    "return_line_items": [
      {
        "amount_returned": 0.0,
        "quantity": 0,
        "upc": "123123123",
        "reason": null
      },
      {
        "amount_returned": 0.0,
        "quantity": 0,
        "upc": "123123124",
        "reason": null
      }
    ]
  }
}
```

### Retrieve multiple refunds
Retrieves the details of a collection of refunds during a given time range.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|start_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|yes|
|end_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|yes|
|state|string|pending, processed|processed|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/refunds
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/refunds \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d start_date="2016-01-31 12:00:00 EDT" \
   -d end_date="2016-01-31 12:00:00 EDT" \
   -d state="pending"
```

**Example Response**

```
{
  "sellect/transaction/refunds": [
    {
      "id": 10,
      "rma_number": "046423181",
      "order_number": "TEST475813582",
      "total_returned": 200.0,
      "credit": 0.0,
      "shipping_credit": 0.0,
      "tax_credit": 0.0,
      "return_line_items": [
        {
          "amount_returned": 0.0,
          "quantity": 0,
          "upc": "123123127",
          "reason": nil},
        {
          "amount_returned": 0.0,
          "quantity": 0,
          "upc": "123123128",
          "reason": nil
        }
      ]
    }
  ]
}
```

## Inventory

### Update an inventory unit
Updates a single inventory unit. Supply the UPC and quantity (optionally the warehouse code) for the variant to update.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|quantity|integer|quantity available|N/A|yes|
|warehouse|string|warehouse code|If your shop only has one warehouse that will be the default|false|

**Definition**

```
PUT https://some-online-store.com/sellect/v1/inventory_units/{VARIANT_UPC}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/inventory_units/123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -X PUT \
   -d warehouse="US" \
   -d quantity=10
```

**Example Response**

```
{
  "sellect/inventory/unit": {
    "id": 1,
    "upc": "123456789",
    "quantity": 10,
    "warehouse": "US"
  }
}
```

### Update multiple inventory units
Updates multiple inventory units in one request by passing a JSON object of inventory units with upc, quantity, and warehouse.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|upc|string|variant upc|N/A|yes|
|quantity|integer|quantity available|N/A|yes|
|warehouse|string|warehouse code|If your shop only has one warehouse that will be the default|false|

**Definition**

```
PUT https://some-online-store.com/sellect/v1/inventory_units
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/inventory_units/bulk_update \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -X PUT \
   -H "Content-Type: application/json" \
   -d '{"inventory_units": [{"upc": "123456789", "quantity": 10, "warehouse": "US"}, {"upc": "223456789", "quantity": 3, "warehouse": "US"}]}'
```

**Example Response**

```
{
  "sellect/inventory/units": [
    {
      "id": 1,
      "upc": "123456789",
      "quantity": 10,
      "warehouse": "US"
    },
    {
      "id": 2,
      "upc": "223456789",
      "quantity": 3,
      "warehouse": "US"
    }
  ]
}
```
