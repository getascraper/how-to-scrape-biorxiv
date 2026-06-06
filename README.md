# How to Scrape bioRxiv and medRxiv Preprints in Node.js

Extract bioRxiv and medRxiv preprints into RAG-ready JSON using the [bioRxiv + medRxiv Scraper for RAG](https://apify.com/devanshlive/biorxiv-medrxiv-rag-extractor) actor on Apify -- no browser automation or proxies required.

## What this example does

- Calls the bioRxiv + medRxiv Scraper for RAG actor via the Apify client
- Passes server selection, category, and date range
- Waits for the run to complete
- Fetches results from the actor's dataset
- Prints each preprint to the console

## Prerequisites

- [Node.js](https://nodejs.org/) v18 or higher
- An [Apify account](https://console.apify.com/sign-up) (free tier available)
- An [Apify API token](https://console.apify.com/settings/integrations)

## Installation

```bash
npm install
```

## Environment setup

```bash
cp .env.example .env
```

Open `.env` and replace `your_apify_token_here` with your actual Apify API token.

## Usage

```bash
npm start
```

## Code example

```javascript
import { ApifyClient } from 'apify-client';
import 'dotenv/config';

const client = new ApifyClient({
  token: process.env.APIFY_TOKEN,
});

const input = {
  servers: ['biorxiv', 'medrxiv'],
  category: 'neuroscience',
  dateFrom: '2024-01-01',
  dateTo: '2024-01-07',
  maxPreprints: 10,
};

const run = await client.actor('devanshlive/biorxiv-medrxiv-rag-extractor').call(input);

console.log('Results from dataset');
console.log(`Check your data here: https://console.apify.com/storage/datasets/${run.defaultDatasetId}`);
const { items } = await client.dataset(run.defaultDatasetId).listItems();
items.forEach((item) => {
  console.dir(item);
});
```

## Example output

See `sample-output.json` for a full example. Each preprint includes:

| Field | Description |
|-------|-------------|
| `doi` | DOI (primary identifier, e.g. `10.1101/2024.03.15.585219`) |
| `server` | `biorxiv` or `medrxiv` |
| `version` | Preprint version returned by the API |
| `title` | Preprint title |
| `abstract` | Abstract as returned by the bioRxiv API |
| `authors` | Author display names in the order the API returned them |
| `category` | Server-specific category slug |
| `publication_date` | YYYY-MM-DD posting date |
| `preprint_url` | Canonical preprint landing page |
| `license` | Normalized license key: `cc_by`, `cc_by_nc`, `cc0`, `none`, or null |
| `source` | `full_text` or `abstract` (text origin) |
| `chunks` | Token-aware chunks for RAG |
| `chunks[].idx` | 0-indexed position |
| `chunks[].text` | Chunk text |
| `chunks[].tokens` | Token count (≤ 512) |

## Use cases

- **Preprint monitoring:** Track the fastest-moving biomedical evidence before peer review
- **Drug discovery AI:** Build RAG corpora from preprints for target identification
- **Clinical evidence:** Extract medRxiv preprints for clinical decision support
- **Embedding pipelines:** Feed pre-chunked text into vector databases for biomedical Q&A

## Try the actor on Apify

[Open the bioRxiv + medRxiv Scraper for RAG on Apify](https://apify.com/devanshlive/biorxiv-medrxiv-rag-extractor)

## Related resources

- [bioRxiv API documentation](https://api.biorxiv.org/)
- [medRxiv API documentation](https://api.medrxiv.org/)
- [Apify Client for JavaScript](https://docs.apify.com/api/client/js/)

## License

MIT
