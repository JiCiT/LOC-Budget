<header>
<font size="+12"><center>
    Transactions - Budget
</center></font>
</header>

<main>

# Purpose

Record budgeted transactions.  Creating transactions here will help you see how all your various accounts are expected to change over time.  It will also allow you to compare your actual spending to your budget.

# Usage

This spreadsheet works via double-entry bookkeeping.  That is any given transaction is going to occupy a minimum of two rows -- one with a debit amount and one with a credit amount.  The credit amonuts *must* equal the debit amounts.

## Rows

As stated above any given transaction is going to occupy two or more rows.  Leave one or more rows blank between transactions to ensure the balance column knows where a transaction ends.

## Columns

# A - Dates

The date you expect this transaction to occur.  For budgeting purposes all dates for a given transaction are typically the same, though this is not required.

## B - Description

Most typically this is the vendor, employeer, individual, etc. involved with the transaciton.  This is informational only and not used within the remainder of the spreadsheet.

## C - Account

Limited to only accounts that appear on the [Chart of Accounts](./Chart%20of%20Accounts.md) sheet.

## D - E - Debit/Credit

The transaction amount for the account on this same row.  Whether debit or credit is dependent on:

* Whether the transaction is an increase or decrease to this account
* Whether the account increases or decreases per the [Info](./Info.md) sheet.

## F - G - *HIDDEN*

These two columns record the running debit and credit amounts for each transaction so the H (Balance) column can show the correct value.

## H - Balance

Debits and Credits must balance one another out for a given transaction.  This column will indicate if the transactions are balanced or not.  If not it will show the amount of either debit (Dr) or credit (Cr) that exceeds the other.

## I - Purposely Blank

## J - Auto

Used to indicate that this transaction somehow occurs automatically (i.e., auto-pay).  It is for your informational purposes only.  It does *not* make the transaction automatically appear on the [Transactions - Actual](./Transactions%20-%20Actual.md) sheet.

## K - Spread

Using this column will cause the budgeted amount to be "spread" over a given number of days or for the rest of the month.

* X
    * Spread from transaction date through the remainder of the month
* _number_
    * Spread from the transaction date for given number of days

---

**_NOTE_** - The remainder of columns are calculated only and *not* to be overwritten.

---

## L - Intentionally Blank

## M - Days in Month

The number of days in the month from column A (Date)

## N - O - Daily (Debit/Credit)

The transaction amount divided by the number of days in the month (column M).

## P - Q - Used as of Today (Debit/Credit)

The amount of the transaction that has been "consumed" as of the current day.  Based on the transaction date, the current date, and the contents of the spread column.

## R - S - Used as of Today (Debit/Credit)

The amount of the transaction remaining to be "consumed" as of the current day.  Based on the transaction date, the current date, and the contents of the spread column.

## T - Intentionally Blank

## U - CD - Used As Of (Day of Month/Debit/Credit)

The amount of the trasaction that has been "consumed" as of the the given day of the month.  Based on the transaction date, the current date, and the contents of the spread column.

# Technical

## Styling

### Row 1

Accent 3 + Underline + Center text

### Columns I, L , and T

Accent 3

## Code

### Column F ; Rows >= 4

#### Pseudo Code

    IF {Column C; Row <current>} IS BLANK THEN                                          -- (Full account name)
        0
    ELSE
        {Column F; Row <current -1>} PLUS {Column D; Row <current -1>}                  -- (Debit balance; debit amount)

#### LOC Code (from F4)

    =IF(
          $C4 = ""
        , 0
        ,$F3 + $D4
    )

### Column G ; Rows >= 4

#### Pseudo Code

    IF {Column C; Row <current>} IS BLANK THEN                                          -- (Full account name)
        0
    ELSE
        {Column G; Row <current -1>} PLUS {Column E; Row <current -1>}                  -- (Credit balance; credit amount)

#### LOC Code (from G4)

    =IF(
          $C4 = ""
        , 0
        ,$G3 + $E4
    )

### Column H ; Rows >= 4

#### Pseudo Code

    IF
        {Column C; Row <current>) IS BLANK THEN                                         -- (Full account name)
            switch:
                {Column G; Row <current - 1>} GT {Column H; Row <current - 1>}:         -- (Debit balance; credit balance)
                    {Column G; Row <current - 1>} MINUS {Column H; Row <current - 1>}   -- (Debit balance; credit balance)
                {Column H; Row <current - 1>} GT {Column G; Row <current - 1>}:         -- (Credit balance; debit balance)
                    {Column H; Row <current - 1>} MINUS {Column G; Row <current - 1>}   -- (Credit balance; debit balance)
                TRUE:
                    "Balanced"
        ELSE
            ""

