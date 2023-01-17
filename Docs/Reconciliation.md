<header>
<font size="+12"><center>
    Reconciliation
</center></font>
</header>

<main>

# Purpose

For every day of the year and each account, shows sum of opening balances and the *reconciled* accounting (using the Reconciled date from the [Transactions - Actual ](./docs/Transactions%20-%20-Actual) sheet.

# Usage

NO DATA ENTRY  -- This sheet is for informational display only.

# Technical

## Styling

### Row 1

Accent 3 + Underline + Center text

## Conditional

### A1:AMJ1048576

#### Condition 1

Formula is:

    INDIRECT(ADDRESS(ROW(), 1)) <> ""

Apply Style: Accent 1

#### Condition 2

Formula is:

    INDIRECT(ADDRESS(ROW(), 2)) <> ""

Apply Style: Accent 2

## Code

### All Columns; Rows 3 - 200

#### Pseudo Code

    switch:
        {Column: <current>} = 1:
            {Chart of Accounts; Column Q; Row: <current>}                           -- (Account Type)
        {Column: <current>} = 2:
            {Chart of Accounts; Column R; Row: <current>}                           -- (Category 1)
        {Column: <current>} = 3:
            {Chart of Accounts; Column S; Row: <current>}                           -- (Category 2)
        {Column: <current>; Row: 1} is a number (__NOTE__ - dates are numbers):     -- (Cash Flow date)
            switch:
                {Column: A; Row: <current>} is not blank:
                    Sum of:
                                {Column: <current>; Row: <current  + 1>}
                        through {Column: <current>; Row: <one less than the first row found *after* the current with non-blank in Column <current>>
                        where:
                                {Column: A; Row: <corresponding>} is not blank
                {Column: B; Row: <current>} is not blank:
                    Sum of:
                                {Column: <current>; Row: <current  + 1>}
                        through {Column: <current>; Row: <one less than the first row found *after* the current with non-blank in Column <current>>
                        where:
                                {Column: A; Row: <corresponding>} is not blank
                {Column: C; Row: <current>} is not blank:
                            /*
                             * OPENING BALANCE
                             /*
                            {Chart of Accounts; Column: Y; Row: <current>}
                            /*
                             *  __END__  - OPENING BALANCE
                             /*
                            /*
                             * __START__ - ACTUALS
                             */
                    PLUS    (
                                        (
                                                        (
                                                            IF {Info; Column B; Row 2} IS TRUE:                         -- (Include Actuals After "TODAY"):
                                                                Sum of:
                                                                            {Transactions - Actual; Column G; Row 3}                        -- (Debits)
                                                                    through {Transactions - Actual; Column G; Row 5000}
                                                                    where:
                                                                                    {Transactions - Actual; Column F; Row <corresponding>}  -- (Full account name)
                                                                            EQUALS  {Chart of Accounts; Column X; Row: <current>            -- (Full account name)
                                                                        AND         {Transactions - Actual; Column B; Row <corresponding>}  -- (Date posted)
                                                                            LTE     {Column: <current>; Row 1}                              -- (Cash Flow date)
                                                            ELSE:
                                                                Sum of:
                                                                            {Transactions - Actual; Column G; Row 3}                        -- Debits)
                                                                    through {Transactions - Actual; Column G; Row 5000}
                                                                    where:
                                                                                    {Transactions - Actual; Column F; Row <corresponding>}  -- (Full account name)
                                                                            EQUALS  {Chart of Accounts; Column X; Row: <current>            -- (Full account name)
                                                                        AND         {Transactions - Actual; Column B; Row <corresponding>}  -- (Date posted)
                                                                            LTE     {Column: <current>; Row 1}                              -- (Cash Flow date)
                                                                        AND         {Transactions - Actual; Column B; Row <corresponding>}  -- (Date posted)
                                                                            LTE     "TODAY"
                                                        )
                                            MULTIPLY BY (
                                                            IF account type raises by debits:
                                                                1
                                                            ELSE
                                                                -1
                                                        )
                                        )
                                PLUS    (
                                                        (
                                                            IF {Info; Column B; Row 2} IS TRUE:                         -- (Include Actuals After "TODAY"):
                                                                Sum of:
                                                                            {Transactions - Actual; Column H; Row 3}                        -- (Credits)
                                                                    through {Transactions - Actual; Column H; Row 5000}
                                                                    where:
                                                                                    {Transactions - Actual; Column F; Row <corresponding>}  -- (Full account name)
                                                                            EQUALS  {Chart of Accounts; Column X; Row: <current>            -- (Full account name)
                                                                        AND         {Transactions - Actual; Column B; Row <corresponding>}  -- (Date posted)
                                                                            LTE     {Column: <current>; Row 1}                              -- (Cash Flow date)
                                                            ELSE:
                                                                Sum of:
                                                                            {Transactions - Actual; Column H; Row 3}                        -- (Credits)
                                                                    through {Transactions - Actual; Column H; Row 5000}
                                                                    where:
                                                                                    {Transactions - Actual; Column F; Row <corresponding>}  -- (Full account name)
                                                                            EQUALS  {Chart of Accounts; Column X; Row: <current>            -- (Full account name)
                                                                        AND         {Transactions - Actual; Column B; Row <corresponding>}  -- (Date posted)
                                                                            LTE     {Column: <current>; Row 1}                              -- (Cash Flow date)
                                                                        AND         {Transactions - Actual; Column B; Row <corresponding>}  -- (Date posted)
                                                                            LTE     "TODAY"
                                                        )
                                            MULTIPLY BY (
                                                            IF account type raises by credits:
                                                                1
                                                            ELSE
                                                                -1
                                                        )
                                        )
                            )
                    /*
                     *  __END__  - ACTUALS
                     */
                TRUE:
                    "" (blank)
        TRUE:
            "" (blank)

#### LOC Code (from Column E; Row 3)

    =IFS(
          COLUMN() = 1
        , $'Chart of Accounts'.$Q3
        , COLUMN() = 2
        , $'Chart of Accounts'.$R3
        , COLUMN() = 3
        , $'Chart of Accounts'.$T3
        , ISNUMBER(E$1)
        , IFS(
              $A3<>""
            , SUMIFS(
                  INDIRECT(
                      ADDRESS(ROW() + 1, COLUMN())
                    & ":"
                    & ADDRESS(ROW() + MATCH(".+",$A4:$A$200,0) - 1, COLUMN())
                  )
                , INDIRECT (
                      ADDRESS(ROW() + 1, 3)
                    & ":"
                    & ADDRESS(ROW() + MATCH(".+",$A4:$A$200,0) - 1, 3)
                  )
                , "=.+"
              )
            , $B3<>""
            , SUMIFS(
                  INDIRECT(
                      ADDRESS(ROW() + 1, COLUMN())
                    & ":"
                    & ADDRESS(ROW() + MATCH(".+",$B4:$B$200,0) - 1, COLUMN())
                  )
                , INDIRECT (
                    ADDRESS(ROW() + 1, 3)
                    & ":"
                    & ADDRESS(ROW() + MATCH(".+",$B4:$B$200,0) - 1, 3)
                  )
                , "=.+"
              )
            , $C3<>""
            ,   $'Chart of Accounts'.$Y3
              + (
                    (
                        IF (
                              $'Info'.$B$2 = TRUE()
                            , SUMIFS(
                                  $'Transactions - Actual'.$G$3:$G$5000
                                , $'Transactions - Actual'.$F$3:$F$5000
                                , "=" & $'Chart of Accounts'.$X3
                                , $'Transactions - Actual'.$C$3:$C$5000
                                , "<=" & E$1
                              )
                            , SUMIFS(
                                  $'Transactions - Actual'.$G$3:$G$5000
                                , $'Transactions - Actual'.$F$3:$F$5000
                                , "=" & $'Chart of Accounts'.$X3
                                , $'Transactions - Actual'.$C$3:$C$5000
                                , "<=" & E$1
                                , $'Transactions - Actual'.$C$3:$C$5000
                                , "<=" & TODAY()
                              )
                        ) * IF(
                              VLOOKUP(
                                  $'Chart of Accounts'.$V3
                                , $'Info'.$A$5:$'Info'.$B$9
                                , 2
                                , 1
                              ) = "Dr"
                            , 1
                            , -1
                        )
                    )
                  + (
                        IF (
                              $'Info'.$B$2 = TRUE()
                            , SUMIFS(
                                  $'Transactions - Actual'.$H$3:$H$5000
                                , $'Transactions - Actual'.$F$3:$F$5000
                                , "=" & $'Chart of Accounts'.$X3
                                , $'Transactions - Actual'.$C$3:$C$5000
                                , "<=" & E$1
                              )
                            , SUMIFS(
                                  $'Transactions - Actual'.$H$3:$H$5000
                                , $'Transactions - Actual'.$F$3:$F$5000
                                , "=" & $'Chart of Accounts'.$X3
                                , $'Transactions - Actual'.$C$3:$C$5000
                                , "<=" & E$1
                                , $'Transactions - Actual'.$C$3:$C$5000
                                , "<=" & TODAY()
                              )
                        ) * IF(
                              VLOOKUP(
                                  $'Chart of Accounts'.$V3
                                , $'Info'.$A$5:$'Info'.$B$9
                                , 2
                                , 1
                              ) = "Cr"
                            , 1
                            , -1
                        )
                    )
                )
            , 1
            , ""
          )
        , 1
        , ""
    )

</main>