# Phase 1: Getting the data

The phase ends when, and only when, you can list every table with its actual name, actual column names, and actual record count, read from the source (Iron rule R1). Until you can, you do not have access, no matter how plausible the data sounds.

## Try, in order

1. **If they pasted a link and you have a tool that can genuinely read it** (a connector, an integration, or an MCP tool for that product), read everything: table list, columns, column descriptions, views, and all records. Then prove access per R1.
2. **If they gave you files or a folder path** (this environment can read local files), read them all directly. An Excel file may carry many tabs; treat each tab as a candidate table.
3. **If you cannot genuinely read the link**, say so in one sentence, without jargon and without apologizing at length, then give them export instructions for their exact product:
   - **Airtable**: open each table, click the view name, "Download CSV". One file per table. Bring them all here.
   - **Google Sheets**: File > Download > Microsoft Excel (.xlsx). One file carries all the tabs.
   - **Notion**: open the database page, "..." menu > Export > "Markdown & CSV".
   - **Lark / Feishu Base**: open each table, "..." menu > Download as Excel.
   - **Excel**: just bring the file itself.
   - **A real database someone set up**: ask that person for "a CSV export of each table". Those exact words are enough for them.

Give only the instructions for the product they actually named. Do not print the whole menu.

## Accept mess

Oddly named files, half-exported tables, extra sheets: take what arrives, then ask for what is obviously missing ("your invoices mention job numbers, but I do not see a list of jobs; is there one?"). Never refuse a messy export; mess is material for the findings list, not a reason to stop.

## What exports destroy

A CSV or Excel export carries none of the owner's column descriptions, notes, or saved views. If you are reading through a connector, capture those now, while you can. If you are working from an export, remember this for Phase 2: you will ask for the missing context cheaply there, not here.
