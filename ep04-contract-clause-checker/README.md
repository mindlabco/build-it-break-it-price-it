# Episode 4: Contract clause checker

Nobody reads the schedules at the back of a contract. This workflow reads every page, schedules included, and flags the clauses that could cost you money or lock you in. A person makes the final call.

## The workflow

| Step | Type | What it does |
|---|---|---|
| When a file is created | OneDrive for Business trigger | Watches your contracts folder |
| Extract | Extract, with two custom fields | `supplierName`, and `contractText`: "The full text of the contract, word for word" |
| Agent | Agent step, no tools | Risk level (High, Medium or Low), the risky clauses, a plain summary and a recommended action. The instructions list what to look for and what each risk level means |
| Add a row into a table | Excel Online (Business) | Logs it in `Contract-Log.xlsx` |
| If/Else | Condition | If it's High, ping a person in Teams before anyone signs |

The agent has no tools and doesn't approve anything.

## Set it up

1. Upload `test-files/Contract-Log.xlsx` to your OneDrive. It has an empty table called `ContractLog`.
2. Create a folder for incoming contracts, for example `/Contracts`.
3. Import `solution/Ep04ContractClauseChecker_1_0_0_1.zip`.
4. In **copilotstudio.microsoft.com › Workflows › Contract Clause Checker - Ep 04**, fix these steps:
   - **When a file is created:** pick your contracts folder
   - **Add a row into a table:** pick your OneDrive and `Contract-Log.xlsx`
   - **Post message in a chat or channel:** change `you@contoso.com` to your own address
5. Publish, then drop the six contracts from `test-files/contracts` into your folder.

The agent's instructions are written for a made-up customer, Larkspur Studio Ltd. Change the name and the list of risky terms to suit your own business.

## Break it: the six test contracts

| File | What's in it | Expected |
|---|---|---|
| Brightwave-Cloud-Subscription-Agreement.docx | A clean, balanced agreement | Low |
| Ironclad-Facility-Services-Agreement.docx | Unlimited indemnity, $500 liability cap | High |
| Summit-Print-Equipment-Lease.docx | 36-month auto-renewal | High |
| Northgate-Payroll-Services-Agreement.docx | Data held offshore, used to train products | High |
| Tidewater-Data-Analytics-Agreement.docx | A clean main body with the problems hidden in Schedule C, after the signatures | High |
| Halcyon-Marketing-Services-Agreement.docx | Planted as a Medium | The agent said High, because the media spend had no cap |

My first version handed the agent the Word file straight from the trigger. It couldn't read it, because a .docx is a zipped package, not text. It failed safe and said "High, unable to assess, have a person review it", but that run cost about 6 credits and told me nothing. Putting Extract first fixed it.

## Price it (estimated, pay-as-you-go at $0.01 a credit)

About 9 credits per contract, so roughly $0.09. Extract is most of it and the review itself is under 2 credits. Each run took 30 to 45 seconds.
