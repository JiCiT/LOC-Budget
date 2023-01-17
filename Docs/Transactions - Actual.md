<header>
<font size="+12"><center>
    Transactions - Actual
</center></font>
</header>

<main>

# Purpose

Record all actual transactions

# Usage

This spreadsheet works via double-entry bookkeeping.  That is any given transaction is going to occupy a minimum of two rows -- one with a debit amount and one with a credit amount.  The credit amounts *must* equal the debit amounts.

## Rows

As stated above any given transaction is going to occupy two or more rows.  Leave one ore more rows blank between transactions to ensure the balance column knows where a transaction ends.

## Columns

### A - C - Dates

Not all dates for all parts of a given transaction need be the same.  For example, the posted date for a bank account is often different than that for an expense.

#### A - Transaction

When the transaction actually occured.

These usually are, but needn't be, the same for a given transaction set.  This is for your informational purposes only and is not used anywhere within the remainder of the spreadsheet.

#### B - Posted

The date when the transaction posted.

For example, debit and credit card purchases may not post for one to several days after a transaction actually occurs.  This should be the the date shown as posted (no longer pending).

#### C - Reconciled

This is the date from the account statement upon which the transaction appears

For example, bank and credit card statements are typically issued monthly.  The date from that statement should be here if the transaction is on the statement.

### E - Description

Most typically this is the vendor, employeer, individual, etc. involved with the transaction.  This is informational only and not used within the remainder of the spreadsheet.

### F - Account

Limited to only accounts that appear on the [Chart of Accounts](./Chart%20of%20Accounts.md) sheet.

### G - H - Debit/Credit

The transaction amount for the account on this same row.  Whether debit or credit is dependent on:

* Whether the transaction is an increase or decrease to this account
* Whether the account increases or decreases per the [Info](./Info.md) sheet.

### I - J - *HIDDEN*

These two columns record the running debit and credit amounts for each transaction so the K (Balance) column can show the correct value.

### K - Balance

Debits and Credits must balance one another out for a given transaction.  This column will indicate if the transactions are balanced or not.  If not it will show the amount of either debit (Dr) or credit (Cr) that exceeds the other.

### L - Purposely Blank

### M - Notes

Use to record any other notes you'd like about the transaction.  Informational only.  Not used within the remainder of the spreadsheet.

# Technical

## Styling

### Rows 1 - 2

Accent 3 + Underline + Center text

### Columns D and L

Accent 3

## Data Validity

### Column F

#### Criteria

* Allow: Cell Range
* Allow Empty Cells: False
* Show selection list: True
* Sort entries ascending: True
* Source: $'Chart of Accounts'.$X$3:$'Chart of Accounts'.$X$165

#### Input Help

None

#### Error Alert

* Action: Stop
* Title:
* Error Message:

## Code

### Column I

#### Pseudo Code

    IF {Column F; Row <current>} IS BLANK THEN                                          -- (Full account name)
        0
    ELSE
        {Column J; Row <current -1>} PLUS {Column H; Row <current -1>}                  -- (Debit balance; debit amount)

#### LOC Code

    =IF(
          $F4 = ""
        , 0
        ,$J3 + $H4
    )

### Column J

#### Pseudo Code

    IF {Column F; Row <current>} IS BLANK THEN                                          -- (Full account name)
        0
    ELSE
        {Column J; Row <current -1>} PLUS {Column H; Row <current -1>}                  -- (Credit balance; credit amount)

#### LOC Code

    =IF(
          $F4 = ""
        , 0
        ,$J3 + $H4
    )

### Column K

#### Pseudo Code

    IF
        {Column F; Row <current>) IS BLANK THEN                                         -- (Full account name)
            switch:
                {Column I; Row <current - 1>} GT {Column J; Row <current - 1>}:         -- (Debit balance; credit balance)
                    {Column I; Row <current - 1>} MINUS {Column J; Row <current - 1>}   -- (Debit balance; credit balance)
                {Column J; Row <current - 1>} GT {Column I; Row <current - 1>}:         -- (Credit balance; debit balance)
                    {Column J; Row <current - 1>} MINUS {Column I; Row <current - 1>}   -- (Credit balance; debit balance)
                TRUE:
                    "Balanced"
        ELSE
            ""
            
#### LOC Code (from K4)

    =IF(
          $F4=""
        , IFS(
              $I3 > $J3
            , TEXT($I3-$J3, "0.00") & " Dr"
            , $J3 > $I3
            , TEXT($J3-$I3, "0.00") & " Cr"
            , 1
            , "Balanced"
          )
        , ""
    )

</main>