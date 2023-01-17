<header>
<center>
<font size="+12">LibreOffice Calc</font><br/><font size="+3">Budget</font>
</center>
</header>

<main>

# Intro

This spreadsheet is born out a fruitless search to find a system meeting all these requirements:

* Uses [double-entry bookkeeping](https://www.freshbooks.com/hub/accounting/double-entry-bookkeeping)
* Supports [static budgeting](https://www.accountingtools.com/articles/what-is-a-static-budget.html)
* Supports [envelope budgeting](https://www.ramseysolutions.com/budgeting/envelope-system-explained)
* Requires (mostly) only basic journal entries
* Shows the budget and actual accounting in an easily consumable format
    * That is, cash flow across accounts quickly visible day-by-day
    * Budget vs. Actual is quickly visible month-by-month
    * Unused/Remaining budget amounts are quickly visible

## LibreOffice Calc Settings

### Required

From Tools -> Options and then LibreOffice Calc -> Calculate

* Enable regular expressions in formuals selecetd
* Search criteria = and <> must apply to whole cells checked

### Recommended

From Tools -> Options and then LibreOffice Calc -> Calculate

* Enable multi-threaded calculation checked

# Sheets

* [Info](./Docs/Info.md)
* [Chart of Accounts](./Docs/Chart%20of%20Accounts.md)
* [Transactions - Actual](./Docs/Transactions%20-%20Actual.md)
* [Actual](./Docs/Actual.md)
* [Transactions - Budget](./Docs/Transactions%20-%20Budget.md)
* [Budget](./Docs/Budget.md)
* [Budget - To-Date](./Docs/Budget%20-%20To%20-%20Date.md)
* [Budget - Projected](./Docs/Budget%20-%20Projected.md)
* [Budget vs. Actual - To-Date](./Docs/Budget%20vs.%20Actual%20-%20To-Date.md)
* [Projected](./Docs/Projected.md)
* [Cash Flow](./Docs/Cash%20Flow.md)
* [Reconciliation](./Docs/Reconciliation.md)

# Usage Examples

## Static Budgeting

Handled via journal entries on the [Transactions - Budget](./docs/Transactions%20-%20Budget.md) sheet.

This sheet also allows for creating a single journal entry and then having the amounts from that entry distributed (aka "spread") over the remainder or the month or any number of days (but not beyond the end of the month).

## Envolope Budgeting

### Using "Hard" Moves

"Hard" moves moving the money from your primary spending account (e.g., a checking account) into a savings account held at a financial institution.  Use this method of having a "hard" distinction betweeen your day-to-day funds and your envelope funds.

#### Accounts

* An account that represents a checking account held at a financial institution
    * Assets::Checking::MyBank
* An account that represents a savings account held at a financial institution
    * Assets\:\:Savings\:\:MyBank
* Multipe other accounts each reprenting an envelope (Gifts, vacation, etc.)
    * Assets\:\:Savings\:\:Gifts
    * Assets\:\:Savings\:\:Vacation
    * etc.
* A savings contra account to offset the envelope accounts
    * Assets\:\:Savings\:\:Contra Envelopes
        * Contra flag on

Of course, the accounts involved may be adjusted or altered to suit one's needs.

#### Put Money into Envelopes

* Create budget journal entries for placing funds into the virtual envelopes at various times (e.g., at the beginning of each month) that also:
    * Transfer funds into the savings account equal to the sum moved into the various envelopes
    * Transfer amounts into the contra account

For example:

| Account                               | Debit  | Credit   |
| -------                               | -----: | -------: |
| Asset\:\:Checking\:\:MyBank           |        | 200.00   |
| Asset\:\:Savings\:\:Gifts             | 100.00 |          |
| Asset\:\:Savings\:\:Vacation          | 100.00 |          |
| Asset\:\:Savings\:\:Contra Envelopes  |        | 200.00   |
| Asset\:\:Savings\:\:MyBank            | 200.00 |          |

On the [Cash Flow](./docs/Cash%20Flow.md) sheet this has the affect of showing the amount in the Savings::MyBank account with its real value and each of the envelope accounts with their real values *and* the Savings category with its net real value due to the amount in the Asset\:\:Savings\:\:Contra Envelopes account.

#### Spend money from envelopes

##### Purchase

Typically nothing is going to be purchased *directly* from funds in the Savings account.  Instead, perhaps the purchase is made using cash!  To illustrate this let's introduce a few new accounts:

* Assets\:\:Cash\:\:OnHand
* Expenses\:\:Vaaction\:\:Lodging

Purchase of a hotel stay for vactaition might then look like this:

| Account                               | Debit  | Credit   | Notes |
| -------                               | -----: | -------: | ----- |
| Expense\:\:Vacation\:\:Lodging        | 100.00 |          |       |
| Assets\:\:Cash\:\:OnHand              |        | 100.00   |       |
| Asset\:\:Savings\:\:Contra Envelopes  | 100.00 |          |       |
| Asset\:\:Savings\:\:Vacation          |        | 100.00   |  TODO: Repay $100.00 Savings\:\:Vacation -> Cash\:\:OnHand |

##### Self- Reimbursement

Note the use of the notes section as a reminder to pay back the Assets\:\:Cash\:\:OnHand.  That can be done at a later time.  For example, it may be combined with the transfer of funds to envelopes the next time that's done.  That would look like

| Account                               | Debit  | Credit   | Notes |
| -------                               | -----: | -------: | ----- |
| Assets\:\:Savings\:\:MyBank           |        | 100.00   |       |
| Assets\:\:Cash\:\:OnHand              | 100.00 |          | DONE: Repaid $100.00 Savings\:\:Vacation -> Cash\:\:OnHand from YYYY-MM-DD |

Also edit the note in the original transaction to indicate that the reimbursement has been completed and on what date.

This would work exactly the same way if the purchase was made using a Debit card tied to the checking account.  In both transactions the Assets\:\:Cash\:\:OnHand account would be replaced with the Assets\:\:Checking\:\:MyBank account.

### Using "Soft" Moves

With "soft" moves the envelope funds remain in your day-to-day (e.g. checking account).  This method does not require transferring any funds around and, therefore, requires less journal entries, including eliminating the reimbursement step.

Instead of a contra savings account we need a contra checking account

* Assets\:\:Checking\:\:\Contra Envelopes
    * Contra flag on

#### Put Money into Envelopes

| Account                               | Debit  | Credit   |
| -------                               | -----: | -------: |
| Asset\:\:Savings\:\:Gifts             | 100.00 |          |
| Asset\:\:Savings\:\:Vacation          | 100.00 |          |
| Asset\:\:Checking\:\:Contra Envelopes |        | 200.00   |

#### Spend Money from Envelope

| Account                               | Debit  | Credit   |
| -------                               | -----: | -------: |
| Expense\:\:Vacation\:\:Lodging        | 100.00 |          |
| Assets\:\:Checking\:\:MyBank          |        | 100.00   |
| Asset\:\:Checking\:\:Contra Envelopes | 100.00 |          |
| Asset\:\:Savings\:\:Vacation          |        | 100.00   |

## Credit Cards

Credit cards can be nice things given that they often come with various perks and rewards.  Using a method very similar to that shown for envelope accounting, it's possible to have quick, easy insight into the net effective funds available while using one or more credit cards.

### Purchase

To illustrate, we need a few additional accounts:

* Assets Checking::Holding@MyCard1
* Liabilities::Credit Cards::MyCard1
* Liabilities::Credit Cards::Holding@MyCard1
* Expenses::Food::Groceries

Here's an example of buying food at the grocery store:

| Account                                       | Debit  | Credit   |
| -------                                       | -----: | -------: |
| Expenses\:\:Food\:\:Groceries                 | 100.00 |          |
| Liabilities\:\:Credit Cards\:\:MyCard1        |        | 100.00   |
| Liabilities\:\:Credit Cards\:\:Contra@MyCard1 | 100.00 |          |
| Assets\:\:Checking\:\:Holding@MyCard1         |        | 100.00   |

**TIP** - Nothing precludes the use of colons within either Category or Account names, therefore, the @ sign in these accounts may be replaced by \:\:

[Cash Flow](./docs/Cash%20Flow.md) sheet will now show the actual amount on the row of the checking account and the *effective* amount on the row for the Checking category.

### Re-pay Card

When it comes time to re-pay the card, determine how much you're going to repay (assuming multiple purchases have been made) and then:

| Account                                       | Debit  | Credit   |
| -------                                       | -----: | -------: |
| Assets\:\:Checking\:\:MyBank                  |        | 500.00   |
| Liabilities\:\:Credit Cards\:\:MyCard1        | 500.00 |          |
| Liabilities\:\:Credit Cards\:\:Contra@MyCard1 |        | 500.00   |
| Assets\:\:Checking\:\:Holding@MyCard1         | 500.00 |          |

## Combined Use of Envelope Funds ("Hard" Move Method) and Credit Card Purchase

Combining the examples above, here are sample transactions to:

1. Purchase a hotel stay with a credit card
1. Reimburse that purchase from envelope funds to checking
1. Pay the credit card back from checking

### Hotel Room Purchase

| Account                                       | Debit  | Credit   | Notes |
| -------                                       | -----: | -------: | ----- |
| Expense\:\:Vacation\:\:Lodging                | 100.00 |          |       |
| Liabilities\:\:Credit Cards\:\:MyCard1        |        | 100.00   |       |
| Liabilities\:\:Credit Cards\:\:Contra@MyCard1 | 100.00 |          |       |
| Assets\:\:Checking\:\:Holding@MyCard1         |        | 100.00   |       |
| Asset\:\:Savings\:\:Contra Envelopes          | 100.00 |          |       |
| Asset\:\:Savings\:\:Vacation                  |        | 100.00   |  TODO: Repay $100.00 Savings\:\:Vacation -> Checking\:\:MyBank |

### Reimburse Savings to Checking

| Account                               | Debit  | Credit   | Notes |
| -------                               | -----: | -------: | ----- |
| Assets\:\:Savings\:\:MyBank           |        | 100.00   |       |
| Assets\:\:Checking\:\:MyBank          | 100.00 |          | DONE: Repaid $100.00 Savings\:\:Vacation -> Checking\:\:MyBank from YYYY-MM-DD |

### Re-pay card

| Account                                       | Debit  | Credit   |
| -------                                       | -----: | -------: |
| Assets\:\:Checking\:\:MyBank                  |        | 100.00   |
| Liabilities\:\:Credit Cards\:\:MyCard1        | 100.00 |          |
| Liabilities\:\:Credit Cards\:\:Contra@MyCard1 |        | 100.00   |
| Assets\:\:Checking\:\:Holding@MyCard1         | 100.00 |          |

</main>