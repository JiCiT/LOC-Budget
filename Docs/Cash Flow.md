<header>
<font size="+12"><center>
    Cash Flow
</center></font>
</header>

<main>

# Purpose

For every day of the year and each account, shows sum of opening balances, the *actual* accounting, and the *budgeted* accounting _**after**_ the current day (i.e., the projected budget).

# Usage

NO DATA ENTRY  -- This sheet is for informational display only.

# Technical

## Styling

## Row 1

Accent 3 + Underline + Center text

### Conditional

#### A1:AMJ1048576

##### Condition 1

Formula is:

    INDIRECT(ADDRESS(ROW(), 1)) <> ""

Apply Style: Accent 1

##### Condition 2

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
                                                                            {Transactions - Actual; Column K; Row 3}                        -- (Credits)
                                                                    through {Transactions - Actual; Column K; Row 5000}
                                                                    where:
                                                                                    {Transactions - Actual; Column F; Row <corresponding>}  -- (Full account name)
                                                                            EQUALS  {Chart of Accounts; Column X; Row: <current>            -- (Full account name)
                                                                        AND         {Transactions - Actual; Column B; Row <corresponding>}  -- (Date posted)
                                                                            LTE     {Column: <current>; Row 1}                              -- (Cash Flow date)
                                                            ELSE:
                                                                Sum of:
                                                                            {Transactions - Actual; Column K; Row 3}                        -- (Credits)
                                                                    through {Transactions - Actual; Column K; Row 5000}
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
                    /*
                     * __START__ - BUDGET
                     */
                    PLUS    (
                                IF {Column <current>; Row 1} GTE "TODAY":
                                    (
                                        /*
                                         *  __START__ - NON-SPREAD
                                         */
                                                (
                                                    Sum of:
                                                                {Transactions - Budget; Column D; Row 3}                -- (Debits)
                                                        through {Transactions - Budget; Column D; Row 5000}
                                                        where:
                                                                {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                    > "TODAY"
                                                            AND {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                    <= {Column: <current>; Row: 1}                      -- (Cash Flow date)
                                                            AND {Transactions - Budget; Column C: Row <corresponding>}  -- (Full account name)
                                                                    = {Chart of Accounts; Column: X; Row: <current>}    -- (Full account name)
                                                            AND {Transactions - Budget; Column K: Row <corresponding>}  -- (Spread flag/days)
                                                                    is blank
                                                    Multipiled By:
                                                        {Chart of Accounts; Column Z; Row: <current>}                   -- (Contra flag)
                                                    Multiplied By:
                                                        IF account type increases by debits THEN
                                                            1
                                                        ELSE
                                                            -1
                                                )
                                        PLUS    (
                                                    Sum of:
                                                                {Transactions - Budget; Column E; Row 3}                -- (Credits)
                                                        through {Transactions - Budget; Column E; Row 5000}
                                                        where:
                                                                {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                    > "TODAY"
                                                            AND {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                    <= {Column: <current>; Row: 1}                      -- (Cash Flow date)
                                                            AND {Transactions - Budget; Column C: Row <corresponding>}  -- (Full account name)
                                                                    = {Chart of Accounts; Column: X; Row: <current>}    -- (Full account name)
                                                            AND {Transactions - Budget; Column K: Row <corresponding>}  -- (Spread flag/days)
                                                                    is blank
                                                    Multipiled By:
                                                        {Chart of Accounts; Column Z; Row: <current>}                   -- (Contra flag)
                                                    Multiplied By:
                                                        IF account type increases by credits THEN
                                                            1
                                                        ELSE
                                                            -1
                                                )
                                        /*
                                         *   __END__  - NON-SPREAD
                                         */
                                        /*
                                         *  __START__ - SPREAD
                                         */
                                        /*
                                         * __START__ - BUDGET MONTH *ON OR AFTER* CASH FLOW MONTH
                                         */
                                        PLUS    (
                                                    (
                                                        Sum of:
                                                                    {
                                                                        Transactions - Budget;
                                                                        Column: <
                                                                                            19                                                                      -- (Debits)
                                                                                    PLUS    (
                                                                                                            /*
                                                                                                             * ADJUSTMENT FOR
                                                                                                             * current days beyond the BUDGET date
                                                                                                            (
                                                                                                                        DAY of {Column: <current>; Row: 1}          -- (Cash flow date)
                                                                                                                MIUNS   IF
                                                                                                                                {Column: <current>; Row: 1} >= TODAY
                                                                                                                            AND {Column: <current>; Row: 1} <= end-of-month of TODAY
                                                                                                                        THEN
                                                                                                                            DAY of TODAY
                                                                                                                        ELSE
                                                                                                                            0
                                                                                                            )
                                                                                                MULTIPLY    2
                                                                                            )
                                                                                >;
                                                                        Row: 3
                                                                    }
                                                            through {
                                                                        Transactions - Budget;
                                                                        Column: <
                                                                                            19                                                                      -- (Debits)
                                                                                    PLUS    (
                                                                                                            /*
                                                                                                             * ADJUSTMENT FOR
                                                                                                             * current days beyond the BUDGET date
                                                                                                            (
                                                                                                                        DAY of {Column: <current>; Row: 1}          -- (Cash flow date)
                                                                                                                MIUNS   IF
                                                                                                                                {Column: <current>; Row: 1} >= TODAY
                                                                                                                            AND {Column: <current>; Row: 1} <= end-of-month of TODAY
                                                                                                                        THEN
                                                                                                                            DAY of TODAY
                                                                                                                        ELSE
                                                                                                                            0
                                                                                                            )
                                                                                                MULTIPLY    2
                                                                                            )
                                                                                >;
                                                                        Row: 3
                                                                    }
                                                            where:
                                                                    {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                        > end of month prior to the Cash Flow Date
                                                                AND {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                        <= {Column: <current>; Row 1}                                                               -- (Cash Flow date)
                                                                AND {Transactios - Budget; Column: C; Row: <corresponding>}                                         -- (Full account name)
                                                                        = {Chart of Accounts; Column: X, Row: <current>}                                            -- (Full account name)
                                                                AND {Transactios - Budget; Column: K; Row: <corresponding>}                                         -- (Spread flag/number)
                                                                    is not blank
                                                    )
                                                    MULTIPLY BY:
                                                        {"Chart of Accounts: Column: Z; Row: <current>}                                                             -- (Contra flag)
                                                    MULTIPLY BY:
                                                        IF account increases by debits THEN:
                                                            1
                                                        ELSE:
                                                            -1
                                                )
                                        PLUS    (
                                                    (
                                                        Sum of:
                                                                    {
                                                                        Transactions - Budget;
                                                                        Column: <
                                                                                            19
                                                                                    PLUS    (
                                                                                                            /*
                                                                                                             * ADJUSTMENT FOR
                                                                                                             * current days beyond the BUDGET date
                                                                                                            (
                                                                                                                        DAY of {Column: <current>; Row: 1}        -- (Cash flow date)
                                                                                                                MIUNS   IF
                                                                                                                                {Column: <current>; Row: 1} >= TODAY
                                                                                                                            AND {Column: <current>; Row: 1} <= end-of-month of TODAY
                                                                                                                        THEN
                                                                                                                            DAY of TODAY
                                                                                                                        ELSE
                                                                                                                            0
                                                                                                            )
                                                                                                MULTIPLY    2
                                                                                            )
                                                                                    PLUS    1                                                                       -- (Credits)
                                                                                >;
                                                                        Row: 3
                                                                    }
                                                            through {
                                                                        Transactions - Budget;
                                                                        Column: <
                                                                                            19
                                                                                    PLUS    (
                                                                                                            /*
                                                                                                             * ADJUSTMENT FOR
                                                                                                             * current days beyond the BUDGET date
                                                                                                            (
                                                                                                                        DAY of {Column: <current>; Row: 1}          -- (Cash flow date)
                                                                                                                MIUNS   IF
                                                                                                                                {Column: <current>; Row: 1} >= TODAY
                                                                                                                            AND {Column: <current>; Row: 1} <= end-of-month of TODAY
                                                                                                                        THEN
                                                                                                                            DAY of TODAY
                                                                                                                        ELSE
                                                                                                                            0
                                                                                                            )
                                                                                                MULTIPLY    2
                                                                                            )
                                                                                    PLUS    1                                                                       -- (Credits)
                                                                                >;
                                                                        Row: 3
                                                                    }
                                                            where:
                                                                    {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                        > end of month prior to the Cash Flow Date
                                                                AND {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                        <= {Column: <current>; Row 1}                                                               -- (Cash Flow date)
                                                                AND {Transactios - Budget; Column: C; Row: <corresponding>}                                         -- (Full account name)
                                                                        = {Chart of Accounts; Column: X, Row: <current>}                                            -- (Full account name)
                                                                AND {Transactios - Budget; Column: K; Row: <corresponding>}                                         -- (Spread flag/number)
                                                                    is not blank
                                                    )
                                                    MULTIPLY BY:
                                                        {"Chart of Accounts: Column: Z; Row: <current>}                                                             -- (Contra flag)
                                                    MULTIPLY BY:
                                                        IF account increases by credits THEN:
                                                            1
                                                        ELSE:
                                                            -1
                                                )
                                        /*
                                         *  __END__  - BUDGET MONTH *ON OR AFTER* CASH FLOW MONTH
                                         */
                                        /*
                                         * __START__ - BUDGET MONTH *BEFORE* CASH FLOW MONTH
                                         */
                                        PLUS    (
                                                                (
                                                                    Sum of:
                                                                                {Transactios - Budget; Column D; Row: 3}                                            -- (Debits)
                                                                        through {Transactios - Budget; Column D; Row: 5000}
                                                                        where:
                                                                                {Transactios - Budget; Column A; Row: <corresponding>}                              -- (Budget date)
                                                                                    <= end-of-month prior to Cash Flow date
                                                                            AND {Transactios - Budget; Column C; Row: <corresponding>}                              -- (Account full name)
                                                                                    = {Chart of Accounts; Column: X, Row: <current>}                                -- (Account full name)
                                                                            AND {Transactios - Budget; Column C; Row: <corresponding>}                              -- (Spread flag/number)
                                                                                    is not blank
                                                                )
                                                    MULTIPLY BY {Chart of Accounts; Column: Z; Row: <current>}                                                      -- (Contra flag)
                                                    MULTIPLY BY (
                                                                    IF account increases by debits THEN:
                                                                        1
                                                                    ELSE
                                                                        -1
                                                                )

                                                )
                                        PLUS    (
                                                                (
                                                                    Sum of:
                                                                                {Transactios - Budget; Column E; Row: 3}                                            -- (Credits)
                                                                        through {Transactios - Budget; Column E; Row: 5000}
                                                                        where:
                                                                                {Transactios - Budget; Column A; Row: <corresponding>}                              -- (Budget date)
                                                                                    <= end-of-month prior to Cash Flow date
                                                                            AND {Transactios - Budget; Column C; Row: <corresponding>}                              -- (Account full name)
                                                                                    = {Chart of Accounts; Column: X, Row: <current>}                                -- (Account full name)
                                                                            AND {Transactios - Budget; Column C; Row: <corresponding>}                              -- (Spread flag/number)
                                                                                    is not blank
                                                                )
                                                    MULTIPLY BY {Chart of Accounts; Column: Z; Row: <current>}                                                      -- (Contra flag)
                                                    MULTIPLY BY (
                                                                    IF account increases by credits THEN:
                                                                        1
                                                                    ELSE
                                                                        -1
                                                                )

                                                )
                                        /*
                                         *  __END__  - BUDGET MONTH *BEFORE* CASH FLOW MONTH
                                         */
                                    )
                                ELSE:
                                    ZERO
                            )
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
                                , $'Transactions - Actual'.$B$3:$B$5000
                                , "<=" & E$1
                              )
                            , SUMIFS(
                                  $'Transactions - Actual'.$G$3:$G$5000
                                , $'Transactions - Actual'.$F$3:$F$5000
                                , "=" & $'Chart of Accounts'.$X3
                                , $'Transactions - Actual'.$B$3:$B$5000
                                , "<=" & E$1
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
                                , "<=" & E$1
                              )
                            , SUMIFS(
                                  $'Transactions - Actual'.$H$3:$H$5000
                                , $'Transactions - Actual'.$F$3:$F$5000
                                , "=" & $'Chart of Accounts'.$X3
                                , $'Transactions - Actual'.$B$3:$B$5000
                                , "<=" & E$1
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
                )
              + (
                    IF (
                          E$1 > TODAY()
                        , (
                              (
                                  SUMIFS(
                                      $'Transactions - Budget'.$D$3:$D$5000
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , ">" & TODAY()
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , "<=" & E$1
                                    , $'Transactions - Budget'.$C$3:$C$5000
                                    , "=" & $'Chart of Accounts'.$X3
                                    , $'Transactions - Budget'.$K$3:$K$5000
                                    , "="
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
                                      $'Transactions - Budget'.$E$3:$E$5000
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , ">" & TODAY()
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , "<=" & E$1
                                    , $'Transactions - Budget'.$C$3:$C$5000
                                    , "=" & $'Chart of Accounts'.$X3
                                    , $'Transactions - Budget'.$K$3:$K$5000
                                    , "="
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
                            + (
                                  SUMIFS(
                                      INDIRECT(
                                          "$'Transactions - Budget'."
                                        & ADDRESS(
                                              3
                                            , 19
                                              + (
                                                      (
                                                          DAY(E$1)
                                                        - IF (
                                                              AND (
                                                                  E$1 >= TODAY()
                                                                , E$1 <= EOMONTH(TODAY(), 0)
                                                              )
                                                            , DAY(TODAY())
                                                            , 0
                                                          )
                                                      )
                                                    * 2
                                                )
                                          )
                                        & ":"
                                        & ADDRESS(
                                              5000
                                            , 19
                                              + (
                                                      (
                                                          DAY(E$1)
                                                        - IF (
                                                              AND (
                                                                  E$1 >= TODAY()
                                                                , E$1 <= EOMONTH(TODAY(), 0)
                                                              )
                                                            , DAY(TODAY())
                                                            , 0
                                                          )
                                                      )
                                                    * 2
                                                )
                                          )
                                      )
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , ">" & EOMONTH(E$1, -1)
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , "<=" & E$1
                                    , $'Transactions - Budget'.$C$3:$C$5000
                                    , "=" & $'Chart of Accounts'.$X3
                                    , $'Transactions - Budget'.$K$3:$K$5000
                                    , "<>"
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
                                      INDIRECT(
                                          "$'Transactions - Budget'."
                                        & ADDRESS(
                                              3
                                            , 19
                                              + (
                                                      (
                                                          DAY(E$1)
                                                        - IF (
                                                              AND (
                                                                  E$1 >= TODAY()
                                                                , E$1 <= EOMONTH(TODAY(), 0)
                                                              )
                                                            , DAY(TODAY())
                                                            , 0
                                                          )
                                                      )
                                                    * 2
                                                )
                                              + 1
                                          )
                                        & ":"
                                        & ADDRESS(
                                              5000
                                            , 19
                                              + (
                                                      (
                                                          DAY(E$1)
                                                        - IF (
                                                              AND (
                                                                  E$1 >= TODAY()
                                                                , E$1 <= EOMONTH(TODAY(), 0)
                                                              )
                                                            , DAY(TODAY())
                                                            , 0
                                                          )
                                                      )
                                                    * 2
                                                )
                                              + 1
                                          )
                                      )
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , ">" & EOMONTH(E$1, -1)
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , "<=" & E$1
                                    , $'Transactions - Budget'.$C$3:$C$5000
                                    , "=" & $'Chart of Accounts'.$X3
                                    , $'Transactions - Budget'.$K$3:$K$5000
                                    , "<>"
                                  )
                                * $'Chart of Accounts'.$Z3
                                * IF(
                                      VLOOKUP(
                                          $'Chart of Accounts'.$V3
                                        , $Info.$A$5:$Info.$B$9
                                        , 2
                                        , 1
                                      ) = "Cr"
                                    , 1
                                    , -1
                                  )
                              )
                            + (
                                  SUMIFS(
                                      $'Transactions - Budget'.$D$3:$D$5000
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , "<=" & EOMONTH(E$1, -1)
                                    , $'Transactions - Budget'.$C$3:$C$5000
                                    , "=" & $'Chart of Accounts'.$X3
                                    , $'Transactions - Budget'.$K$3:$K$5000
                                    , "<>"
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
                                      $'Transactions - Budget'.$E$3:$E$5000
                                    , $'Transactions - Budget'.$A$3:$A$5000
                                    , "<=" & EOMONTH(E$1, -1)
                                    , $'Transactions - Budget'.$C$3:$C$5000
                                    , "=" & $'Chart of Accounts'.$X3
                                    , $'Transactions - Budget'.$K$3:$K$5000
                                    , "<>"
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
                          )
                        , 0
                    )
                )
            , 1
            , ""
          )
        , 1
        , ""
    )

</main>