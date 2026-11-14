# Episode 1: Receipt agent

Drop a receipt photo into a OneDrive folder. Extract reads it, a plain expression checks the tax, the agent makes the one judgement call, and fixed steps write the Excel row. Teams only pings you when something's off.

## The workflow

| Step | Type | What it does |
|---|---|---|
| When a file is created | OneDrive for Business trigger | Watches your receipts folder (subfolders off, so the log never re-triggers it) |
| Extract | Extract, Receipt template | Merchant, date, subtotal, tax, total |
| TaxCheck | Expression | Tax as a share of subtotal: 12.9 to 13.1% is OK (HST), 14.9 to 14.99% is OK (Québec GST + QST), 4.9 to 5.1% is GST_ONLY, 0 is NO_TAX, no subtotal is MISSING, anything else is MISMATCH |
| Agent | Agent step, no tools | Category, a one-line English description, and an alert message only if TaxCheck failed or the total is over $150. Told never to do arithmetic |
| Add a row into a table | Excel Online (Business) | Writes the row to `Expenses.xlsx` |
| If/Else | Condition | If there's an alert message, post it in Teams |

The point of the design: fixed steps don't use AI credits and don't get the arithmetic wrong. The agent only does the judgement part.

## Set it up

1. Upload `test-files/Expenses.xlsx` to your OneDrive. It has an empty table called `Expenses`.
2. Create a folder for incoming receipts, for example `/Receipts`.
3. Import `solution/Ep01ReceiptAgent_1_0_0_1.zip` (see the main README).
4. In **copilotstudio.microsoft.com › Workflows › Snap Drop Done - Receipt Agent**, fix these steps:
   - **When a file is created:** pick your receipts folder (it says `SELECT_YOUR_FOLDER`)
   - **Add a row into a table:** pick your OneDrive and `Expenses.xlsx` (it says `SELECT_YOUR_ONEDRIVE` and `SELECT_Expenses.xlsx`). The table is already set to `Expenses`
   - **Post message in a chat or channel:** change `you@contoso.com` to your own address
5. Publish, then drop the six receipts from `test-files/receipts` into your folder.

## Break it: the six test receipts

| File | What it tests | What should happen |
|---|---|---|
| 01-grand-river-cafe.png | A normal Ontario receipt | OK, no alert |
| 02-faded-office-supply.png | A faded, hard-to-read photo | MISSING and an alert, not a guess |
| 03-cafe-vieux-quai.png | A French receipt from Québec | OK. This was the one that broke: my first TaxCheck didn't know the Québec rate, so I added a band. No AI needed |
| 04-cascade-outfitters-us.png | A US receipt with no Canadian tax | NO_TAX |
| 05-main-street-hardware.png | $15 tax on a $100 subtotal, which isn't Ontario's 13% | MISMATCH and an alert |
| 06-pinecrest-software.png | A total over $150 | OK on tax, but an alert for the amount |

A second thing these receipts caught: Québec's combined rate is 14.975%, and the hardware receipt is exactly 15%. A band of 14.9 to 15.1% lets the wrong receipt through too, so the band in this solution stops just below 15% (14.9 to 14.99%).

## Price it

My estimate before building was 20 to 25 credits a receipt, so about $0.25 at pay-as-you-go.

The real numbers from the Monitor tab:

- 6 runs, 0% errors, 39.57 credits in total. About 6.6 credits a receipt, so roughly 7 cents
- The cheapest run was 5.03 credits and the most expensive was 14.24
- On the 5.03 run, the agent step was 1.56 credits. Most of the rest was reading the receipt

To see your own: **Workflows › your workflow › Monitor**, then **Activity › select a run** for the per-step cost.