#### LOC Code (from H4)

    =IF(
          $C4=""
        , IFS(
              $G3 > $H3
            , TEXT($G3-$H3, "0.00") & " Dr"
            , $H > $G3
            , TEXT($H3-$G3, "0.00") & " Cr"
            , 1
            , "Balanced"
          )
        , ""
    )

### Column M ; Rows >= 4

#### Pseudo Code

    Days is Month of {Column A; Row <current>}

#### LOC Code (from M4)

    =DAYSINMONTH($A4)

### Column N ; Rows >= 4

#### Pseudo Code

    Round-up to two decimal places:
        {Column D; Row <current}                                        -- (Debit amount)
        divided by:
            days-in-month of {Column A; Row <current>}                  -- (Budget date)
            MINUS the day of {Column A; Row <current>}                  -- (Budget date)
            PLUS one

#### LOC Code (from N4)

    =ROUNDUP(
            $D4
          / MIN(
                $K4
              , (
                  - (
                        DAY($A4)
                      - DAYSINMONTH($A4)
                    )
                  + 1
                )
            )
        , 2
    )

### Column O ; Rows >= 4

#### Pseudo Code

    Round-up to two decimal places:
        {Column E; Row <current}                                        -- (Debit amount)
        divided by:
            days-in-month of {Column A; Row <current>}                  -- (Budget date)
            MINUS the day of {Column A; Row <current>}                  -- (Budget date)
            PLUS one

#### LOC Code (from O4)

    =ROUNDUP(
            $E4
          / MIN(
                $K4
              , (
                  - (
                        DAY($A4)
                      - DAYSINMONTH($A4)
                    )
                  + 1
                )
            )
        , 2
    )

### Column P ; Rows >= 4

#### Pseudo Code

    switch:
        {Column: A; Row: <current>} is before TODAY:                            -- (Budget date)
            zero
        (
                {Column: K; Row: <current>} is blank
            OR  End-of-Month for {Column: A; Row: <current>} is before TODAY:   -- (Budget date)
        )
            {Column: D; Row: <current>}                                         -- (Debit amount)
        True:
            Round to 2 decimal places (
                        {Column: D; Row: <current>}                             -- {Debit amount}
                DIVIDE  (
                            days-in-month( {Column: A; Row: <current>} )        -- (Budget date)
                            MINUS day of ( {Column: A; Row: <current>} )        -- (Budget date)
                            PLUS  one
                        )
                TIMES   (
                                    TODAY
                            MINUS   {Column: A; Row: <current>}                 -- (Budget date)
                            PLUS    one
                        )
            )

#### LOC Code (from Row 4)

    =IFS(
          $A4 > TODAY()
        , 0
        , OR(
              $K4=""
            , EOMONTH($A4, 0) < TODAY()
          )
        , $D4
        , 1
        , ROUNDUP(
              (
                  $D4
                / MIN(
                    $K4
                  , (
                      - (
                            DAY($A4)
                          - DAYSINMONTH($A4)
                        )
                      + 1
                    )
                  )
                * MIN(
                      $K4
                    , (DAY(TODAY()) - DAY($A4) + 1)
                  )
              )
            , 2
          )
    )

### Column Q ; Rows >= 4

#### Pseudo Code

    switch:
        {Column: A; Row: <current>} is before TODAY:                            -- (Budget date)
            zero
        (
                {Column: K; Row: <current>} is blank
            OR  End-of-Month for {Column: A; Row: <current>} is before TODAY:   -- (Budget date)
        )
            {Column: D; Row: <current>}                                         -- (Debit amount)
        True:
            Round to 2 decimal places (
                        {Column: E; Row: <current>}                             -- {Debit amount}
                DIVIDE  (
                            days-in-month( {Column: A; Row: <current>} )        -- (Budget date)
                            MINUS day of ( {Column: A; Row: <current>} )        -- (Budget date)
                            PLUS  one
                        )
                TIMES   (
                                    TODAY
                            MINUS   {Column: A; Row: <current>}                 -- (Budget date)
                            PLUS    one
                        )
            )

