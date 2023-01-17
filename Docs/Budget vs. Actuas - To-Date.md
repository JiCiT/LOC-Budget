<header>
<font size="+12"><center>
    Budget vs. Actual - To Date
</center></font>
</header>

<main>

# Purpose

For each month and each account shows the *budgeted* accounting through the current day, the *actual* accounting and the difference between the two.

When the the actual amount is "better" than the budgeted amount, the difference is highlighted with the "Good" styling.

When the the actual amount is "worse" than the budgeted amount, the difference is highlighted with the "Bad" styling.

# Usage

NO DATA ENTRY  -- This sheet is for informational display only.

# Technical

## Styling

## Rows 1 & 2

Accent 3 + Underline + Center text

### Conditional

#### A1:AMJ1048576

##### Condition 1

Formula is:

    INDIRECT(ADDRESS(2, COLUMN()))<>""

Apply Style: Accent 3

#### A3:AMJ1048576

##### Condition 2

Formula is:

    INDIRECT(ADDRESS(ROW(), 2)) <> ""

Apply Style: Accent 2

##### Condition 3

Formula is:

    AND((CELL("CONTENTS") * IF(VLOOKUP(INDIRECT("$'Chart of Accounts'.$V$" & ROW()), $Info.$A$5:$Info.C$9, 3, 1) = ">0", 1, -1)) > 0, INDIRECT(ADDRESS(2, COLUMN())) = "Diff") = 1

Apply Style: Good

#### Condition 4

Formula is:

    AND((CELL("CONTENTS") * IF(VLOOKUP(INDIRECT("$'Chart of Accounts'.$V$" & ROW()), $Info.$A$5:$Info.C$9, 3, 1) = ">0", 1, -1)) < 0, INDIRECT(ADDRESS(2, COLUMN())) = "Diff") = 1

Apply Style: Bad


## Code

### All Columns; Rows 3 - 200

