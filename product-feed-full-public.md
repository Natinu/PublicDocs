---
layout: article
title: Olapic Product Feed
resource: true
categories: [Resources]
---

The following document will explain the product feed supported by **Olapic**.

## Table of Contents

- [Basics](#basics)
- [XML product feed](#XML-product-feed)
    - [Start your XML file](#Start-your-XML-file)
    - [Product categorization](#Product-categorization)
    - [`<Product>` element](#<Product>-element)
    - [Attributes elements of `<Product>` element](#Attributes-elements-of-<Product>-element)
    - [XML Feed Example](#XML-Feed-Example)
      - [Product Hierarchy Explained](#Product-Hierarchy-Explained)
- [Validating your feed](#Validating-your-feed)
- [Delivering your Product Feed](#Delivering-your-Product-Feed)
    - [Note: Feed update times](#note-feed-update-times)
- [Providing a custom Product Feed](#Providing-a-custom-Product-Feed)
    - [Feed Feature Support](#Feed-Feature-Support)
- [Changing the Product Feed](#Changing-the-Product-Feed)

## Basics

**Product Feed (PF)** is a list of products in your e-commerce store. Olapic requires this feed to create what we call `streams` for your account so you can start tagging the media to specific products in the Moderation Queue. So having the right product feed format is critical for a correct implementation.

Ideally, we will create one stream per product object / node in your PF.

## XML product feed

We will focus on how to create your PF for Olapic, and explain which fields are necessary for a correct implementation.


### Start your XML file

To start, we only support `UTF-8` encoding, so your XML feed must start with the following line:

```xml
<?xml version="1.0" encoding="utf-8"?>
```

**Note:** Please ensure your feed is in `UTF-8`. If not, please contact your Integration Engineer to check other available options.

As this will be an XML file, we will need a root node. We will use `<Feed>` as the root node as default. Inside the `<Feed>` node, we will have `<Products>` node with children nodes called `<Product>`.

Here is an example of the general schema:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Feed>
	<Categories>
		<Category>...</Category>
		<Category>...</Category>
	</Categories>
	<Products>
		<Product>...</Product>
		<Product>...</Product>
	</Products>
</Feed>
```

### Product categorization

We support category structure for each product in your e-commerce store. In order to use our *category_based widget*. If you want to use this feature, all you need to do is give us all the categories you will want to create in Olapic as child elements within the `<Categories>` element.

The `<Categories>` element can contain as meny `<Category>` children nodes as you need.

You can build the `<Category>` elements using the following children elements:

| Element Name | Description | Required |
|--------------|-------------|----------|
| Name | The visible name of the category. | **Yes** |
| CategoryUniqueID | A unique ID for the category. <br>**Note: The value of this element can not be empty or contain white spaces.** | **Yes** |
| CategoryUrl | A URL where visitors can shop by this category, if you have one. **Include the full URL, with the schema(http/https)**. **This must be anyURI valid, see below(1)** | Only if you use our *category_based* widget |
| CategoryParentID | This is to support sub-category levels. If the category is a sub-category with a specific parent category, please use the parent category's CategoryUniqueID within this element.<br>**Note: The value of this element can not be empty or contain white spaces. If you don't have a valid ID to provide, please don't include the field** | NO |


`Categories` node example:

```xml
<Categories>
	<Category>
		<CategoryUniqueID>cat1001</CategoryUniqueID>
		<Name>Men's</Name>
		<CategoryUrl>http://www.myawesomestore.com/categories/mens</CategoryUrl>
	</Category>
	<Category>
		<CategoryUniqueID>cat1002</CategoryUniqueID>
		<Name>T-shirts</Name>
		<CategoryUrl>http://www.myawesomestore.com/categories/mens/tshirts</CategoryUrl>
		<CategoryParentID>cat1001</CategoryParentID>
	</Category>
</Categories>
```

In this example, the first `<Category>` (*My Demo Category*) is a root category, and it has no parent category. However, it has a sub-category called *My Demo Sub-Category*. This is observed in the second `<Category>` element, which includes `CategoryParentID` element with the value as `CategoryUniqueID` of the first element.

### `<Product>` element

`<Product>` will define product stream you want to import to Olapic. In essence, we will create a `stream` entity for every `<Product>` element.

Here is a list of possible elements you can use under `<Product>`. Please pay attention to the required elements:

| Element Name | Description | Required |
|--------------|-------------|----------|
| Name | The visible name of the product in your PDP.| **Yes** |
| ProductUniqueID | This is the unique identifier of the product. We treat this as an *unique* key and your organization will use it to call our widgets in your PDP. ***Note: The value of this element can not be empty or contain white spaces.***| **Yes** |
| ProductUrl | This is the URL we use when you click "Shop this look" in an Olapic viewer. This must take the visitor to a page to purchase the item. **Include the full URL, with the schema(http/https)**. **This must be anyURI valid, see below(1)**| **Yes** |
| ImageUrl | This is the URL of the product primery image. The image that most represents your product in your PDP. **This must be anyURI valid, see below(1)**| **Yes** |
| Description | This is a short and plain text description of the product. We use this in your Olapic Admin page, your visitors will not see it. *No HTML elements are recognized in this element*. | No |
| CategoryID | This is the unique identifier of the category related with this product. We use this in the *category_based* Widget. <br>**Note: The value here should match the `CategoryUniqueID` of the associated `<Category>` element. Note: The value of this element can not be empty or contain white spaces. If you don't have a valid ID to provide, please don't include the field** | Only if you use our *category_based* widget |
| CategoriesID | Contains at least one `<CategoryID>` element. | Only if you have multiples categories associated with this product |
| EAN | European Article Number, which is used world wide for marking retail goods. Can be a string of digits either 8 or 13 characters long. | No |
| EANs | Contains at least one `<EAN>` element. | Only if you use `<EAN>` elements or *syndication* |
| UPC | Universal Product Code, which is the 6 - or 12- digit bar code used for standard retail packaging in the United States. The UPC must contain numerals only, with no letters or characters. Further, spaces and hyphens disrupt ***syndication*** matching and must be removed. | **Yes** |
| UPCs | Contains at least one `<UPC>` element. | Only if you use `<UPC>` elements or *syndication* |
| ISBN | International Standard Book Number, which is a unique identifier for books and which is intended for commercial use. The number is either 10 or 13 digits long and consists of four or five parts. The different sections of the number can be of different lengths and are usually separated by hyphens (-) or tildes (~). Spaces and hyphens disrupt ***syndication*** matching and must be removed. | **Yes** (if product has multiple UPCs) |
| ISBNs | Contains at least one `<ISBNs>` element. | Only if you use `<ISBN>` elements or *syndication* |
| Price | This is the most significative price your visitor can see in you PDP. *Do NOT include the currency*. Only include the number with decimals separeted by '.'. Example: 23.99 | No |
| Stock | This is an integer that represents your stock of this product | No |
| Availability | This is a bool representing the current status of this product. Should be consistent with your site. We can set `INACTIVE` galleries dynamically based on this value. *Expected values: {true, false, 0, 1}* | No |
| Color | This is a string with the color name of the product. Useful for color specific products. | No |
| ParentID | This is to support sub-streams levels. If this stream is not a root stream and, instead, it's a sub-stream of another stream, all you need to do is give us that ProductUniqueID here. We do the rest! Example: Color specific stream has as ParentID the non color specific stream.<br>**Note: The value of this element can not be empty or contain white spaces. If you don't have a valid ID to provide, please don't include the field** | Only if you need sub-stream support |



**Note: Fields marked as `required` must not be empty**

**(1)All URLs are of type xsd:anyURI:
URIs require that some characters be escaped with their hexadecimal Unicode code point preceded by the % character. This includes non-ASCII characters and some ASCII characters, namely control characters, spaces, and the following characters (unless they are used as deliimiters in the URI): <>#%{}|\^`. For example, ../édition.html must be represented instead as ../%C3%A9dition.html, with the é escaped as %C3%A9. However, the anyURI type will accept these characters either escaped or unescaped. With the exception of the characters % and #, it will assume that unescaped characters are intended to be escaped when used in an actual URI, although the schema processor will do nothing to alter them. It is valid for an anyURI value to contain a space, but this practice is strongly discouraged. Spaces should instead be escaped using %20.
**

### Attributes elements of `<Product>` element

We want your organization to have full control of the streams, and in order to accomplish we give you the following attributes you can include in a `<Product>` element to be able to mark as `INACTIVE` streams or even delete them.

| Attribute Name | Description | Required |
|--------------|-------------|----------|
| removed | This is a bool you can use to remove products. If you set this to `true` then we will remove the stream associated with this product. Default: false. *Expected values: {true, false, 0, 1}* | No |
| disabled | This is a bool you can use to disable products. If you set this to `true` then we will set the stream associated with this product as INACTIVE. Default: false. *Expected values: {true, false, 0, 1}* | No |

### XML Feed Example
The following is an example of a valid feed you can provide.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Feed>
	<Categories>
		<Category>
			<CategoryUniqueID>cat1001</CategoryUniqueID>
			<Name>Men's</Name>
			<CategoryUrl>http://www.myawesomestore.com/categories/mens</CategoryUrl>
		</Category>
		<Category>
			<CategoryUniqueID>cat1002</CategoryUniqueID>
			<Name>T-shirts</Name>
			<CategoryUrl>http://www.myawesomestore.com/categories/mens/tshirts</CategoryUrl>
			<CategoryParentID>cat1001</CategoryParentID>
		</Category>
	</Categories>
	<Products>
			<Product>
				<Name>Awesome Possum T-shirt</Name>
				<ProductUniqueID>APTS</ProductUniqueID>
				<ProductUrl>http://www.myawesomestore.com/products/APTS</ProductUrl>
				<ImageUrl>http://www.myawesomestore.com/images/APTS-mainstockimage.jpg</ImageUrl>
				<Description>This is an example description of my Awesome Possum T-shirt</Description>
				<CategoryID>cat1002</CategoryID>
				<UPCs>
					<UPC>001122334451</UPC>
					<UPC>001122334452</UPC>
					<UPC>001122334453</UPC>
					<UPC>001122334461</UPC>
					<UPC>001122334462</UPC>
					<UPC>001122334463</UPC>
					<UPC>001122334471</UPC>
					<UPC>001122334472</UPC>
					<UPC>001122334473</UPC>
					<UPC>001122334481</UPC>
					<UPC>001122334482</UPC>
					<UPC>001122334483</UPC>
				</UPCs>
				<Price>23.99</Price>
				<Stock>15</Stock>
				<Availability>true</Availability>
			</Product>
			<Product>
				<Name>Awesome Possum T-shirt</Name>
				<ProductUniqueID>APTS-01</ProductUniqueID>
				<ProductUrl>http://www.myawesomestore.com/products/APTS?color=01</ProductUrl>
				<ImageUrl>http://www.myawesomestore.com/images/APTS-black.jpg</ImageUrl>
				<Description>This is an example description of my Awesome Possum T-shirt</Description>
				<CategoryID>cat1002</CategoryID>
				<UPCs>
					<UPC>001122334451</UPC>
					<UPC>001122334452</UPC>
					<UPC>001122334453</UPC>
				</UPCs>
				<Price>23.99</Price>
				<Stock>5</Stock>
				<Availability>true</Availability>
				<Color>Black</Color>
				<ParentID>APTS</ParentID>
			</Product>
			<Product>
				<Name>Awesome Possum T-shirt</Name>
				<ProductUniqueID>APTS-02</ProductUniqueID>
				<ProductUrl>http://www.myawesomestore.com/products/APTS?color=02</ProductUrl>
				<ImageUrl>http://www.myawesomestore.com/images/APTS-blue.jpg</ImageUrl>
				<Description>This is an example description of my Awesome Possum T-shirt</Description>
				<CategoryID>cat1002</CategoryID>
				<UPCs>
					<UPC>001122334461</UPC>
					<UPC>001122334462</UPC>
					<UPC>001122334463</UPC>
				</UPCs>
				<Price>23.99</Price>
				<Stock>5</Stock>
				<Availability>true</Availability>
				<Color>Blue</Color>
				<ParentID>APTS</ParentID>
			</Product>
			<Product>
				<Name>Awesome Possum T-shirt</Name>
				<ProductUniqueID>APTS-03</ProductUniqueID>
				<ProductUrl>http://www.myawesomestore.com/products/APTS?color=03</ProductUrl>
				<ImageUrl>http://www.myawesomestore.com/images/APTS-red.jpg</ImageUrl>
				<Description>This is an example description of my Awesome Possum T-shirt</Description>
				<CategoryID>cat1002</CategoryID>
				<UPCs>
					<UPC>001122334471</UPC>
					<UPC>001122334472</UPC>
					<UPC>001122334473</UPC>
				</UPCs>
				<Price>23.99</Price>
				<Stock>5</Stock>
				<Availability>true</Availability>
				<Color>Red</Color>
				<ParentID>APTS</ParentID>
			</Product>
			<Product disabled="true">
				<Name>Awesome Possum T-shirt</Name>
				<ProductUniqueID>APTS-03</ProductUniqueID>
				<ProductUrl>http://www.myawesomestore.com/products/APTS?color=04</ProductUrl>
				<ImageUrl>http://www.myawesomestore.com/images/APTS-red.jpg</ImageUrl>
				<Description>This is an example description of my Awesome Possum T-shirt</Description>
				<CategoryID>cat1002</CategoryID>
				<UPCs>
					<UPC>001122334481</UPC>
					<UPC>001122334482</UPC>
					<UPC>001122334483</UPC>
				</UPCs>
				<Price>23.99</Price>
				<Stock>0</Stock>
				<Availability>false</Availability>
				<Color>Green</Color>
				<ParentID>APTS</ParentID>
			</Product>
	</Products>
</Feed>
```

#### Product Hierarchy Explained
The above contains 4 `<Product>` nodes. Typically your feed will include all the products you have stored within your e-commerce platform.

For a better understanding of the product hierarchy requirement, please refer to [this guide](http://9odg7y.axshare.com/home.html).

At the simplest level, the product hierarchy is broken down into 2 levels:

- Parent (Style level)
- Child (Color level)

Parent level `<Product>` will include the "summation" of the child products being included in the feed.

Child level `<Product>` will include the `<ParentID>` element to denote the Parent `<Product>` related to this product.

Theoretically, you can have infinite number of levels. However, please consult your Integration Engineer on best practices on the product hierarchy required by Olapic.

#### Size Variant Level 
**[!] Important Note:** We do not want any "size" specific product nodes unless it is a business requirement for the integration. Size levels are visually indistinguishable in product stock images as well as UGC, and their presence in the feed will complicate the moderation process.


## Validating your feed
By now, you should know how to build a correct feed to fully integrate with Olapic. Congratulations!

Please use the XML Schema Definition (.xsd) we have for you to validate the feed before sending it to us. You can find our schema here:

[XML Schema Definition for a valid Olapic Feed](http://photorank.me/olapicProductFeedV1_0.xsd)

**Important Note:** Please validate your feed before sending it over. You can use any tool you want to validate the feed, but we encourage you to do a schema validation using our XSD file above to make sure everything lines up correctly.

Here some tools:

* [CoreFiling](http://www.corefiling.com/opensource/schemaValidate.html) is an online tool that will ask your XML file and the XSD file (that you can download form above).
* [XML Validation](http://www.xmlvalidation.com/) is an online tool that will ask you first to paste you XML code or upload it and then you must use the checkbox *"Validate against external XML schema"* and click *"validate"* to go to the next page were you'll be asked to paste your XSD or upload it.
* `xmllint` (Command Line): you likely have xmllint installed on your machine, which can handle validation. In your terminal, type the following command:

See below for `xmllint` usage in terminal:

```sh
xmllint -noout --schema olapicProductFeedV1_0.xsd my_company_feed.xml
```

## Delivering your Product Feed

To keep a good sync between your inventory and Olapic, we need to update the feed on a scheduled basis. In order to do update regularly, we import the feed on a daily basis. The time of feed import varies, however we usually schedule our jobs to run at night or early in the morning. This way, your team can update the feed in the afternoon to have the updates in Olapic the next day.

You can make your PF available to Olapic using one of the following options:

* **SFTP Account**: Request your Integration Engineer to set up an account for your organization and then please upload your feed periodically to that account.
* **FTP Account**: Request your Integration Engineer to set up an account for your organization and then please upload your feed periodically to that account.
* **HTTP** Endpoint: Provide us with a URL where we can grab the feed. You can set HTTP Auth methods to secure the data if needed.

### Note: Feed update times

We run our feed importer every 24 hours, scheduled between 04:00 to 10:00 UTC. There is no exact time that the feed ingestion happens, so please deliver the feed before 04:00 UTC for the most up-to-date product data in Olapic.

## Providing a custom Product Feed
If you plan on giving us a custom feed (existing vendor feeds that are not Olapic specific), please be aware of the following:

* Please keep in mind that some of the product features provided by Olapic **may not** be available on Olapic using a custom feed. Specific parts of Olapic feed schema pertains to product features such as syndication, inventory updates, configurable/simple products, etc (not limited to the features mentioned).
* It must be in a XML, otherwise it can take 10 or more days to process it and we can't guarantee a correct data import. That's why we work with open and stable standards.
* We require the fields listed as **required**. You can rename them, but they are essential for a good implementation.
* We cannot guarantee a successful import with custom feeds.

### Feed Feature Support

Olapic supports many different types of feeds. However, the featureset that Olapic can provide is limited by the type of feed that you deliver to us. 

By default, Olapic standard feed will support the full feature-set. Please refer to the following table for the supported feature across 3 types of feeds, and consult your account team on which product feed would be optimal for your integration:

|                        Olapic Feature                       | Olapic Standard Feed | Google Product Feed (Standard) | Anything else |
| ----------------------------------------------------------- | -------------------- | ------------------------------ | ------------- |
| Create new products                                         | x                    | x                              | x             |
| Update existing products                                    | x                    | x                              | x             |
| Set product availability (using stock or availability flag) | x                    | x                              |               |
| Deactivate products (Availability = INACTIVE)               | x                    | x *                            |               |
| Re-activate products (Availability = OK)                    | x                    | x *                            |               |
| Remove products (delete completely)						  | x 					 |    							  | 			  |
| Extra metadata support (stock, color, price)                | x                    | x                              |               |
| Single Universal ID (UPC, EAN, ISBN, etc) Support           | x                    | x                              |               |
| Multiple Universal ID (UPC, EAN, ISBN, etc) Support         | x                    |                                |               |
| Multiple Category                                           | x                    | x                              |               |
| Category Hierarchy                                          | x                    | x                              |               |
| Category Widget Support                                     | x                    |                                |               |
| Product Hierarchy (color variants, etc)                     | x                    | x                              |               |
| Schema Validation Support                                   | x                    |                                |               |

**[!] Important Note:** We accept Google feeds that are in accordance to the [Google Merchant Center Products Feed Specification](https://support.google.com/merchants/answer/188494?hl=en) only.

**[**\***]** These features are available with the full import mode only. Please request this from your Olapic account team if you wish to enable this mode.

**Full Import Mode:** If products are removed in the next incoming feed, our import job will detect this as a signal to deactivate the product (set it to INACTIVE).


## Changing the Product Feed
Please note that if you want to change something in your product feed, we will have to be notified to make sure that the data schema does not break. Please contact your Integration Engineer if you have any changes to the feed scheduled.
