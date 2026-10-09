# US Dog and Cat Food Recalls (FDA, 2012 onward)

Every dog and cat food recall the US Food and Drug Administration has classified since 8 June 2012: 266 recalls covering 1,232 products, from 2012-07-11 to 2026-09-24. Published by [PerBowl](https://perbowl.com/), which builds pet food records from public sources.

FDA publishes these recalls only through its Data Dashboard and Enforcement Report search, with no bulk download for pet food. This dataset puts them in one place and adds:

- a reason category for each recall (Salmonella, excess vitamin D, pentobarbital, ...) and the species
- lot codes for 1,232 products, from FDA's Enforcement Reports
- barcodes (UPC/EAN) found in 607 product descriptions
- links to brands, makers and plants, each reviewed and backed by evidence

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23255873.svg)](https://doi.org/10.5281/zenodo.23255873)

**Version 2026-10-09** · FDA records as loaded on 2026-10-05 · Dataset page: https://perbowl.com/recalls/dataset/ · Search the recalls: https://perbowl.com/recalls/

## Files

### `recalls.csv` (266 rows)

One row per recall event: a firm's recall of one or more products, as FDA classified it.

| Column | Type | Description |
|---|---|---|
| `event_id` | integer | FDA recall event number (Enforcement Report). Joins every file. |
| `classified_date` | date | Date FDA classified the recall (earliest across its products). |
| `initiated_date` | date | Date the firm started the recall (earliest across its products), from FDA's Enforcement Report. |
| `terminated_date` | date | Date FDA ended the recall: the latest product termination, filled only once every product is terminated. |
| `fda_class` | string | Class I (most serious), Class II or Class III. |
| `status` | string | Active if any product is still ongoing at FDA, else Closed. |
| `firm` | string | Recalling firm, as FDA records it. |
| `firm_city` | string | Recalling firm's city (FDA). |
| `firm_state` | string | Recalling firm's state (FDA). |
| `species` | string | dog, cat or both (dog;cat), from the product descriptions. Empty when no product names either. |
| `reason_category` | string | PerBowl's category for the reason, e.g. Salmonella, Excess vitamin D. Several are joined with ";". |
| `reason_text` | string | FDA's reason for recall, verbatim. |
| `distribution` | string | FDA's distribution pattern, verbatim (shortened to 400 characters). |
| `product_count` | integer | Number of product rows in products.csv for this event. |
| `maker` | string | The pet food maker this recall is linked to. Empty unless the link has evidence. |
| `maker_evidence` | string | name = the recalling firm is that maker (reviewed name match); plant = same FDA facility number as one of its licensed plants. |
| `plant` | string | The licensed plant linked to this recall, if any, as "Maker, City, ST". |
| `plant_evidence` | string | fei = same FDA facility number; trace = a published investigation (see recall_traces.csv). |
| `maker_url` | url | The maker's page on PerBowl. |
| `plant_url` | url | The plant's page on PerBowl. |
| `brands` | string | Brands linked after a person read the product list (see recall_brands.csv). Joined with ";". |
| `fda_url` | url | FDA's record for the first product in the event. |
| `perbowl_url` | url | The recall's page on PerBowl. |

### `products.csv` (1,232 rows)

One row per recalled product in FDA's record.

| Column | Type | Description |
|---|---|---|
| `event_id` | integer | FDA recall event number. |
| `product_no` | integer | Position of the product within its event (1, 2, ...). |
| `description` | string | FDA's product description, verbatim (shortened to 900 characters). |
| `upcs` | string | Barcodes (UPC/EAN) found in the description, digits only, joined with ";". |
| `lot_codes` | string | Lot codes, best-by dates and other codes from FDA's Enforcement Report "Code Info" field. |
| `fda_class` | string | Class I, II or III for this product. |
| `status` | string | FDA status for this product: Ongoing, Completed or Terminated. |
| `recall_number` | string | FDA recall number, e.g. V-166-2012 (V = veterinary). |
| `initiated_date` | date | Date the firm started this product's recall. |
| `report_date` | date | Date the recall appeared in FDA's weekly Enforcement Report. |
| `terminated_date` | date | Date FDA terminated this product's recall. Empty while it is open. |
| `quantity` | string | Amount of product recalled, as the firm reported it (units vary: cases, bags, pounds). |
| `voluntary` | string | FDA's voluntary or mandated field, verbatim. |
| `firm_notified_by` | string | How the firm first told its customers or the public (letter, telephone, press release, ...). |
| `fda_url` | url | FDA's record for this product. |

### `recall_brands.csv` (63 rows)

One row per brand a person confirmed in a recall's product list. A word match alone never makes a link.

| Column | Type | Description |
|---|---|---|
| `event_id` | integer | FDA recall event number. |
| `brand` | string | Brand name on PerBowl. |
| `perbowl_url` | url | The brand's page on PerBowl. |

### `excluded.csv` (2,428 rows)

Every FDA veterinary recall row the dog-and-cat filter dropped, with the reason, so the filter can be checked.

| Column | Type | Description |
|---|---|---|
| `event_id` | integer | FDA recall event number. |
| `product_id` | integer | FDA product number. |
| `classified_date` | date | Date FDA classified it. |
| `firm` | string | Recalling firm (FDA). |
| `description` | string | FDA's product description (shortened to 300 characters). |
| `excluded_because` | string | Why it is not in recalls.csv. |
| `fda_url` | url | FDA's record. |

### `recall_traces.csv` (3 rows)

Plants named by a published investigation (for example a CDC report), not by FDA's recall record.

| Column | Type | Description |
|---|---|---|
| `event_id` | integer | FDA recall event number. |
| `plant` | string | The plant, as "Maker, City, ST". |
| `source_title` | string | The investigation that names the plant. |
| `source_url` | url | Where to read it. |

## How it was built

Built and checked by Jeremiah Say (https://perbowl.com/about/jeremiah-say/).

| Step | Rows |
|---|---:|
| FDA veterinary recall rows, June 2012 on | 3,660 |
| Left out: animal drug, device or non-food product | −1,277 |
| Left out: not dog or cat food | −554 |
| Left out: livestock feed or food for other animals | −439 |
| Left out: recalled by an animal-drug maker or pharmacy | −158 |
| **Kept: dog and cat food and treats** (266 recalls) | **1,232** |

Every row left out is in `excluded.csv` with its reason. Recall records come from FDA's Data Dashboard export; lot codes, recall numbers, dates and quantities from FDA's Enforcement Report exports, matched by event and product description. Reason categories and species come from fixed word rules; brand, maker and plant links are reviewed one by one.

All files are UTF-8 CSV with a header row. `event_id` joins them. `datapackage.json` describes the same columns in the Frictionless Data format.

## What it does not cover

- **Starts in June 2012.** That is the first date in FDA's online recall records. It ends with the latest recall FDA had classified when PerBowl last loaded its records (5 Oct 2026).
- **Classified recalls only.** A recall a company has announced but FDA has not yet classified is not in it yet. PerBowl's recall alerts cover those from the announcement.
- **Dog and cat food and treats only.** Animal drugs, livestock feed and food for other animals are left out, as PerBowl's methodology explains. A product that names a dog or cat is always kept.
- **Lot codes can lag.** They come from FDA's Enforcement Report exports, which PerBowl downloads by hand. A recall classified after the last export can have empty lot codes until the next release.
- **Links are sparse on purpose.** Each maker, plant and brand link needs evidence: a reviewed name, the same FDA facility number, a published investigation, or a person reading the product list. An empty field means no evidence yet, not that there is no link.

## How to cite

> PerBowl (2026). US Dog and Cat Food Recalls (FDA, 2012 onward), version 2026-10-09 [Data set]. https://perbowl.com/recalls/dataset/. https://doi.org/10.5281/zenodo.23255873

Use it for anything, including commercially, under CC BY 4.0: credit PerBowl and link to https://perbowl.com/recalls/dataset/ (or cite the DOI). The FDA fields are in the public domain. See `LICENSE.md` and `CITATION.cff`.

## Sources

- FDA Data Dashboard, Recalls (Veterinary): https://datadashboard.fda.gov/oii/cd/recalls.htm
- FDA Enforcement Reports: https://www.accessdata.fda.gov/scripts/ires/
- How PerBowl checks facts: https://perbowl.com/methodology/

## Corrections

Found a mistake? Report it at https://perbowl.com/corrections/ and it is fixed in the next release.
