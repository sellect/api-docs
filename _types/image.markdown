---
layout: post
title:  Image
---

An image asset to display for a product.

|parameter|type|explanation|default|required|
|---|---|---|---|---|
|position|integer|images are sorted in ascending order based on this field|N/A|no|
|color|string|the color this image should be displayed for|N/A|yes|
|alt|string|alt text|N/A|no|
|attachment_remote_url|string|URL of image file|N/A|if attachment_dropbox_filename is not specified|
|attachment_dropbox_filename|string|filename of image in configured DropBox images folder|N/A|if attachment_remote_url is not specified|
