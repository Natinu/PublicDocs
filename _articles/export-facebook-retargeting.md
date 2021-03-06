---
layout: article
title: Standard Facebook Retargeting Export
resource: true
categories: [Exports]
---

## Overview

Olapic offers a flat file CSV export solution (including basic product information and the associated best user generated content) for the purpose of integration curated UGC (user generated content) with Facebook's retargeting ads. The feed’s format was designed per [*Facebook’s Product Catalog Guidelines*](https://developers.facebook.com/docs/marketing-api/dynamic-product-ads/product-catalog). The associated UGC product images included in the CSV will be curated by the client, and have rights & permissions obtained from the original user who generated the photo.

There are two choices in regards to the inclusion of product image(s) in the feed:
* The image with the highest Photorank score associated with the product. Photorank is Olapic’s proprietary algorithm scoring system that predicts the success of a photo based on 40+ attributes.
* The image with the highest click-through rate (CTR).

Also note that if there is no rights-approved UGC associated with the product, the product id record will be excluded from the feed.

## File Specifications

**File Naming Convention:**
\[ClientNameID\]\_Olapic\_FB\_\[yyyymmddmiss\].csv

**File Format:** Comma-delimited, double quote qualifier, .CSV
extension, Unix line ending, header row per below.

**File Frequency:** Daily, full file refresh.

**File Transfer Options:**

1.  Push to your ftp or sftp server via password authentication

2.  Pick up from an Olapic hosted ftp account with password authentication or an sftp account with PEM key authentication

**File Drop Time:** Configurable from 11pm to 10am UTC

**UGC Selection Option:** Highest Photorank score or highest click-through rate.

**File Layout Options:**

1. UGC Image URL options:
  * Best image associated with the product (default)
  * Up to ten best images associated with the product (in one field, comma separated)
<br><br>
2. Filters: 
- You can choose the media size we export to the UGC Image URL.
- (Optional) You also have the option of only exporting square images from one of these selected media sizes.
- Media Size options:
  * Original - Original image without modifications (not necessarily square)
        * When selected, you can export only square images greater than a specified pixel width
  * Normal - 640x640px image. Maintains ratio (not necessarily square)
  * Mobile - 320x320px image. Maintains ratio (not necessarily square)
  * Thumbnail - 150x150px image. Maintains ratio (not necessarily square)
  * Square - 90x90px image. Cropped and does not maintain ratio


##### File Layout:

| **Position** | **Header Field Name**  | **Description/Notes**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|--------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1            | id                     | The product id as represented in the original feed provided by the client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 2            | availability           | Either will output ‘in stock’ or ‘out of stock’ based on product availability information passed to Olapic by the client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 3            | condition              | This information is not collected at Olapic and therefore is hardcoded to ‘new’ and will output as such.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 4            | description            | The product description as represented in the original feed provided by the client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 5            | image\_link            | If the option is chosen to output the one best UGC, the system will output the best approved, with rights, UGC image URL associated to that product, . If the option is chosen to output up to the best 10 UGC image URLs, then the value outputted in this field will be the product’s stock photo image URL from the original feed provided to Olapic. Any additional image links will be added to the `additional_image_link` row.<br> |
| 6            | link                   | The product page URL as represented in the original feed provided by the client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 7            | title                  | The product name as represented in the original feed provided by the client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 8            | price                  | The price as represented in the original feed provided by the client plus the ISO 4217 currency code. Otherwise 0.00 USD will output instead.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 9            | brand                  | The client name id, internally known as the client’s template directory.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 10           | additional\_image\_link | Optional field. Up to 10 best, Published with Rights UGC URL’s associated with this product in a comma separated fashion. If up to 10 best UGC is not chosen, then the 10th field will not output and this will be a file layout file consisting of 9 fields only.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |

<br>

##### Sample Default File Layout With Top UGC:


| **id** | **availability** | **condition** | **description** | **image\_link** | **link** | **title** | **price** | **brand** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 176 | in stock | `new`| This shirt is covered with amazing flowers. | [https://photorankmedia-a.akamaihd.net/media/d/2/i/socno6/normal.jpg](https://photorankmedia-a.akamaihd.net/media/d/2/i/socno6/normal.jpg) | [http://www.someolapicclient.com/product/176/daisyshirt/index.tmpl](http://www.someolapicclient.com/product/176/daisyshirt/index.tmpl) | Daisy Shirt | 25.99 USD | freelove |
| 177 | in stock | `new`| This top has a colorful plaid print that is still classic. | [https://photorankmedia-a.akamaihd.net/media/d/2/i/socno71/normal.jpg](https://photorankmedia-a.akamaihd.net/media/d/2/i/socno71/normal.jpg) | [http://www.someolapicclient.com/product/176/classicplaid/index.tmpl](http://www.someolapicclient.com/product/176/classicplaid/index.tmpl) | Classic Plaid | 35.00 USD | freelove |
| 201 | out of stock | `new`| This jacket is the perfect edgy jacket for fall. | [http://www.someolapicclient/images/products/152x358/SOC201\_152x358.jpg](http://www.someolapicclient/images/products/152x358/SOC201_152x358.jpg) | [http://www.someolapicclient.com/product/201/daisyshirt/index.tmpl](http://www.someolapicclient.com/product/201/daisyshirt/index.tmpl) | Biker Jacket | 0.00 USD | freelove |


