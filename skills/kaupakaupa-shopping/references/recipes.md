# Recipes

Each recipe lists the calls in order. Amounts are examples in ISK for the `is` market. Product ids are whatever the previous call returned.

## Best price for a named device

Example request: "What is the best price for an iPhone 18 Pro?"

1. Search with a price floor so accessories drop out.

   ```json
   {"query": "iPhone 18 Pro", "price_min": {"amount": 100000, "currency": "ISK"}, "sort": "price_asc", "page_size": 20}
   ```

2. Read `result_categories`. If the page is still mostly accessories, raise the floor. Do not add a category filter yet: a shop may have filed the phone under the wrong category.
3. Pick one listing and call `get_offers` with its id.
4. If the offers list is empty or short, treat the search hits as the candidates. Keep only the same model and variant (check storage size in the names; some shops leave it out, so say so when you cannot tell).
5. Call `get_products` with the candidate ids.
6. Present shop, price, delivery, total and price-check date for each, cheapest total first. Say which rows you matched by name.

## A gift within a budget

Example request: "A Christmas gift for a woman who likes outdoor sports and fitness, 10,000 to 30,000 ISK."

1. Run two or three searches with different keywords, each with the price range and `in_stock_only`. Use Icelandic and English words ("hlaupajakki", "running jacket", "jógadýna").

   ```json
   {"query": "hlaupajakki", "price_min": {"amount": 10000, "currency": "ISK"}, "price_max": {"amount": 30000, "currency": "ISK"}, "in_stock_only": true, "page_size": 20}
   ```

2. Use the `facets` in the answer to narrow: target gender, size and colour come back with ids you can pass in `attributes`.
3. Sorting a whole category by `popularity` is rarely useful for gifts; keyword searches inside the budget give better shortlists.
4. Build a shortlist from several shops. Call `get_products` once for the shortlist to add delivery facts.
5. Show the picture only if it loads. Say when a price is stale.

## Is this sale real?

Example request: "Is this Black Friday price actually a discount?"

1. `get_product` for the product. Note `price`, `discount_price`, `lowest_30d` and `is_lowest_price_30d`.
2. `get_price_history` with `range` `90d`.

   ```json
   {"product_id": "<id>", "range": "90d"}
   ```

3. Read the points oldest first. Each point holds until the next one. A real sale shows a higher price holding for weeks before the drop. A price that rose shortly before the "sale" is a warning sign. Say what you see.
4. If the answer's state is `too_new`, there are fewer than 14 days of history: say there is not enough history to judge. If it is `unavailable`, there is no history to show.
5. `get_offers` (or the name-search fallback) to see whether another shop is cheaper anyway.

## Splitting a shopping list across shops

Example request: "I need these five things. How do I pay the least including delivery?"

1. For each item, find the candidates (search, then `get_offers` or the fallback).
2. Fetch all candidates with `get_products`, 10 ids per call.
3. Group by shop. For each shop add the prices of the items you would buy there; if the sum reaches the shop's `free_over`, delivery is free, otherwise add `home_delivery_from` once per shop.
4. Compare a few groupings: everything from the single cheapest shop, each item from its cheapest shop, and one where a second item is moved to a shop to reach its free-delivery threshold.
5. Present the best grouping with a per-shop subtotal. Delivery figures are floors, and those with `is_estimate` are typical figures, so say the total is an estimate.

## Shopping locally with pickup

Example request: "Where can I pick this up today in Reykjavík?"

1. Find the candidates as above and keep those with `delivery.pickup_available` true. False means unknown, so do not rule a shop out on it; say you could not confirm pickup.
2. Call `get_shop` with each shop's `domain` to read its `city`.
3. Stock on the day and opening hours are not in the data. Tell the user to confirm with the shop through the link.

## Exploring a category

1. `browse_categories` with no `parent_code` lists the top level. Pass a returned `code` as `parent_code` to go deeper.
2. `product_count` is the size of the catalogue under that category, not the number of products currently on sale there. A small count can open an empty list.
3. Use a `code` in `category_codes` with `search_products`. Remember that a product can be filed under the wrong category.
