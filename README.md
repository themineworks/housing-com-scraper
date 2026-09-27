# Housing.com Scraper: India Property Prices

Scrape Housing.com property listings across 32 major Indian metros: title, BHK, price, area, address, amenities, map coordinates, and listing URL. Covers rent, buy, and commercial (rent or sale). Calls Housing.com's own internal GraphQL API. No browser, no login.

**Run it on Apify:** [apify.com/themineworks/housing-com-scraper](https://apify.com/themineworks/housing-com-scraper)
**Docs, FAQ and pricing:** [themineworks.com/actors/housing-com-scraper](https://themineworks.com/actors/housing-com-scraper/)

**Price:** $1.00 per 1,000 listings on Apify's free plan, down to $0.60 on higher plans, plus a $0.005 start fee per run. Failed and empty results are never charged.

## What it returns

* 32 major Indian metros via a verified city lookup table
* Rent, buy, and commercial (rent or sale) listing types
* Price, area, and carpet area returned as numbers for price-per-sqft
* Latitude/longitude on every record for geo-plotting
* No login or API key required

## Quick start

You need a free [Apify account](https://console.apify.com/sign-up) and its API token (Settings, API & Integrations).

### Python

```bash
pip install apify-client
```

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")
run = client.actor("themineworks/housing-com-scraper").call(run_input={
    "city": "Bangalore",
    "listingType": "rent",
    "maxResults": 10
})

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item)
```

### Node.js

```bash
npm install apify-client
```

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_TOKEN' });
const run = await client.actor('themineworks/housing-com-scraper').call({
    "city": "Bangalore",
    "listingType": "rent",
    "maxResults": 10
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

### cURL

One request that runs the actor and returns the results in the response (for runs under 5 minutes):

```bash
curl -X POST "https://api.apify.com/v2/acts/themineworks~housing-com-scraper/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"city": "Bangalore", "listingType": "rent", "maxResults": 10}'
```

### Command line

This repo includes ready-made clients that save results to JSON and CSV:

```bash
python3 housing_com_scraper.py --token YOUR_APIFY_TOKEN --city "Bangalore" --listing-type "rent" --max-results "10"
node housing_com_scraper.mjs --token YOUR_APIFY_TOKEN --city "Bangalore" --listing-type "rent" --max-results "10"
```

## Input

| Field | Type | Default | Description |
|---|---|---|---|
| `city` (required) | string | `"Bangalore"` | Indian city to search |
| `listingType` | string | `"rent"` | What kind of listings to scrape |
| `maxResults` | integer | `50` | Maximum number of listings to return |
| `allowResidentialFallback` | boolean | `true` | If a page fails every datacenter attempt, retry it once on Indian residential before giving up |

## Output

One row per result, as JSON, CSV, Excel or through the API.

| Field | Type | Description |
|---|---|---|
| `listing_id` | string | Housing.com internal listing ID |
| `title` | string | Listing title (for example '2 BHK Flat') |
| `listing_type` | string | rent, buy, commercial_rent or commercial_buy |
| `property_type` | string | Apartment, Independent House/Villa, Office Space, Commercial Property, etc |
| `bhk` | number | Number of bedrooms (BHK), parsed from the title |
| `price` | integer | Price in INR (monthly rent for rent listings, total for buy/commercial) |
| `price_label` | string | Formatted price string as shown on Housing.com (for example '₹35,000' or '₹1.04 Cr, 1.95 Cr') |
| `price_unit` | string | 'per month' for rent listings, 'total' for buy/commercial |
| `deposit` | integer | Security deposit in INR, where applicable (rent listings) |
| `area_sqft` | number | Built-up area in square feet |
| `carpet_area_sqft` | number | Carpet area in square feet, where Housing.com reports it separately |
| `address` | string | Locality / address string as shown on the listing |
| `city` | string | City searched |
| `latitude` | number | Latitude |
| `longitude` | number | Longitude |
| `amenities` | array | Readable highlight bullets for the listing (furnishing, build-up area, floors, seats, etc.) |
| `description` | string | Listing description text, HTML tags stripped |
| `posted_on` | string | Listing posted date |
| `verified` | boolean | Whether Housing.com marks the listing as verified |
| `photo_url` | string | Primary listing photo URL |
| `listing_url` | string | Full Housing.com listing URL |
| `scraped_at` | string | ISO timestamp when the record was captured |

## Use it from an AI agent

The actor works as a tool in Claude, Cursor or any MCP client through Apify's MCP server:

```
https://mcp.apify.com/?tools=themineworks/housing-com-scraper
```

## FAQ

### Which cities does it support?

32 major Indian metros selectable from a dropdown, with common aliases (Bengaluru, Gurugram, New Delhi, Bombay, Calcutta) mapped automatically.

### Does it scrape rent, buy, and commercial?

Yes. Set listingType to rent or buy for residential, or commercial_rent / commercial_buy for office space, shops, and warehouses.

### Why doesn't it accept a freeform city name?

Housing.com's search API requires an opaque internal location code per city that can't be derived from the name, so this actor ships a verified table rather than guessing.

### Does it need a login or API key?

No. It calls only Housing.com's own public search API and never touches any account.

### How much does it cost?

Pay per event: charged only for listings actually delivered, never for empty or blocked runs.

### Can I export the results to CSV or Excel?

Yes. Every run saves to an Apify dataset you can download as JSON, CSV, Excel or XML, or read through the API. The Python and Node clients in this repo also write the results to local files.

### Can I run it on a schedule?

Yes. Save your input as a task on Apify and attach a schedule, or call the API from your own cron job. Scheduled runs are billed the same way as manual ones.

## Related scrapers

* [B2B Leads Finder](https://themineworks.com/actors/b2b-leads-finder/): Business emails and LinkedIn profiles for target companies
* [LinkedIn Company Scraper](https://themineworks.com/actors/linkedin-company-details/): Company size, industry, website, and followers without login
* [Zillow Rental Listings Scraper](https://themineworks.com/actors/zillow-rental-listings/): Scrape Zillow for-rent listings by city or zip. $1 per 1,000 results

Part of [The Mine Works](https://themineworks.com/): 151 pay-per-result scrapers with no login and no browser setup on your side.

## License

MIT © The Mine Works