#### LOC Code (from Row 4)

    =IFS(
          $A4 > TODAY()
        , 0
        , OR(
              $K4=""
            , EOMONTH($A4, 0) < TODAY()
          )
        , $E4
        , 1
        , ROUNDUP(
              (
                  $E4
                / MIN(
                    $K4
                  , (
                      - (
                            DAY($A4)
                          - DAYSINMONTH($A4)
                        )
                      + 1
                    )
                  )
                * MIN(
                      $K4
                    , (DAY(TODAY()) - DAY($A4) + 1)
                  )
              )
            , 2
          )
    )

### Column R ; Rows >= 4

#### Pseudo Code

    Total debit amount MINUS used debit amount

#### LOC Code (from Row 4)

    =$D4-$P4

### Column S ; Rows >= 4

#### Pseudo Code

    Total credit amount MINUS used credit amount

#### LOC Code (from Row 4)

    =$E4-$Q4

### Column U ; Rows >= 4

#### Pseudo Code

    switch:
        {Column K; Row <current> IS BLANK:                                                              -- (Spread flag / number)
            IF {Column U; Row 1} = Day of Month of {Column A; Row <current>} THEN                       -- (Day of month) / (Transation date)
                {Column D; Row <current>}                                                               -- (Debit amount)
            ELSE
                0
        {Column K; Row <current> IS A NUMBER:                                                           -- (Spread flag / number)
            IF {Column U; Row 1} LESS THAN MINUMUM OF (                                                 -- (Day of month)
                {Column K; Row <current>}                                                               -- (Spread number)
                    -- OR --
                (
                            OPPOSITE OF (
                                        Day of month of {Column A; Row <current>}                       -- (Transaction date)
                                MINUS   Days in month of {Column A; Row <current>}                      -- (Transaction date)
                            )
                    PLUS    1
            THEN
                Round to decimal places: (
                                {Column D; Row <current>}                                               -- (Debit amount)
                    DIVIDED BY  MINIMUM OF (
                                    {Column K; Row <current>}                                           -- (Spread Nmuber)
                                        -- OR --
                                    (
                                                OPPOSITE OF (
                                                            Day of month of {Column A; Row <current>}   -- (Transaction date)
                                                    MINUS   Days in month of {Column A; Row <current>}  -- (Transaction date)
                                                )
                                        PLUS    1
                                )
                    MULTIPLY BY {Column U; Row 1}                                                       -- (Day of month)
                )
            ELSE
                0
        {Column K; Row <current>} IS NOT BLANK:                                                         -- (Spread flag)
            IF {Column U; Row 1} LESS THAN (                                                            -- (Day of month)
                OPPOSITE OF (
                            Day of month of {Column A; Row <current>}                                   -- (Transaction date)
                    MINUS   Days in month of {Column A; Row <current>}                                  -- (Transaction date)
                )
                PLUS    1
            THEN
                Round to decimal places: (
                                {Column D; Row <current>}                                               -- (Debit amount)
                    DIVIDED BY  MINIMUM OF (
                                    {Column K; Row <current>}                                           -- (Spread Nmuber)
                                        -- OR --
                                    (
                                                OPPOSITE OF (
                                                            Day of month of {Column A; Row <current>}   -- (Transaction date)
                                                    MINUS   Days in month of {Column A; Row <current>}  -- (Transaction date)
                                                )
                                        PLUS    1
                                )
                    MULTIPLY BY {Column U; Row 1}                                                       -- (Day of month)
                )
            ELSE
                0

#### LOC Code (from Row 4)

    =IFS(
          $K4 = ""
        , IF(
              U$1 = DAY($A4)
            , $D4
            , 0
          )
        , ISNUMBER($K4)
        , IFS(
              AND(
                  U$1 >= DAY($A4)
                , U$1 < DAY($A4) + MIN(
                              $K4
                            , (
                                - (
                                      DAY($A4)
                                    - DAYSINMONTH($A4)
                                  )
                                + 1
                              )
                          )
              )
            , ROUND(
                    $D4
                  / MIN(
                        $K4
                      , (
                          - (
                                DAY($A4)
                              - DAYSINMONTH($A4)
                            )
                          + 1
                        )
                    )
                  * (U$1 - DAY($A4) + 1)
                , 2
              )
            , U$1 >= DAY($A4)
            , $D4
            , 1
            , 0
          )
        , $K4 <> ""
        , IFS(
              AND(
                  U$1 >= DAY($A4)
                , U$1 < DAY($A4) + (
                        - (
                              DAY($A4)
                            - DAYSINMONTH($A4)
                          )
                        + 1
                    )
              )
            , ROUND(
                    $D4
                  / (
                      - (
                            DAY($A4)
                          - DAYSINMONTH($A4)
                        )
                      + 1
                    )
                  * (U$1 - DAY($A4) + 1)
                , 2
              )
            , U$1 >= DAY($A4)
            , $D4
            , 1
            , 0
          )
    )

### Column V ; Rows >= 4

#### Pseudo Code

    switch:
        {Column K; Row <current> IS BLANK:                                                              -- (Spread flag / number)
            IF {Column U; Row 1} = Day of Month of {Column A; Row <current>} THEN                       -- (Day of month) / (Transation date)
                {Column E; Row <current>}                                                               -- (Credit amount)
            ELSE
                0
        {Column K; Row <current> IS A NUMBER:                                                           -- (Spread flag / number)
            IF {Column U; Row 1} LESS THAN MINUMUM OF (                                                 -- (Day of month)
                {Column K; Row <current>}                                                               -- (Spread number)
                    -- OR --
                (
                            OPPOSITE OF (
                                        Day of month of {Column A; Row <current>}                       -- (Transaction date)
                                MINUS   Days in month of {Column A; Row <current>}                      -- (Transaction date)
                            )
                    PLUS    1
            THEN
                Round to decimal places: (
                                {Column E; Row <current>}                                               -- (Credit amount)
                    DIVIDED BY  MINIMUM OF (
                                    {Column K; Row <current>}                                           -- (Spread Nmuber)
                                        -- OR --
                                    (
                                                OPPOSITE OF (
                                                            Day of month of {Column A; Row <current>}   -- (Transaction date)
                                                    MINUS   Days in month of {Column A; Row <current>}  -- (Transaction date)
                                                )
                                        PLUS    1
                                )
                    MULTIPLY BY {Column U; Row 1}                                                       -- (Day of month)
                )
            ELSE
                0
        {Column K; Row <current>} IS NOT BLANK:                                                         -- (Spread flag)
            IF {Column U; Row 1} LESS THAN (                                                            -- (Day of month)
                OPPOSITE OF (
                            Day of month of {Column A; Row <current>}                                   -- (Transaction date)
                    MINUS   Days in month of {Column A; Row <current>}                                  -- (Transaction date)
                )
                PLUS    1
            THEN
                Round to decimal places: (
                                {Column E; Row <current>}                                               -- (Credit amount)
                    DIVIDED BY  MINIMUM OF (
                                    {Column K; Row <current>}                                           -- (Spread Nmuber)
                                        -- OR --
                                    (
                                                OPPOSITE OF (
                                                            Day of month of {Column A; Row <current>}   -- (Transaction date)
                                                    MINUS   Days in month of {Column A; Row <current>}  -- (Transaction date)
                                                )
                                        PLUS    1
                                )
                    MULTIPLY BY {Column U; Row 1}                                                       -- (Day of month)
                )
            ELSE
                0

#### LOC Code (from Row 4)

    =IFS(
          $K4 = ""
        , IF(
              V$1 = DAY($A4)
            , $E4
            , 0
          )
        , ISNUMBER($K4)
        , IFS(
              AND(
                  V$1 >= DAY($A4)
                , V$1 < DAY($A4) + MIN(
                              $K4
                            , (
                                - (
                                      DAY($A4)
                                    - DAYSINMONTH($A4)
                                  )
                                + 1
                              )
                          )
              )
            , ROUND(
                    $E4
                  / MIN(
                        $K4
                      , (
                          - (
                                DAY($A4)
                              - DAYSINMONTH($A4)
                            )
                          + 1
                        )
                    )
                  * (V$1 - DAY($A4) + 1)
                , 2
              )
            , V$1 >= DAY($A4)
            , $E4
            , 1
            , 0
          )
        , $K4 <> ""
        , IFS(
              AND(
                  V$1 >= DAY($A4)
                , V$1 < DAY($A4) + (
                        - (
                              DAY($A4)
                            - DAYSINMONTH($A4)
                          )
                        +1
                    )
              )
            , ROUND(
                    $E4
                  / (
                      - (
                            DAY($A4)
                          - DAYSINMONTH($A4)
                        )
                      + 1
                    )
                  * (V$1 - DAY($A4) + 1)
                , 2
              )
            , V$1 >= DAY($A4)
            , $E4
            , 1
            , 0
          )
    )

### Columns W - CD

The remaining columns mimic columns U and V just chaging any reference to column U or V to itself.

</main>