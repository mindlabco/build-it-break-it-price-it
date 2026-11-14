# Episode 2: Invoice vs PO checker

Most invoices don't need an AI agent. This workflow checks each invoice against a purchase order register with one plain expression. Clean invoices are logged and the workflow stops. Only exceptions reach the agent.

## The workflow

| Step | Type | What it does |
|---|---|---|
| When a file is created | OneDrive for Business trigger | Watches your invoices folder |
| Extract | Extract, Invoice template | Supplier, invoice number, PO number, subtotal, total |
| List rows present in a table | Excel Online (Business) | Looks up the PO in `PO-Register.xlsx`. The PO number is normalised first: "PO", "#", "-" and spaces are stripped, then matched against the `PONumberKey` column |
| MatchCheck | Expression | NO_PO if nothing matched, CLOSED if the PO is closed, OVER if the subtotal is more than 2% above the approved amount, otherwise OK |
| If/Else | Condition | OK: log the row and stop. Anything else: go to the agent |
| Agent | Agent step, no tools | A one-line exception summary, the next step and a priority. Told not to recalculate anything |
| Add a row / Post message | Excel + Teams | Logs every invoice, and pings Teams for exceptions |

## Set it up

1. Upload `test-files/PO-Register.xlsx` and `test-files/Invoice-Log.xlsx` to your OneDrive.
2. Create a folder for incoming invoices, for example `/Invoices`.
3. Import `solution/Ep02InvoicevsPOChecker_1_0_0_1.zip`.
4. In **copilotstudio.microsoft.com › Workflows › Invoice vs PO Checker - Ep 02**, fix these steps:
   - **When a file is created:** pick your invoices folder
   - **List rows present in a table:** pick your OneDrive and `PO-Register.xlsx` (table `PORegister`)
   - **Both Add a row steps:** pick your OneDrive and `Invoice-Log.xlsx` (table `InvoiceLog`)
   - **Post message in a chat or channel:** change `you@contoso.com` to your own address
5. Publish, then drop the six invoices from `test-files/invoices` into your folder.

## Break it: the six test invoices

| File | Expected |
|---|---|
| 01-kestrel-it-services-invoice.pdf | OK, never reaches the agent |
| 02-riverbend-electrical-invoice.pdf | OVER its PO |
| 03-maple-ridge-print-house-invoice.pdf | NO_PO |
| 04-grey-owl-office-furniture-invoice.pdf | CLOSED PO |
| 05-copper-lane-catering-invoice.pdf | OK, never reaches the agent |
| 06-northbank-supply-co-invoice.pdf | Should be OK. This is the one that broke |

Invoice 06 says "PO #10442" and the register says "PO-10442". Same purchase order, different punctuation, and an exact-match lookup doesn't know that. The fix was the normalisation in the lookup step, not AI.

## Price it (estimated, pay-as-you-go at $0.01 a credit)

- A clean invoice was about $0.13, because it only pays for the read
- An exception was about $0.20, the read plus the agent
- Clean invoices finished in about 20 seconds, against 33 to 49 seconds with the agent

If 8 in 10 of your invoices are clean, 8 in 10 never pay for the agent.
