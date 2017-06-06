|parameter|type|explanation|default|required|
|---|---|---|---|---|
|name|string|product name|N/A|yes|
|sku|product SKU|string|N/A|yes|
|permalink|string|unique permalink for product URL|auto-generated from product name|no|
|season_id|integer|season ID|N/A|no|
|auto_unpublish|boolean|automatically unpublish when inventory reaches 0|false|no|
|description|string|product description|N/A|no|
|detail_description|string|additional description|N/A|no|
|product_details|string|details for product|N/A|no|
|product_care|string|product care instructions|N/A|no|
|product_sizing|string|sizing details|N/A|no|
|preorder|allow product preorder|boolean|false|no|
|preorder_date|expected ship date for preorder|date|N/A|no|
|inventory_threshold|integer|threshold above which the product is considered in stock|N/A|no|
|images_attributes|[Image[]](types/image)|array of image objects|N/A|no|
|variants_attributes|[Variant[]](types/variant)|array of product variant objects|N/A|no|
