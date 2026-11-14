# Build It. Break It. Price It.

The builds from my Copilot Studio series, ready to import into your own environment.

Each episode follows the same pattern: build a real workflow, try to break it with test files (including the ones that fail), then work out what it costs in Copilot Credits. This repo has the solution I exported from my dev environment, the test files I used, and the numbers I got.

| Folder | What it is | Agent's job |
|---|---|---|
| [ep01-receipt-agent](ep01-receipt-agent) | Drop a receipt photo in OneDrive, get an Excel row and a Teams ping when something's off | Category, one-line description, whether to alert |
| [ep02-invoice-vs-po](ep02-invoice-vs-po) | Invoice vs purchase order checker. Clean invoices never reach the agent | Explain exceptions only |
| [ep04-contract-clause-checker](ep04-contract-clause-checker) | Reads every page of a supplier contract, schedules included, and flags risky clauses | Risk level, risky clauses, summary, next step |
| [patterns-expense-review-agent](patterns-expense-review-agent) | An expense policy agent with an 8-question evaluation gate, plus a workflow where the agent proposes a change and a person approves it | Answer from the policy, refuse exceptions, propose but never commit |

## Before you import

- A Power Platform environment with Dataverse, where you can create solutions. Use a dev or sandbox environment, not production.
- Copilot Studio with **Workflows** available in that environment. Workflows, the Agent step and Extract are new, so check they show up in your tenant before importing.
- Copilot Credits. I used pay-as-you-go billing at $0.01 a credit.
- Connections for OneDrive for Business, Excel Online (Business) and Microsoft Teams.

## Importing a solution

1. Download the `.zip` from the episode's `solution` folder.
2. Go to **make.powerapps.com › Solutions › Import solution** and pick the zip.
3. When asked, choose or create a connection for each connection reference.
4. Open the workflow in **copilotstudio.microsoft.com › Workflows**. I removed my own folder, workbook and email from the steps, so each step marked `SELECT_...` or `you@contoso.com` needs you to pick your own. The episode README lists which ones.
5. Publish, then drop the test files into your folder.

All the solutions are unmanaged, so you can change anything.

## What's not in here

- My tenant details. Folder IDs, file IDs, connection names and email addresses were replaced with placeholders.
- Video files. The walkthroughs are on the YouTube channel.

## Licence

MIT. Use it, change it, break it. If you find a better way to do something, open an issue.

The test receipts, invoices and contracts are made up. Any resemblance to a real business is a coincidence.
