---
layout: post
title:  Variant
---

A product variant.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|upc|string|universal product code|N/A|yes|
|description|string|description of variant|N/A|no|
|details|string|details about variant|N/A|no|
|gm_merchandisable|boolean|whether this variant should appear in the Google Merchant feed|false|no|
|on_sale|boolean|if variant is on sale|false|no|
|out_of_stock|boolean|variant is out of stock (manual inventory only)|false|no|
|preorder_date|date|expected ship date for preorders|N/A|no|
|published|boolean|whether variant should appear on website|true|no|
|color|string|color of variant|N/A|no|
|size|string|size of variant|N/A|no|
|pricings_attributes|[Pricing[]](/types/pricing)|array of price objects|N/A|no|
