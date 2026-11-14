# Episode 3: Inbox triage, fixed step vs agent

Twelve customer emails, two ways to label them. A fixed Extract step returns one of five categories. An Agent step returns the same five categories plus a one-sentence reason. Both run on every email, both answers go into Excel side by side, and complaints ping Teams.

## The workflow

| Step | Type | What it does |
|---|---|---|
| When a new email arrives | Outlook trigger | Inbox, subject filter `[TRIAGE TEST]`. Concurrency control on: 5 at a time, 20 waiting |
| Extract | Extract step, one text field | `category`: exactly one of Billing, Change request, Complaint, Spam, Other, with a one-line definition of each |
| Agent | Agent step, no tools | Same five categories, a few rules (urgent or foreign-language invoices are Billing, sarcasm is a Complaint, two asks means the one that needs action first, reply-all chatter is Other) and a one-sentence reason |
| Add a row into a table | Excel Online (Business) | Subject, sender, both labels, the agent's reason, time |
| If/Else | Condition | Agent says Complaint |
| Post message in a chat or channel | Teams | Pings you about the complaint |

## Set it up

1. Upload `test-files/Triage-Log.xlsx` to your OneDrive. The `Match` column is a formula that compares the two labels.
2. Import `solution/Ep03InboxTriage_1_0_0_2.zip`.
3. In **copilotstudio.microsoft.com › Workflows › Inbox Triage - Ep 03**, fix these steps:
   - **When a new email arrives:** check the folder is your Inbox
   - **Add a row into a table:** pick your OneDrive and `Triage-Log.xlsx` (table `TriageLog`)
   - **Post message in a chat or channel:** change `you@contoso.com` to your own address
4. Publish, then send yourself the 12 emails in `test-files/test-emails.md`.

## Break it

**1. Five lines into one box.** My first build used a Classify step with five branches, and I drew all five into the Agent. The canvas showed five connections. Only the "Other" branch actually ran the Agent. Every Billing, Change request, Complaint and Spam email finished with the Agent and Excel steps marked Skipped, and the run still said Succeeded. 11 of my 12 emails never reached the log. Workflow branches don't merge back together. If every branch does the same thing next, you don't need branches: get the label from a single step and carry on in one line. That's why this build uses Extract.

**2. One stuck run held up the inbox.** I set concurrency to 1 while chasing the problem above. Then one Agent call (the "Service down" email) sat on Running for 17 minutes with no error, and every email behind it waited. I cancelled it, resent the email and it finished in seconds. The Agent step has no timeout setting that I could find, so concurrency now runs 5 at a time and I check Activity for anything stuck on Running.

**3. The hard emails were fine.** Sarcasm, French, a scary "FINAL NOTICE" that's a real invoice, and two requests in one email. Extract and the Agent agreed on all 12, and both matched what I expected.

## Price it (estimated, pay-as-you-go at $0.01 a credit)

| Step | Credits per email | Cost |
|---|---|---|
| Extract (label only) | about 2.7 to 2.9 | about $0.028 |
| Agent (label plus a reason) | about 1.5 | about $0.015 |
| Classify (first build, one sample) | about 1.6 | about $0.016 |
| Whole run, both steps, Excel and Teams | about 4.6 to 4.9 | about $0.05 |

The step I expected to be cheap cost the most. On these short emails the Agent was about half the price of Extract and gave a reason as well. Check the per-step credits in **Activity** before you decide which step is the "cheap" one. Your numbers will depend on email length and model.
