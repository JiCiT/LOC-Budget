<header>
<font size="+12"><center>
    Actual
</center></font>
</header>

<main>

# Purpose

For each account shows the *actual* accounting (ignoring any budgeted amounts).  For every account and every month, it shows:

* The account balance at the beginning of the month
* The net tally of debits and credits for the month
* The account balance at the end of the month

# Usage

NO DATA ENTRY  -- This sheet is for informational display only.

# Technical

## Styling

### Row 1

Accent 3 + Underline + Center text

### Conditional

#### A1:AMJ1048576

##### Condition 1

Formula is:

    NOT(ISERROR(MONTH("01-" & INDIRECT(ADDRESS(1, COLUMN())) & "-01")))

Apply Style: Accent 3

#### A3:AMJ1048576

##### Condition 1

Formula is:

    INDIRECT(ADDRESS(ROW(), 1)) <> ""

Apply Style: Accent 1

## Code

### All Columns; Rows 3 - 200

#### Pseudo Code

    switch:
        {Column <current>} is 1:
            {'Chart of Accounts'; Column Q; Row <current>}                                                                                  -- (Account Type)
        {Column <current>} is 2:
            {'Chart of Accounts'; Column R; Row <current>}                                                                                  -- (Category 1)
        {Column <current>} is 3:
            {'Chart of Accounts'; Column T; Row <current>}                                                                                  -- (Category 2)
        {Column <current>} is 4:
            Switch:
                {Column A; Row <current>} is not empty:
                    Sum of {'Chart of Accounts'; Column Y; Row (3 - 200)}                                                                   -- (Opening Amount)
                        where {'Chart of Accounts'; Colmun V; Row <corresponding>} == {'Chart of Accounts'; Column Q; Row <current>}        -- (Account Type)
                {Column B; Row <current>} is not empty:
                    Sum of {'Chart of Accounts'; Column Y; Row (3 - 200)}                                                                   -- (Opening Amount)
                        where {'Chart of Accounts'; Column W; Row <corresponding>} == {'Chart of Accounts'; Column S; Row <current>}        -- (Category 1)
                {Column C; Row <current>} is not empty:
                    {'Chart of Accounts'; Column Y; Row <current>}	                                                                        -- (Opening Amount)
                TRUE:
                    empty-string
        {Column <current>; Row 1} is *NOT* a valid month:
            {Column <current - 2>; Row <current>} minus {Column <current - 1>; Row <current>}                                               -- (Opening Amount - Monthly Actual)
        {Column A; Row <current>} is not empty:
            Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Account Type)
        {Column B; Row <current>} is not empty:
            Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Category 1)
        {Column C; Row <current>} is not empty:
            (
                (
                    If {'Info'; Column B; Row 2} is TRUE:                                                                                   -- (Show actuals before today)
                        Sum of {'Transactions - Actual'; Column G; rows 3 - 5000}                                                           -- (Debits)
                            Where
                                       {'Transactions - Actual'; Column F; Row <corresponding>}                                             -- (fully-qualified account name)
                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                                                 -- (fully-qualified account name)
                                AND    {'Transactions - Actual'; Column B; Row <corresponding>}                                             -- (posted date)
                                    >=  DATE(
                                              {'Info'; Column B; Row 2}                                                                     -- (year)
                                            , MONTH("01" & {Row 1; Column <current>} & "01")                                                    -- (month)
                                            , "-01"                                                                                         -- (day)
                                        )                                                                                                   -- (first of month)
                                AND     {'Transactions - Actual'; Column B; Row <corresponding row>}                                        -- (posted date)
                                    <=  EOMONTH(
                                            DATE(
                                                  {'Info'; Column B; Row 1}                                                                 -- (year)
                                                , MONTH("01" & {Row 1; Column <current>} & "01")                                            -- (month)
                                                , "-01"                                                                                     -- (day)
                                            )
                                        )                                                                                                   -- (last of month)
                    Else:
                        Sum of {'Transactions - Actual'; Column G; rows 3 - 5000}                                                           -- (Debits)
                            Where
                                       {'Transactions - Actual'; Column F; Row <corresponding>}                                             -- (fully-qualified account name)
                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                                                 -- (fully-qualified account name)
                                AND    {'Transactions - Actual'; Column B; Row <corresponding>}                                             -- (posted date)
                                    >=  DATE(
                                              {'Info'; Column B; Row 2}                                                                     -- (year)
                                            , MONTH("01" & {Row 1; Column <current>} & "01")                                                -- (month)
                                            , "-01"                                                                                         -- (day)
                                        )                                                                                                   -- (first of month)
                                AND     {'Transactions - Actual'; Column B; Row <corresponding>}                                            -- (posted date)
                                    <=  EOMONTH(
                                            DATE(
                                                  {'Info'; Column B; Row 1}                                                                 -- (year)
                                                , MONTH("01" & {Row 1; Column <current>} & "01")                                            -- (month)
                                                , "-01"                                                                                     -- (day)
                                            )
                                        )
                                AND     {'Transactions - Actual'; Column B; Row <corresponding}                                             -- (posted date)
                                    <=  TODAY()
                )
                multiplied by (
                          1 if account increases by debits
                      OR -1 if account increases by credits
                )
                multiplied by(                                                                                                              -- (contra account?)
                    1 for "regular" account
                    -1 for "contra" account
                )
            )
            MINUS
            (
                (
                    If {'Info'; Column B; Row 2} is TRUE:                                                                                   -- (Show actuals before today)
                        Sum of {'Transactions - Actual'; Column H; rows 3 - 5000}                                                           -- (Credits)
                            Where
                                       {'Transactions - Actual'; Column F; Row <corresponding>}                                             -- (fully-qualified account name)
                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                                                 -- (fully-qualified account name)
                                AND    {'Transactions - Actual'; Column B; Row <corresponding>}                                             -- (posted date)
                                    >=  DATE(
                                              {'Info'; Column B; Row 2}                                                                     -- (year)
                                            , MONTH("01" & {Colmun <current>; Row 1} & "01")                                                    -- (month)
                                            , "-01"                                                                                         -- (day)
                                        )                                                                                                   -- (first of month)
                                AND     {'Transactions - Actual'; Column B; Row <corresponding>}                                            -- (posted date)
                                    >=  EOMONTH(
                                            DATE(
                                                  {'Info'; Column B; Row 1}                                                                 -- (year)
                                                , MONTH("01" & {Column <current>; Row 1} & "01")                                            -- (month)
                                                , "-01"                                                                                     -- (day)
                                            )
                                        )                                                                                                   -- (last of month)
                    Else:
                        Sum of {'Transactions - Actual'; Column H; rows 3 - 5000}                                                           -- (Credits)
                            Where
                                       {'Transactions - Actual'; Column F; Row <corresponding>}                                             -- (fully-qualified account name)
                                    == {'Chart of Accounts'; Column X; Row <corresponding>}                                                 -- (fully-qualified account name)
                                AND    {'Transactions - Actual'; Column B; Row <corresponding>}                                             -- (posted date)
                                    >=  DATE(
                                              {'Info'; Column B; Row 2}                                                                     -- (year)
                                            , MONTH("01" & {Colmun <current>; Row 1} & "01")                                                    -- (month)
                                            , "-01"                                                                                         -- (day)
                                        )                                                                                                   -- (first of month)
                                AND     {'Transactions - Actual'; Column B; Row <corresponding>}                                            -- (posted date)
                                    <=  EOMONTH(
                                            DATE(
                                                  {'Info'; Column B; Row 1}                                                                 -- (year)
                                                , MONTH("01" & {Column <current>; Row 1} & "01")                                            -- (month)
                                                , "-01"                                                                                     -- (day)
                                            )
                                        )                                                                                                   -- (last of month)
                                AND     {'Transactions - Actual'; Column B; Row <corresponding>}                                            -- (posted date)
                                    <=  TODAY()
                )
                multiplied by (
                        1 if account increases by credits
                    OR -1 if account increases by debits
                )
                multiplied by(                                                                                                               -- (contra account?)
                    1 for "regular" account
                    -1 for "contra" account
                )
        TRUE:
            emtpy string

#### LOC Code (from Column E, Row 3)

    =IFS(
          COLUMN() = 1
        , $'Chart of Accounts'.$Q3
        , COLUMN() = 2
        , $'Chart of Accounts'.$R3
        , COLUMN() = 3
        , $'Chart of Accounts'.$T3
        , COLUMN() = 4
        , IFS(
              $A3<>""
            , SUMIF(
                  $'Chart of Accounts'.$V$3:$'Chart of Accounts'.$V$200
                , $'Chart of Accounts'.$Q3
                , $'Chart of Accounts'.$Y$3:$'Chart of Accounts'.$Y$200
              )
            , $B3<>""
            , SUMIF(
                  $'Chart of Accounts'.$W$3:$'Chart of Accounts'.$W$200
                , $'Chart of Accounts'.$S3
                , $'Chart of Accounts'.$Y$3:$'Chart of Accounts'.$Y$200
              )
            , $C3<>""
            , $'Chart of Accounts'.$Y3
            , 1
            , ""
          )
        , ISERROR(MONTH("01-" & E$1 & "-01"))
        , IFERROR(C3+D3, "")
        , $A3<>""
        , SUMIFS(
              INDIRECT(ADDRESS(ROW() + 1, COLUMN()) & ":" & ADDRESS(ROW() + MATCH(".+",$A4:$A$200,0) - 1, COLUMN()))
            , INDIRECT(ADDRESS(ROW() + 1, 3)        & ":" & ADDRESS(ROW() + MATCH(".+",$A4:$A$200,0) - 1, 3))
            , "=.+"
          )
        , $B3<>""
        , SUMIFS(
              INDIRECT(ADDRESS(ROW() + 1, COLUMN()) & ":" & ADDRESS(ROW() + MATCH(".+",$B4:$B$200,0) - 1, COLUMN()))
            , INDIRECT(ADDRESS(ROW() + 1, 3)        & ":" & ADDRESS(ROW() + MATCH(".+",$B4:$B$200,0) - 1, 3))
            , "=.+"
          )
        , $C3<>""
        , (
            IF(
                  $Info.$B$2 = TRUE()
                , SUMIFS(
                      $'Transactions - Actual'.$G$3:$'Transactions - Actual'.$G$6000
                    , $'Transactions - Actual'.$F$3:$'Transactions - Actual'.$F$6000
                    , "=" & $'Chart of Accounts'.$X3
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , ">=" & DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1)
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , "<=" & EOMONTH(DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1), 0)
                  )
                , SUMIFS(
                      $'Transactions - Actual'.$G$3:$'Transactions - Actual'.$G$6000
                    , $'Transactions - Actual'.$F$3:$'Transactions - Actual'.$F$6000
                    , "=" & $'Chart of Accounts'.$X3
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , ">=" & DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1)
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , "<=" & EOMONTH(DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1), 0)
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , "<=" & TODAY()
                  )
            ) * IF(
                  VLOOKUP(
                      $'Chart of Accounts'.$V3
                    , $Info.$A$5:$Info.$B$9
                    , 2
                    , 1
                  ) = "Dr"
                , 1
                , -1
            ) * $'Chart of Accounts'.$Z3
          ) + (
            IF(
                  $Info.$B$2 = TRUE()
                , SUMIFS(
                      $'Transactions - Actual'.$H$3:$'Transactions - Actual'.$H$6000
                    , $'Transactions - Actual'.$F$3:$'Transactions - Actual'.$F$6000
                    , "=" & $'Chart of Accounts'.$X3
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , ">=" & DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1)
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , "<=" & EOMONTH(DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1), 0)
                  )
                , SUMIFS(
                      $'Transactions - Actual'.$H$3:$'Transactions - Actual'.$H$6000
                    , $'Transactions - Actual'.$F$3:$'Transactions - Actual'.$F$6000
                    , "=" & $'Chart of Accounts'.$X3
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , ">=" & DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1)
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , "<=" & EOMONTH(DATE($Info.$B$1, MONTH("01-" & E$1 & "-01"), 1), 0)
                    , $'Transactions - Actual'.$B$3:$'Transactions - Actual'.$B$6000
                    , "<=" & TODAY()
                  )
            ) * IF(
                  VLOOKUP(
                      $'Chart of Accounts'.$V3
                    , $Info.$A$5:$Info.$B$9
                    , 2
                    , 1
                  ) = "Cr"
                , 1
                , -1
            ) * $'Chart of Accounts'.$Z3
          )
        , 1
        , ""
    )

</main>