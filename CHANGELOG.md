# Changelog

Changes to the server's tools and to this guide, newest first.

## 2026-11-27

- First version of this repository: setup guide per client, the `kaupakaupa-shopping` skill and its recipes.
- Server: `get_offers` now says an empty list means no match was found and gives a `reason`.
- Server: `search_products` answers list the categories of the returned products (`result_categories`).
- Server: three new tools, `get_products`, `get_price_history` and `list_markets`.
- Server: the instructions and tool descriptions warn about accessories in device searches, wrong categories, missed matches, shop-supplied brands and pictures that may not load.
