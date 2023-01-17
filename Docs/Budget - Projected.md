<header>
<font size="+12"><center>
    Budget - Projected
</center></font>
</header>

<main>

# Purpose

For each account shows the *budgeted* accounting _**after**_ the current day (ignoring any actual amounts).  For every account and every month, it shows:

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
                {Column A; Row <current>} is not empty:                                                                                     -- (Account Type)
                    Sum of {'Chart of Accounts'; Column Y; Row (3 - 200)}                                                                   -- (Opening Amount)
                        where {'Chart of Accounts'; Colmun V; Row <corresponding>} == {'Chart of Accounts'; Column Q; Row <current>}        -- (Account Type)
                {Column B; Row <current>} is not empty:                                                                                     -- (Category 1)
                    Sum of {'Chart of Accounts'; Column Y; Row (3 - 200)}                                                                   -- (Opening Amount)
                        where {'Chart of Accounts'; Column W; Row <corresponding>} == {'Chart of Accounts'; Column S; Row <current>}        -- (Category 1)
                {Column C; Row <current>} is not empty:                                                                                     -- (Category 2)
                    {'Chart of Accounts'; Column Y; Row <current>}	                                                                        -- (Opening Amount)
                TRUE:
                    zero
        {Column <current>; Row 1} is *NOT* a valid month:
            {Column <current - 2>; Row <current>} plus {Column <current - 1>; Row <current>}                                                -- (Opening Amount - Monthly Actual)
        {Column A; Row <current>} is not empty:                                                                                             -- (Account Type)
            Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Account Type)
        {Column B; Row <current>} is not empty:                                                                                             -- (Category 1)
            Sum of {Column <current>; Row <current + 1> - <<row - 1> of first non-empty cell DOWN {Column <current>; Row <current + 1>}>    -- (Last row for <current> Category 1)
        {Column C; Row <current>} is not empty:                                                                                             -- (Category 2)
                    (
                                    Sum of {'Transactions - Budget'; Column N; rows 3 - 5000}                                               -- (Debits - remaining)
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
                                    Sum of {'Transactions - Budget'; Column O; rows 3 - 5000}                                               -- (Credits - remaining)
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
                                                              {'Info'; Column B; Row 1}                                                      -- (Budget year)
                                                            & {Column: <current>; Row: 1}                                                    -- (Budget month)
                                                            , "-01"                                                                          -- (day)
                                                          )
                                                        , 0
                                                    )                                                                                        -- (end of budget date month)
                        MULTIPLY BY {Chart of Accounts; Column: Z, Row: <current>}                                                           -- (contra account flag)
                        MULTIPLY BY (
                                        IF account increases by credits THEN
                                            1
                                        ELSE
                                            -1
                                    )
                    )
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
        , COLUMN() = 4
        , IFS(
              $A3<>""
            , SUMIF(
                  $'Chart of Accounts'.$V$3:$V$200
                , $'Chart of Accounts'.$Q3
                , $'Chart of Accounts'.$Y$3:$Y$200
              )
            , $B3<>""
            , SUMIF(
                  $'Chart of Accounts'.$W$3:$W$200
                , $'Chart of Accounts'.$S3
                , $'Chart of Accounts'.$Y$3:$Y$200
              )
            , $C3<>""
            , $'Chart of Accounts'.$Y3
            , 1
            , 0
          )
        , ISERROR(MONTH("01-" & E$1 & "-01"))
        , C3+D3
        , $A3<>""
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
                      $'Transactions - Budget'.$N$3:$N$5000
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
                        , $Info.$A$5:$B$9
                        , 2
                        , 1
                      ) = "Dr"
                    , 1
                    , -1
                )
            )
          + (
                SUMIFS(
                      $'Transactions - Budget'.$O$3:$O$5000
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
        , 0
    )

</main>