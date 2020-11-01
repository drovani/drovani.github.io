---
layout: post
title: Overcoming Limitations with Shopify Fulfillments and Pricing
category: "Rovani in C&sharp;"
tags:
- shopify
- azurefunctions
- cosmosdb
---

BlueBolt recently launched a new Shopify store for a Canadian client that sells products to customer in their home country, in the United States, and in the European Union. The client's pricing needs were more complex than what Shopify allows, their fulfillment prioritization required customization not included in Shopify's base functionality, and their new order management system was unable to natively support these requirements. Despite these issues, BlueBolt was able to deliver a solution that overcame these challenges using a clever set of tools. This post will detail the specifics of the issues and the solutions we created to overcome them.

## Currency-specific Pricing

Natively, Shopify allows for a store to [display prices in multiple currencies](https://help.shopify.com/en/manual/payments/shopify-payments/multi-currency). However, it only allows for one price to be set (in the store's currency) and then market rate conversion will adjust that price to the "presentment price" shown in the customer's local currency. This is very useful for stores that just want to display the price in a foreign currency but do not have specific pricing for various regions. Our client needs to be able to set specific prices for each of their three currencies (CAD, USD, EUR).

For example, an item might cost C$12.99. With a conversion rate of 0.752, that would be US$9.77. Shopify allows the store to set rounding rules, so it could be rounded up to US$9.99. However, because of tariffs or manufacturing cost differences, the store needs to charge US$10.99. Similarly, a conversion rate of 0.636 to the Euro would be &euro;8.32; perhaps with a rounding rule, bringing it to &euro;8.90. Again, this may not match what the customer needs to be charged. Additionally, if there is a printed price in a catalog or advertisement, that price needs to be consistent on the website. Even if exchange rates drastically change, the price the customer pays needs to match the price advertised.

Shopify's current solution for a merchant is to build three different stores - one for each currency. However, this brings into play usability problems with orders being spread across several stores, admin/marketing users having to update content on multiple stores, and the OMS needing to sync with the three stores.

## Locale-specific Inventory and Fulfillment

Another challenge with operating three different Shopify storefronts in one Shopify instance is in the way Shopify handles inventory tracking and fulfillment. Shopify allows a store to have multiple locations and lets the [store prioritize which location fulfills orders](https://help.shopify.com/en/manual/locations/setting-up-your-locations#set-the-priority-of-locations-for-fulfilling-orders). This works for merchants that have one location that fulfills most of the orders and a back-up warehouse that will handle overload. This isn't the case for our client. They have one warehouse in each territory (CA, US, EU) - inventory is tracked independently from each warehouse and orders placed in the territory must be fulfilled from that warehouse.

An example situation on how Shopify would normally handle order fulfillment: for a given SKU of PROD1, the merchant may have 27 in Canada, 19 in the US, and 4 in Europe. Regardless of where the product is shipping to, when a customer orders 17 units, they will be shipped out of the primary location (Canada). If the next customer orders 13 units, they will be shipped out of the secondary location (US - because Canada only has 10 units remaining). It does not matter if the order is being shipped to Victoria, BC or Turin, Italy. While the fulfillment location can be manually changed after the order is placed, this sets significant manual intervention requirements on the part of the merchant to ensure products are shipping from the correct warehouse.

The client's order management system has the correct inventory levels for all warehouses and is fully capable of setting inventory levels for a SKU in each of the warehouse locations. The problem being that Shopify would not intellegently handle fulfillment.

## Solution Part 1: Variant-Specific Pricing

For each SKU in the product catalog, we have created three copies. Each of the variants has a suffix with the country-code that it applies to. Using some magic on the front-end (details later), we select the appropriate variant to utilize and then use the numeric value for the price (and compare-at price) to display to the customer.

|Customer Location|Variant Used|Price|Compare At|Fulfillment Location|
|-|-|-|-|-|
|Canada           |    PROD1-CA|12.99|     15.99|Oakville, Ontario   |
|United States    |    PROD1-US|10.99|     13.99|Buffalo, NY         |
|European Union   |    PROD1-EU| 7.99|      9.90|Bleiswijk, NL       |
{: .bordered }

