---
layout: post
title:  "Sellect API v1 Documentation"
date:   2016-08-05 17:34:21 -0400
categories: sellect documentation v1
---

1. [Authentication](#authentication)
2. [Errors](#errors)
3. [Orders](#orders)
4. [Shipments](#shipments)
5. [Refunds](#refunds)
6. [Products](#products)
7. [Variants](#variants)
8. [Inventory](#inventory)
9. [Product Categories](#product-categories)
10. [Users](#users)

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
    "ordered_at": "2017-06-09 15:26:20 UTC",
    "created_at": "2017-06-09 15:21:48 UTC",
    "updated_at": "2017-06-13 15:50:30 UTC",
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
        "tax": 0.0,
        "vat": 0.0
      },
      {
        "price": "100.0",
        "quantity": 1,
        "upc": "123123122",
        "tax": 0.0,
        "vat": 0.0
      }
    ]
  }
}
```

### Retrieve multiple orders
Retrieves the details of a collection of orders during a given time range. If no start or end date is supplied then the default is all complete orders.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|start_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|no|
|end_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|no|
|time_scope|string|ordered_at, created_at, updated_at|created_at|no|
|state|string|shipping, complete, canceled|shipping|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/orders
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/orders \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d start_date="2016-01-10%2001%3A00%3A00%20EDT" \
   -d end_date="2017-06-13%2011%3A50%3A30%20EDT" \
   -d state="shipping"
```

**Example Response**

```
{
  "sellect/orders": [
    {
      "number": "R123456789",
      "state": "shipping",
      "ordered_at": "2017-06-09 15:26:20 UTC",
      "created_at": "2017-06-09 15:21:48 UTC",
      "updated_at": "2017-06-13 15:50:30 UTC",
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
          "tax": 0.0,
          "vat": 0.0
        },
        {
          "price": "100.0",
          "quantity": 1,
          "upc": "123123122",
          "tax": 0.0,
          "vat": 0.0
        }
      ]
    }
  ]
}
```

## Shipments

### Retrieve an shipment
Retrieves the details of an shipment. Supply the unique shipment number and Sellect will return the corresponding shipment information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/shipments/{ORDER_NUMBER}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/shipments/R123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/shipment": {
    "id": 1,
    "order_number": "TEST154633468",
    "shipment_number": "12345678",
    "state": "pending",
    "tracking": null,
    "line_items": [
      {
        "price": "100.0",
        "quantity": 1,
        "upc": "123123121",
        "tax": 0.0,
        "vat": 0.0
      },
      {
        "price": "100.0",
        "quantity": 1,
        "upc": "123123122",
        "tax": 0.0,
        "vat": 0.0
      }
    ]
  }
}
```

### Retrieve multiple shipments
Retrieves the details of a collection of shipments during a given time range. If no start or end date is supplied then the default is all pending shipments.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|start_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|no|
|end_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|no|
|time_scope|string|shipped_at, created_at, updated_at|created_at|no|
|state|string|pending, ready, shipped, canceled|ready|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/shipments
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/shipments \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d start_date="2016-01-31%2012%3A00%3A00%20EDT" \
   -d end_date="2016-01-31%2012%3A00%3A00%20EDT" \
   -d state="pending"
```

**Example Response**

```
{
  "sellect/shipments": [
    {
      "id": 2,
      "order_number": "TEST448343386",
      "shipment_number": "12345678",
      "state": "pending",
      "tracking": null,
      "line_items": [
        {
          "price": "100.0",
          "quantity": 1,
          "upc": "123123123",
          "tax": 0.0,
          "vat": 0.0
        },
        {
          "price": "100.0",
          "quantity": 1,
          "upc": "123123124",
          "tax": 0.0,
          "vat": 0.0
        }
      ]
    }
  ]
}
```

### Update a shipment
Update a shipment's tracking information and/or confirm a shipment for dispatch.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|tracking|string|The tracking number used for shipping.|N/A|no|
|ship|boolean|If true the shipment will be shipped and transactional emails will be sent|N/A|no|

**Definition**

```
PUT https://some-online-store.com/sellect/v1/shipments/{ORDER_NUMBER}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/shipments/TEST448343386 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -X PUT \
   -d ship=true \
   -d tracking="tracking1234"
```

**Example Response**

```
{
  "sellect/shipment": {
    "id": 1,
    "order_number": "TEST443244655",
    "shipment_number": "12345678",
    "state": "shipped",
    "tracking": "tracking1234",
    "line_items": [
      {
        "price": "100.0",
        "quantity": 1,
        "upc": "123123121",
        "tax": 0.0,
        "vat": 0.0
      },
      {
        "price": "100.0",
        "quantity": 1,
        "upc": "123123122",
        "tax": 0.0,
        "vat": 0.0
      }
    ]
  }
}
```

## Refunds

### Retrieve refund(s) for a given RMA number
Retrieves the details of refund(s) associated with a given RMA number. Supply the RMA number and Sellect will return the corresponding refund(s) information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/refunds/{RMA_NUMBER}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/refunds/556226021 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/transaction/refunds": [
    {
      "id": 5,
      "rma_number": "556226021",
      "order_number": "TEST808828460",
      "total_returned": 200.0,
      "credit": 0.0,
      "shipping_credit": 0.0,
      "state": "pending",
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
  ]
}
```

### Retrieve multiple refunds
Retrieves the details of a collection of refunds during a given time range. If no start or end date is supplied then the default is all processed refunds.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|start_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|no|
|end_date|string|YYYY-MM-DD HH:MM:SS TZ|N/A|no|
|time_scope|string|created_at, updated_at|created_at|no|
|state|string|pending, processed, failed|processed|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/refunds
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/refunds \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d start_date="2016-01-31%2012%3A00%3A00%20EDT" \
   -d end_date="2016-01-31%2012%3A00%3A00%20EDT" \
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
      "state": "pending",
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

### Approve refunds for a given RMA number
Approves all pending refunds associated with a given RMA number.

**Definition**

```
PUT https://some-online-store.com/sellect/v1/refunds/{RMA_NUMBER}/approve
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/refunds/046423181 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -X PUT
```

## Products

### Retrieve a product
Retrieves the details of a product. Supply the product ID and Sellect will return the corresponding product's information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/products/{PRODUCT_ID}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/products/123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/product": {
    "name": "test product",
    "description": null,
    "variants": [
      {
        "upc": "123123121",
        "product_id": 1,
        "size": "o/s",
        "color": "red"
      }
    ]
  }
}
```

### Retrieve multiple products
Retrieves the details of a collection of products. If specific query information is not passed with the request all products will be returned.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|name|string|product name|N/A|no|
|permalink|string|product permalink|N/A|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/products
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/products \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d permalink="test-product"
```

**Example Response**

```
{
  "sellect/products": [
    {
      "name": "test product",
      "description": null,
      "variants": [
        {
          "upc": "123123121",
          "product_id": 1,
          "size": "o/s",
          "color": "red"
        }
      ]
    }
  ]
}
```

### Create a product
Creates a new product and returns the result.

{% include product_attributes.markdown %}

**Definition**

```
POST https://some-online-store.com/sellect/v1/products
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/products \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -H "Content-Type: application/json" \
   -d @- << EOF
  {
    "product": {
      "name": "New Product",
      "sku": "SOMESKU",
      "variants_attributes": [
        {
          "upc": "12345",
          "description": "This is the first variant",
          "pricings_attributes": [
            {
              "currency": "USD",
              "price": "69.67"
            }
          ]
        },
        {
          "upc": "123456",
          "description": "This is another variant",
          "color": "red",
          "pricings_attributes": [
            {
              "currency": "USD",
              "price": "42.00"
            }
          ]
        }
      ],
      "images_attributes": [
        {
          "color": "red",
          "attachment_remote_url": "https://example.com/images/123456.jpeg"
        }
      ]
    }
  }
EOF

```

**Example Response**

```json
{
  "sellect/product": {
    "name": "New Product",
    "description": null,
    "variants": [
      {
        "upc": "12345",
        "product_id": 1,
        "size": null,
        "color": null
      },
      {
        "upc": "123456",
        "product_id": 1,
        "size": null,
        "color": "red"
      }
    ]
  }
}
```

### Update a product
Update an existing product. Note that nested resources will be updated if an id
is specified, otherwise a new one will be created.

{% include product_attributes.markdown %}

**Definition**

```
PUT https://some-online-store.com/sellect/v1/products/{PRODUCT_ID}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/products/1 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -X PUT \
   -H "Content-Type: application/json" \
   -d @- << EOF
  {
    "product": {
      "variants_attributes": [
        {
          "id": 1,
          "description": "Updated variant description"
        }
      ]
    }
  }
EOF

```

**Example Response**

```json
{
  "sellect/product": {
    "name": "Some Product",
    "description": null,
    "variants": [
      {
        "upc": "12345",
        "product_id": 1,
        "size": null,
        "color": null
      }
    ]
  }
}
```


## Variants

### Retrieve a variant
Retrieves the details of a variant. Supply the variant upc and Sellect will return the corresponding variant's information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/variants/{VARIANT_UPC}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/variants/123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/variant": {
    "upc": "123123121",
    "product_id": 1,
    "size": "o/s",
    "color": "red",
    "pricings": [
      {
        "currency": "usd",
        "price": "100.0"
      }
    ]
  }
}
```

### Retrieve multiple variants
Retrieves the details of a collection of variants. If specific query information is not passed with the request all variants will be returned.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|upc|string|variant upc|N/A|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/variants
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/variants \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d upc="123123122"
```

**Example Response**

```
{
  "sellect/variants": [
    {
      "upc": "123123122",
      "product_id": 2,
      "size": "o/s",
      "color": "red",
      "pricings": [
        {
          "currency": "usd",
          "price": "100.0"
        }
      ]
    }
  ]
}
```

## Inventory

### Retrieve an inventory unit
Retrieves the details of an inventory unit. Supply the variant upc for the inventory unit and Sellect will return the corresponding inventory unit's information.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|warehouse|string|warehouse code|If your shop only has one warehouse that will be the default|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/inventory_units/{VARIANT_UPC}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/inventory_units/123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d warehouse="US"
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

### Retrieve multiple inventory units
Retrieves the details of a collection of inventory units. If no upcs or warehouse are passed with the request all inventory units will be returned.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|upc|string|variant upc|N/A|no|
|warehouse|string|warehouse code|If your shop only has one warehouse that will be the default|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/inventory_units
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/inventory_units \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK: \
   -G \
   -d upc="123456789"
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
      "upc": "123456789",
      "quantity": 3,
      "warehouse": "UK"
    }
  ]
}
```

### Update an inventory unit
Updates a single inventory unit. Supply the UPC and quantity (optionally the warehouse code) for the variant to update.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|quantity|integer|quantity available|N/A|yes|
|warehouse|string|warehouse code|If your shop only has one warehouse that will be the default|no|

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
|warehouse|string|warehouse code|If your shop only has one warehouse that will be the default|no|

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


## Product Categories

### Retrieve a product category
Retrieves the details of a product category. Supply the product category ID and Sellect will return the corresponding product category's information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/product_categories/{PRODUCT_CATEGORIES_ID}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/product_categories/123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
   -G \
   -d permalink="test-category""
```

**Example Response**

```
{
  "sellect/product_category" : {
    "id": 1,
    "name": "test category",
    "presentation": "test category",
    "permalink": "test-category",
    "parent_id": nil,
    "products": []
  }
}
```

### Retrieve multiple product categories
Retrieves the details of a collection of product categories.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|name|string|product category name|N/A|no|
|permalink|string|product category permalink|N/A|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/product_categories
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/product_categories \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
   -G \
   -d permalink="test-category"
```

**Example Response**

```
{
  "sellect/product_categories" : [
    {
      "id": 2,
      "name": "test category",
      "presentation": "test category",
      "permalink": "test-category",
      "parent_id": nil,
      "products": []
    }
  ]
}
```


## Users

### Retrieve a user
Retrieves the details of a user. Supply the user ID and Sellect will return the corresponding user's information.

**Definition**

```
GET https://some-online-store.com/sellect/v1/users/{USER_ID}
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/users/123456789 \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/user": {
    "id": 1,
    "first_name": "test",
    "last_name": "account",
    "phone": 9999999999,
    "email": "testaccount@test.com",
    "created_at": "2017-11-09 15:07:24 UTC",
    "updated_at": "2017-11-09 15:07:24 UTC"
  }
}
```

### Retrieve multiple users
Retrieves the details of a collection of users. If specific query information regarding a user's role is not passed with the request all users will be returned.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|scope|string|user role|N/A|no|

**Definition**

```
GET https://some-online-store.com/sellect/v1/users
```

**Example Request**

```
$ curl https://some-online-store.com/sellect/v1/users \
   -u sellect_test_4gh8KKUIcCEOkCRKQQNOFAiK:
```

**Example Response**

```
{
  "sellect/guest_users" : [
    {
      "id": 77,
      "first_name": "test",
      "last_name": "account",
      "phone": null,
      "email": "tester@test.com",
      "created_at": "2017-12-06 18:32:49 UTC",
      "updated_at": "2017-12-06 18:32:49 UTC"
    }
  ]
}
```
