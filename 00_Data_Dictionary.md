# 00_Data_Dictionary – amazon.csv

**Dataset:** [Amazon Sales Dataset](https://www.kaggle.com/datasets/karkavelrajaj/amazon-sales-dataset) (Kaggle, karkavelrajaj), uploaded unchanged
**File:** `amazon.csv`, 1,465 rows × 16 columns, about 4.5 MB
**Grain:** one row per product listing from Amazon India (amazon.in) search results
**Snapshot date:** the file has no date column. The `qid` timestamps in `product_link` all fall on **5 January 2023**, so treat the data as a single snapshot from that day.
**Currency:** Indian Rupees (₹)

## Columns

| # | Column | Type as stored | Description | Example | Profile |
|---|---|---|---|---|---|
| 1 | `product_id` | Text | Amazon ASIN (product identifier) | `B07JW9H4J1` | 1,351 unique. 114 rows repeat an ID because the product appears in several categories or search positions |
| 2 | `product_name` | Text | Full listing title. Brand = first word (heuristic) | `Ambrane Unbreakable 60W / 3A Fast Charging…` | 1,337 unique, up to 485 characters |
| 3 | `category` | Text | Category path, levels separated by `\|` | `Computers&Accessories\|Accessories&Peripherals\|Cables&Accessories\|Cables\|USBCables` | 211 unique paths, 2–7 levels (mostly 4–5) |
| 4 | `discounted_price` | Text (₹, commas) | Selling price after discount | `₹399` | ₹39 to ₹77,990; median ₹799 |
| 5 | `actual_price` | Text (₹, commas) | List price (MRP) before discount | `₹1,099` | ₹39 to ₹139,900; median ₹1,650 |
| 6 | `discount_percentage` | Text (%) | Discount off the list price | `64%` | 0% to 94%; median 50% |
| 7 | `rating` | Text (number) | Average star rating out of 5 | `4.2` | 2.0 to 5.0; median 4.1. **1 row holds `\|` instead of a number** |
| 8 | `rating_count` | Text (commas) | Number of ratings. A proxy for traction, **not units sold** | `24,269` | 2 to 426,973; median 5,179. **2 rows are blank** |
| 9 | `about_product` | Text | Seller's feature bullets (positioning claims) | `Compatible with all Type C enabled devices…` | Up to 2,646 characters |
| 10 | `user_id` | Text (comma list) | IDs of the reviewers shown on the listing | `AG3D6O4STAQ…,AHMY5CWJ…` | Up to 8 per row |
| 11 | `user_name` | Text (comma list) | Display names of those reviewers | `Manav,Adarsh gupta,…` | Not needed for analysis |
| 12 | `review_id` | Text (comma list) | Review IDs, in the same order as `user_id` | `R3HXWT0LRP0NMF,…` | Up to 8 per row (median 8) |
| 13 | `review_title` | Text (comma list) | Short review headlines | `Satisfied,Charging is really fast,Value for money…` | **Customer-signal source** |
| 14 | `review_content` | Text (comma list) | Full review text | `Looks durable Charging is fine too…` | **Customer-signal source**, up to 18,547 characters |
| 15 | `img_link` | URL | Product image | `https://m.media-amazon.com/…` | Not needed for analysis |
| 16 | `product_link` | URL | Listing URL. Contains `ref=sr_1_N` (search rank) and `qid` (Unix timestamp) | `https://www.amazon.in/…/dp/B07JW9H4J1/ref=sr_1_1?qid=…` | All 1,465 unique |

## Top-level categories (first segment of `category`)

| Category | Rows |
|---|---|
| Electronics | 526 |
| Computers&Accessories | 453 |
| Home&Kitchen | 448 |
| OfficeProducts | 31 |
| Other (MusicalInstruments, HomeImprovement, Toys&Games, Car&Motorbike, Health&PersonalCare) | 7 |

**In scope for this project:** Electronics and Computers&Accessories (cables/chargers, earbuds/headphones, smartwatches).

## Cleaning rules (for the agent, Quick Sight or a code interpreter)

1. **Prices:** remove `₹` and `,`, then convert to a number (`₹1,099` → `1099`).
2. **Discount:** remove `%`, then convert to a number (`64%` → `64`).
3. **Rating:** convert to a number. Exclude the one row whose value is `|`; don't impute it.
4. **rating_count:** remove `,`, then convert to a number. Treat the 2 blank rows as missing, not zero.
5. **Brand:** first word of `product_name`. Check edge cases by hand, for example `AmazonBasics` vs `Amazon` and multi-word brands.
6. **Sub-category:** split `category` on `|`. Level 1 is the top-level category; the last level is the most specific.
7. **Duplicates:** to count unique products, deduplicate on `product_id` (1,351 unique).
8. **Reviews:** split the review columns on commas, but treat the result as approximate, because review text can itself contain commas.

## Known limitations

- **One snapshot from one day (5 Jan 2023).** Prices, ratings and rankings have very likely changed since.
- **Search-result sample, not a census.** Rows come from search pages (`sr_1_N`), so the data over-represents top-ranked, high-visibility products. That inflates average ratings and review counts.
- **No sales, revenue, inventory, seller or geographic data.** It can't support market-share or revenue estimates.
- **Text-formatted numbers.** Any analysis that skips the cleaning rules will mis-sort or fail.
- **Only up to 8 reviews per listing.** These are the reviews shown on the page, not the full review history.

## Evidence label

Claims drawn from this file are labelled **[Dataset]** in the Market Analysis, the Reliability Evaluation and the brief.