#### Pseudo Code

    switch:
        {Column <current>} is 1:
            {'Chart of Accounts'; Column Q; Row <current>}                                                                                          -- (Account Type)
        {Column <current>} is 2:
            {'Chart of Accounts'; Column R; Row <current>}                                                                                          -- (Category 1)
        {Column <current>} is 3:
            {'Chart of Accounts'; Column T; Row <current>}                                                                                          -- (Category 2)
        {Column <current>}; Row: 2} is "Budget":
            Switch:
                {Column A; Row <current>} is not empty:                                                                                             -- (Account Type)
                    Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Account Type)
                {Column B; Row <current>} is not empty:                                                                                             -- (Category 1)
                    Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Category 1)
                {Column C; Row <current>} is not empty:                                                                                             -- (Category 2)
                            (
                                            Sum of {'Transactions - Budget'; Column L; rows 3 - 5000}                                               -- (Debits - Used)
                                                Where
                                                           {'Transactions - Budget'; Column C; Row <corresponding>}                                 -- (fully-qualified account name)
                                                        == {'Chart of Accounts'; Column X; Row <corresponding>}                                     -- (fully-qualified account name)
                                                    AND    {'Transactions - Budget'; Column A; Row <corresponding>}                                 -- (posted date)
                                                        >=  DATEVALUE(
                                                                  {'Info'; Column B; Row 1}                                                         -- (Budget year)
                                                                & {Column: <current>; Row: 1}                                                       -- (Budget month)
                                                                & "-01"                                                                             -- (day)
                                                            )                                                                                       -- (first of month)
                                                    AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                            -- (posted date)
                                                        <=  EOMONTH(
                                                                  DATE(
                                                                      {'Info'; Column B; Row 1}                                                     -- (Budget year)
                                                                    & {Column: <current>; Row: 1}                                                   -- (Budget month)
                                                                    , "-01"                                                                         -- (day)
                                                                  )
                                                                , 0
                                                            )                                                                                       -- (end of budget date month)
                                MULTIPLY BY {Chart of Accounts; Column: Z, Row: <current>}                                                          -- (contra account flag)
                                MULTIPLY BY (
                                                IF account increases by debits THEN
                                                    1
                                                ELSE
                                                    -1
                                            )
                            )
                    PLUS
                            (
                                            Sum of {'Transactions - Budget'; Column M; rows 3 - 5000}                                               -- (Credits - Used)
                                                Where
                                                           {'Transactions - Budget'; Column C; Row <corresponding>}                                 -- (fully-qualified account name)
                                                        == {'Chart of Accounts'; Column X; Row <corresponding>}                                     -- (fully-qualified account name)
                                                    AND    {'Transactions - Budget'; Column A; Row <corresponding>}                                 -- (posted date)
                                                        >=  DATEVALUE(
                                                                  {'Info'; Column B; Row 1}                                                         -- (Budget year)
                                                                & {Column: <current>; Row: 1}                                                       -- (Budget month)
                                                                & "-01"                                                                             -- (day)
                                                            )                                                                                       -- (first of month)
                                                    AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                            -- (posted date)
                                                        <=  EOMONTH(
                                                                  DATE(
                                                                      {'Info'; Column B; Row 1}                                                     -- (Budget year)
                                                                    & {Column: <current>; Row: 1}                                                   -- (Budget month)
                                                                    , "-01"                                                                         -- (day)
                                                                  )
                                                                , 0
                                                            )                                                                                       -- (end of budget date month)
                                MULTIPLY BY {Chart of Accounts; Column: Z, Row: <current>}                                                          -- (contra account flag)
                                MULTIPLY BY (
                                                IF account increases by credits THEN
                                                    1
                                                ELSE
                                                    -1
                                            )
                            )
                TRUE:
                    zero
        {Column <current>}; Row: 2} is "Actual":
            Switch:
                {Column A; Row <current>} is not empty:                                                                                             -- (Account Type)
                    Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Account Type)
                {Column B; Row <current>} is not empty:                                                                                             -- (Category 1)
                    Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Category 1)
                {Column C; Row <current>} is not empty:                                                                                             -- (Category 2)
                        (
                                            (
                                                If {Info; {Column B; Row 2) is TRUE THEN                                                            -- (Include actuals after today)
                                                    (
                                                        Sum of {'Transactions - Acutal'; Column G; rows 3 - 5000}                                   -- (Debits)
                                                            Where
                                                                       {'Transactions - Actual'; Column F; Row <corresponding>}                     -- (fully-qualified account name)
                                                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                         -- (fully-qualified account name)
                                                                AND    {'Transactions - Budget'; Column A; Row <corresponding>}                     -- (posted date)
                                                                    >=  DATEVALUE(
                                                                              {'Info'; Column B; Row 1}                                             -- (Budget year)
                                                                            & {Column: <current>; Row: 1}                                           -- (Budget month)
                                                                            & "-01"                                                                 -- (day)
                                                                        )                                                                           -- (first of month)
                                                                AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                -- (posted date)
                                                                    <=  EOMONTH(
                                                                              DATE(
                                                                                  {'Info'; Column B; Row 1}                                         -- (Budget year)
                                                                                & {Column: <current>; Row: 1}                                       -- (Budget month)
                                                                                , "-01"                                                             -- (day)
                                                                              )
                                                                            , 0
                                                                        )                                                                           -- (end of budget date month)
                                                    )
                                                ELSE
                                                    (
                                                        Sum of {'Transactions - Acutal'; Column G; rows 3 - 5000}                                   -- (Debits)
                                                            Where
                                                                       {'Transactions - Actual'; Column F; Row <corresponding>}                     -- (fully-qualified account name)
                                                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                         -- (fully-qualified account name)
                                                                AND    {'Transactions - Budget'; Column A; Row <corresponding>}                     -- (posted date)
                                                                    >=  DATEVALUE(
                                                                              {'Info'; Column B; Row 1}                                             -- (Budget year)
                                                                            & {Column: <current>; Row: 1}                                           -- (Budget month)
                                                                            & "-01"                                                                 -- (day)
                                                                        )                                                                           -- (first of month)
                                                                AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                -- (posted date)
                                                                    <=  EOMONTH(
                                                                              DATE(
                                                                                  {'Info'; Column B; Row 1}                                         -- (Budget year)
                                                                                & {Column: <current>; Row: 1}                                       -- (Budget month)
                                                                                , "-01"
                                                                              )
                                                                            , 0
                                                                        )
                                                                AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                -- (posted date)
                                                                    <=  TODAY
                                                    )
                                            )
                                MULTIPLY BY (
                                                IF account increases by debits THEN
                                                    1
                                                ELSE
                                                    -1
                                            )
                            )
                    PLUS
                            (
                                            (
                                                If {Info; {Column B; Row 2) is TRUE THEN                                                            -- (Include actuals after today)
                                                    (
                                                        Sum of {'Transactions - Acutal'; Column H; rows 3 - 5000}                                   -- (Credits)
                                                            Where
                                                                       {'Transactions - Actual'; Column F; Row <corresponding>}                     -- (fully-qualified account name)
                                                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                         -- (fully-qualified account name)
                                                                AND    {'Transactions - Budget'; Column A; Row <corresponding>}                     -- (posted date)
                                                                    >=  DATEVALUE(
                                                                              {'Info'; Column B; Row 1}                                             -- (Budget year)
                                                                            & {Column: <current>; Row: 1}                                           -- (Budget month)
                                                                            & "-01"                                                                 -- (day)
                                                                        )                                                                           -- (first of month)
                                                                AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                -- (posted date)
                                                                    <=  EOMONTH(
                                                                              DATE(
                                                                                  {'Info'; Column B; Row 1}                                         -- (Budget year)
                                                                                & {Column: <current>; Row: 1}                                       -- (Budget month)
                                                                                , "-01"                                                             -- (day)
                                                                              )
                                                                            , 0
                                                                        )                                                                           -- (end of budget date month)
                                                    )
                                                ELSE
                                                    (
                                                        Sum of {'Transactions - Acutal'; Column H; rows 3 - 5000}                                   -- (Credits)
                                                            Where
                                                                       {'Transactions - Actual'; Column F; Row <corresponding>}                     -- (fully-qualified account name)
                                                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                         -- (fully-qualified account name)
                                                                AND    {'Transactions - Budget'; Column A; Row <corresponding>}                     -- (posted date)
                                                                    >=  DATEVALUE(
                                                                              {'Info'; Column B; Row 1}                                             -- (Budget year)
                                                                            & {Column: <current>; Row: 1}                                           -- (Budget month)
                                                                            & "-01"                                                                 -- (day)
                                                                        )                                                                           -- (first of month)
                                                                AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                -- (posted date)
                                                                    <=  EOMONTH(
                                                                              DATE(
                                                                                  {'Info'; Column B; Row 1}                                         -- (Budget year)
                                                                                & {Column: <current>; Row: 1}                                       -- (Budget month)
                                                                                , "-01"
                                                                              )
                                                                            , 0
                                                                        )
                                                                AND     {'Transactions - Budget'; Column A; Row <corresponding row>}                -- (posted date)
                                                                    <=  TODAY
                                                    )
                                            )
                                MULTIPLY BY (
                                                IF account increases by credits THEN
                                                    1
                                                ELSE
                                                    -1
                                            )
                            )
                TRUE:
                    zero
        {Column <current>; Row 2} is "diff":
                  {Column <current - 1>; Row <current>}                                                                                             -- (actual)
            MINUS {Column <current - 2>; Row <current>}                                                                                             -- (budget)
        TRUE:
            zero

#### Actual Code (from Column E, Row 3)

    =IFS(
          COLUMN() = 1
        , $'Chart of Accounts'.$Q3
        , COLUMN() = 2
        , $'Chart of Accounts'.$R3
        , COLUMN() = 3
        , $'Chart of Accounts'.$T3
        , E$2 = "Budget"
        , IFS(
              $A3<>""
            , SUMIFS(
                  INDIRECT(
                      ADDRESS(ROW() + 1, COLUMN())
                    & ":"
                    & ADDRESS(
                          ROW() + MATCH(".+",$A4:$A$200,0) - 1
                        , COLUMN()
                      )
                  )
                , INDIRECT(
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
                , INDIRECT(
                      ADDRESS(ROW() + 1, 3)
                    & ":"
                    & ADDRESS(ROW() + MATCH(".+",$B4:$B$200,0) - 1, 3)
                  )
                , "=.+"
              )
            , $C3<>""
            ,   (
                    SUMIFS(
                          $'Transactions - Budget'.$L$3:$L$5000
                        , $'Transactions - Budget'.$C$3:$C$5000
                        , "=" & $'Chart of Accounts'.$X3
                        , $'Transactions - Budget'.$A$3:$A$5000
                        , ">=" & DATEVALUE($Info.$B$1 & "-" & E$1 & "-01")
                        , $'Transactions - Budget'.$A$3:$A$5000
                        , "<=" & EOMONTH($Info.$B$1 & "-" & E$1 & "-01", 0)
                      )
                    * $'Chart of Accounts'.$Z3
                    * IF(
                          VLOOKUP(
                              $'Chart of Accounts'.$V3
                            , $Info.$A$5:$Info.$B$9
                            , 2
                            , 1
                          ) = "Dr"
                        , 1
                        , -1
                    )
                )
              + (
                    SUMIFS(
                          $'Transactions - Budget'.$M$3:$M$5000
                        , $'Transactions - Budget'.$C$3:$C$5000
                        , "=" & $'Chart of Accounts'.$X3
                        , $'Transactions - Budget'.$A$3:$A$5000
                        , ">=" & DATEVALUE($Info.$B$1 &"-" & E$1 & "-01")
                        , $'Transactions - Budget'.$A$3:$A$5000
                        , "<=" & EOMONTH($Info.$B$1 & "-" & E$1 & "-01", 0)
                    )
                    * $'Chart of Accounts'.$Z3
                    * IF(
                          VLOOKUP(
                              $'Chart of Accounts'.$V3
                            , $Info.$A$5:$B$9
                            , 2
                            , 1
                          ) = "Cr"
                        , 1
                        , -1
                    )
                )
            , 1
            , ""
          )
        , E$2 = "Actual"
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
            ,   (
                    IF (
                          $'Info'.$B$2 = TRUE()
                        , SUMIFS(
                              $'Transactions - Actual'.$G$3:$G$5000
                            , $'Transactions - Actual'.$F$3:$F$5000
                            , "=" & $'Chart of Accounts'.$X3
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , ">=" & DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01)
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , "<=" & EOMONTH(DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01), 0)
                          )
                        , SUMIFS(
                              $'Transactions - Actual'.$G$3:$G$5000
                            , $'Transactions - Actual'.$F$3:$F$5000
                            , "=" & $'Chart of Accounts'.$X3
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , ">=" & DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01)
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , "<=" & EOMONTH(DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01), 0)
                            , $'Transactions - Actual'.$B$3:$B$5000
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
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , ">=" & DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01)
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , "<=" & EOMONTH(DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01), 0)
                          )
                        , SUMIFS(
                              $'Transactions - Actual'.$H$3:$H$5000
                            , $'Transactions - Actual'.$F$3:$F$5000
                            , "=" & $'Chart of Accounts'.$X3
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , ">=" & DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01)
                            , $'Transactions - Actual'.$B$3:$B$5000
                            , "<=" & EOMONTH(DATE($'Info'.$B$1; MONTH("01-" & D$1 & "-01"); 01), 0)
                            , $'Transactions - Actual'.$B$3:$B$5000
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
            , 1
            , ""
          )
        , E$2 = "Diff"
        , IFERROR(D3 - C3, "")
        , 1
        , ""
    )

</main>