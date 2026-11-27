# Changelog

Changes to the server's tools and to this guide, newest first.

## 2026-11-27

- First version of this repository: setup guide per client, the `kaupakaupa-shopping` skill and its recipes.
- Server: `get_offers` now says an empty list means no other listing can be shown, and gives a `reason`.
- Server: `search_products` answers list the categories of the returned products (`result_categories`).
- Server: three new tools, `get_products`, `get_price_history` and `list_markets`.
- Server: the instructions and tool descriptions warn about accessories in device searches, wrong categories, missed matches, shop-supplied brands and pictures that may not load.

## 2026-11-27 (later)

- Skill: sort a price-sorted page yourself, set a price floor on any price sort, read `get_shop` for the free-delivery threshold, and do not read a missing `sale_spans` as "no price drop". From a test of the README examples with two assistants.
