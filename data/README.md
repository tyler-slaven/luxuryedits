# Bag catalog

Enter purse data in `bags.csv`. Keep the header row as:

```csv
id,name,price,brand,edit,tier,image_name
```

Example row:

```csv
london-001,Mayfair Top Handle,1500.00,Example House,london,Rare,mayfair-top-handle.webp
```

- `id` must be unique and may contain letters, numbers, hyphens, and underscores.
- `price` is entered in USD. The generated JSON stores it as integer cents, so `1500.00` becomes `150000`.
- `image_name` is only the filename. Upload that file to `images/`; the JSON automatically adds `./images/` to its `image` value.
- Image filenames cannot contain spaces or folders and must end in `.avif`, `.jpeg`, `.jpg`, `.png`, or `.webp`.
- Names containing commas can be quoted using normal CSV syntax, such as `"Classic Bag, Small"`.
- `edit` should be `london`, `new-york`, `milan`, or `paris`. Values such as `London Edit` and `New York Edit` are also normalized correctly by the page.

The page loads `bags.json` when it starts. If an Edit has CSV records, those records replace that Edit's built-in demo purses. An empty or unavailable JSON file leaves the demo purses in place.

## Odds table

Control the tier probabilities independently in `odds.csv`. Keep its header row as:

```csv
tier,edit,price_range,odds
```

Each row assigns a decimal probability to one tier in one Edit. The `odds` values for each Edit included in the file must add up to exactly `1.00`. For example, `0.35` means that the tier receives 35% of that Edit's total probability. When a tier contains multiple purses, its probability is split evenly between them. You can add Edits one at a time; Edits not yet present in the table keep their built-in fallback odds.

Use `price_range` as an inventory check in the form `1500-4999` or `5000+`; amounts are USD without currency symbols or commas. A purse outside its tier's configured range produces a browser console warning but remains eligible.

Every tier used in `bags.csv` should have a matching row for that Edit in `odds.csv`. Inventory with no matching odds row receives 0%. If a configured tier has no available purses, the remaining available tier odds are proportionally normalized to 100% so a reveal can still complete.

Run the converter locally from the repository root with:

```sh
bash scripts/csv-to-json.sh
bash scripts/odds-to-json.sh
```

On GitHub, `.github/workflows/build-bag-data.yml` runs whenever either CSV changes. It overwrites the matching JSON files and commits them back to the same branch. The page reads the generated JSON files, so edit the CSV source files rather than editing JSON by hand.
