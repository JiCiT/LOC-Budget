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
            {Chart of Accounts; Column T; Row: <current>}                           -- (Category 2)
        {Column: <current>; Row: 1} is a number (__NOTE__ - dates are numbers):     -- (Cash Flow date)
            switch:
                {Column: A; Row: <current>} is not blank:
                    Sum of:                                                                                                                             -- Equates to:
                                {Column: <current>; Row: <current  + 1>}                                                                                --  Sum of all rows for Column C (Account)
                        through {Column: <current>; Row: <one less than the first row found *after* the current with non-blank in Column <current>>     --  Between the row after current
                        where:                                                                                                                          --  and the row just prior to the next non-blank Column A (Type)
                                {Column: C; Row: <corresponding>} is not blank                                                                          -- (Account)
                {Column: B; Row: <current>} is not blank:
                    Sum of:                                                                                                                             -- Equates to:
                                {Column: <current>; Row: <current  + 1>}                                                                                --  Sum of all rows for Column C (Account)
                        through {Column: <current>; Row: <one less than the first row found *after* the current with non-blank in Column <current>>     --  Between the row after current
                        where:                                                                                                                          --  and the row just prior to the next non-blank Column A (Type)
                                {Column: A; Row: <corresponding>} is not blank                                                                          -- (Account)
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
                                IF {Column <current>; Row 1} > "TODAY":
                                    (
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
                                        )
                                        /*
                                         *  __START__ - SPREAD
                                         */
                                        /*
                                         * __START__ -      Budget date during current month
                                         *              AND Budget date on or before Cash Flow date
                                         */
                                        PLUS (
                                            /* __START__ - Debits */
                                            (
                                                (
                                                    Sum of:
                                                                {
                                                                    Transactions - Budget;
                                                                    Column: <
                                                                                        19                                                                      -- (Debits cumulative as of date)
                                                                                PLUS    (
                                                                                            /*
                                                                                             * ADJUSTMENT FOR
                                                                                             * budget amount remaining as of today (Cash Flow date during current month)
                                                                                             * or full budget amount (all other cases)
                                                                                             */
                                                                                            (
                                                                                                IF {Column: <current>; Row 1} <= end of month of TODAY:         -- (Cash Flow date)
                                                                                                    Day of month of today
                                                                                                ELSE:
                                                                                                    Day of last day of month of TODAY month
                                                                                            )
                                                                                            MULTIPLY    2
                                                                                        )
                                                                            >;
                                                                    Row: 3
                                                                }
                                                        through {
                                                                    Transactions - Budget;
                                                                    Column: <
                                                                                        19                                                                      -- (Debits cumulative as of date)
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
                                                                    > end of month prior to month of TODAY                                                      -- >= first of current month
                                                            AND {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                    <= end of month of TODAY                                                                    -- <= last of current month
                                                            AND {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                    <= {Column: <current>; Row: 1}                                                              -- (<= Cash Flow date)
                                                            AND {Transactios - Budget; Column: C; Row: <corresponding>}                                         -- (Full account name)
                                                                    = {Chart of Accounts; Column: X, Row: <current>}                                            -- (= Full account name)
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
                                            /*  __END__  - Debits   */
                                            /* __START__ - Credits  */
                                            PLUS (
                                                (
                                                    Sum of:
                                                                {
                                                                    Transactions - Budget;
                                                                    Column: <
                                                                                        19                                                                      -- (Credits cumulative as of date)
                                                                                PLUS    (
                                                                                            /*
                                                                                             * ADJUSTMENT FOR
                                                                                             * budget amount remaining as of today (Cash Flow date during current month)
                                                                                             * or full budget amount (all other cases)
                                                                                             */
                                                                                            (
                                                                                                IF {Column: <current>; Row 1} <= end of month of TODAY:         -- (Cash Flow date)
                                                                                                    Day of month of today
                                                                                                ELSE:
                                                                                                    Day of last day of month of TODAY month
                                                                                            )
                                                                                            MULTIPLY    2
                                                                                        )
                                                                                PLUS    1                                                                       -- (Credits cumulative as of date)
                                                                            >;
                                                                    Row: 3
                                                                }
                                                        through {
                                                                    Transactions - Budget;
                                                                    Column: <
                                                                                        19                                                                      -- (Creditscumulative as of date)
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
                                                                                PLUS    1                                                                       -- (Credits cumulative as of date)
                                                                            >;
                                                                    Row: 3
                                                                }
                                                        where:
                                                                {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                    > end of month prior to month of TODAY                                                      -- >= first of current month
                                                            AND {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                    <= end of month of TODAY                                                                    -- <= last of current month
                                                            AND {Transactios - Budget; Column: A; Row: <corresponding>}                                         -- (Budget date)
                                                                    <= {Column: <current>; Row: 1}                                                              -- (<= Cash Flow date)
                                                            AND {Transactios - Budget; Column: C; Row: <corresponding>}                                         -- (Full account name)
                                                                    = {Chart of Accounts; Column: X, Row: <current>}                                            -- (= Full account name)
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
                                            /*  __END__  - Credits */
                                        )
                                        /*
                                         *  __END__  -      Budget date during current month
                                         *              AND Budget date on or before Cash Flow date
                                         */
                                        /*
                                         * __START__ -      Budget date during current month
                                         *              AND Budget date on or before Cash Flow date
                                         *
                                         *              Backout amouts used as of TODAY
                                         */
                                        PLUS (
                                            /*
                                             *  __START__ - Debits
                                             */
                                            (
                                                Sum of:
                                                            {Transactions - Budget; Column P; Row 3}                -- (Debits used as of today)
                                                    through {Transactions - Budget; Column P; Row 5000}
                                                    where:
                                                            {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                > "TODAY"
                                                        AND {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                <= {Column: <current>; Row: 1}                      -- (Cash Flow date)
                                                        AND {Transactions - Budget; Column C: Row <corresponding>}  -- (Full account name)
                                                                = {Chart of Accounts; Column: X; Row: <current>}    -- (Full account name)
                                                        AND {Transactions - Budget; Column K: Row <corresponding>}  -- (Spread flag/days)
                                                                is not blank
                                                Multipiled By:
                                                    {Chart of Accounts; Column Z; Row: <current>}                   -- (Contra flag)
                                                Multiplied By:
                                                    IF account type increases by debits THEN
                                                        1
                                                    ELSE
                                                        -1
                                            )
                                            /*
                                             *  __END__  - Debits
                                             */
                                            /*
                                             * __START__ - Credits
                                             */
                                            PLUS (
                                                Sum of:
                                                            {Transactions - Budget; Column Q; Row 3}                -- (Credits used as of today)
                                                    through {Transactions - Budget; Column Q; Row 5000}
                                                    where:
                                                            {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                > "TODAY"
                                                        AND {Transactions - Budget; Column A: Row <corresponding>}  -- (Budget Date)
                                                                <= {Column: <current>; Row: 1}                      -- (Cash Flow date)
                                                        AND {Transactions - Budget; Column C: Row <corresponding>}  -- (Full account name)
                                                                = {Chart of Accounts; Column: X; Row: <current>}    -- (Full account name)
                                                        AND {Transactions - Budget; Column K: Row <corresponding>}  -- (Spread flag/days)
                                                                is not blank
                                                Multipiled By:
                                                    {Chart of Accounts; Column Z; Row: <current>}                   -- (Contra flag)
                                                Multiplied By:
                                                    IF account type increases by credits THEN
                                                        1
                                                    ELSE
                                                        -1
                                            )
                                            /*
                                             *  __END__  - Credits
                                             */
                                        )
                                        /*
                                         *  __END __  -     Budget date during current month
                                         *              AND Budget date on or before Cash Flow date
                                         *
                                         *              Backout amouts used as of TODAY
                                         */
                                        /*
                                         * __START__ -      Budget date *after* end of month of TODAY
                                         *              AND Budget date *on or after* Cash Flow date
                                         *              AND Budget date *on or before* Cash Flow date
                                         */
                                        PLUS (
                                            /* __START__ - Debits /*
                                            (
                                                (
                                                    Sum of:
                                                        {
                                                            Transactions - Budget;
                                                            Column: <
                                                                        19                                                                                          -- (Debits)
                                                                PLUS    (
                                                                            Day of ({Column: <current>; Row: 1}                                                     -- (Day of Cash Flow date)
                                                                            MULTIPLY 2
                                                                        )
                                                            >;
                                                            Row: 3
                                                        }
                                                        through {
                                                            Transactions - Budget;
                                                            Column: <
                                                                        19                                                                                          -- (Debits)
                                                                PLUS    (
                                                                            Day of ({Column: <current>; Row: 1}                                                     -- (Day of Cash Flow date)
                                                                            MULTIPLY 2
                                                                        )
                                                            >;
                                                            Row: 5000
                                                        }
                                                    where:
                                                            {Transactios - Budget; Column A; Row: <corresponding>} > end of month of today                          -- (Budget date)
                                                        AND {Transactios - Budget; Column A; Row: <corresponding>}                                                  -- (Budget date)
                                                                > end of month prior to {Column: <current>; Row: 1}                                                 -- (Cash Flow date)
                                                        AND {Transactios - Budget; Column A; Row: <corresponding>}                                                  -- (Budget date)
                                                                <= {Column <current>; Row: 1}                                                                       -- (Cash Flow date)
                                                        AND {Transactions - Budget; Column C: Row <corresponding>}                                                  -- (Full account name)
                                                                = {Chart of Accounts; Column: X; Row: <current>}                                                    -- (Full account name)
                                                        AND {Transactions - Budget; Column K: Row <corresponding>}                                                  -- (Spread flag/days)
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
                                            /*  __END__  - Debits /*
                                            /* __START__ - Credits /*
                                            PLUS (
                                                (
                                                    Sum of:
                                                        {
                                                            Transactions - Budget;
                                                            Column: <
                                                                        19                                                                                          -- (Credits)
                                                                PLUS    (
                                                                            Day of ({Column: <current>; Row: 1}                                                     -- (Day of Cash Flow date)
                                                                            MULTIPLY 2
                                                                        )
                                                                        PLUS 1                                                                                      -- (Credits)
                                                            >;
                                                            Row: 3
                                                        {
                                                        through {
                                                            Transactions - Budget;
                                                            Column: <
                                                                        19                                                                                          -- (Credits)
                                                                PLUS    (
                                                                            Day of ({Column: <current>; Row: 1}                                                     -- (Day of Cash Flow date)
                                                                            MULTIPLY 2
                                                                        )
                                                                        PLUS 1                                                                                      -- (Credits)
                                                            >;
                                                            Row: 5000
                                                        }
                                                    where:
                                                            {Transactios - Budget; Column A; Row: <corresponding>} > end of month of today                          -- (Budget date)
                                                        AND {Transactios - Budget; Column A; Row: <corresponding>}                                                  -- (Budget date)
                                                                > end of month prior to {Column: <current>; Row: 1}                                                 -- (Cash Flow date)
                                                        AND {Transactios - Budget; Column A; Row: <corresponding>}                                                  -- (Budget date)
                                                                <= {Column <current>; Row: 1}                                                                       -- (Cash Flow date)
                                                        AND {Transactions - Budget; Column C: Row <corresponding>}                                                  -- (Full account name)
                                                                = {Chart of Accounts; Column: X; Row: <current>}                                                    -- (Full account name)
                                                        AND {Transactions - Budget; Column K: Row <corresponding>}                                                  -- (Spread flag/days)
                                                                is not blank
                                                )
                                                MULTIPLY BY {Chart of Accounts; Column: Z; Row: <current>}                                                          -- (Contra flag)
                                                MULTIPLY BY (
                                                                IF account increases by credits THEN:
                                                                    1
                                                                ELSE
                                                                    -1
                                                            )
                                            )
                                            /*  __END__  - Credits /*
                                        )
                                        /*
                                         *  __END__  -      Budget date *after* end of month of TODAY
                                         *              AND Budget date *on or before* Cash Flow date
                                         */
                                        PLUS (
                                        /*
                                         * __START__ -      Budget date *after* end of month of TODAY
                                         *              AND Budget date *on or before* end of momnth prior to Cash Flow date
                                         */
                                            /* __START__ - Debits */
                                            (
                                                Sum of:
                                                            {Transactions - Budget; Column D; Row 3}                    -- (Debits)
                                                    through {Transactions - Budget; Column D; Row 5000}
                                                    where:
                                                            {Transactions - Budget; Column A: Row <corresponding>}      -- (Budget Date)
                                                                > end of month of TODAY
                                                        AND {Transactions - Budget; Column A: Row <corresponding>}      -- (Budget Date)
                                                                <= end of month prior to {Column: <current>; Row: 1}    -- (Cash Flow date)
                                                        AND {Transactions - Budget; Column C: Row <corresponding>}      -- (Full account name)
                                                                = {Chart of Accounts; Column: X; Row: <current>}        -- (Full account name)
                                                        AND {Transactions - Budget; Column K: Row <corresponding>}      -- (Spread flag/days)
                                                                is blank
                                                Multipiled By:
                                                    {Chart of Accounts; Column Z; Row: <current>}                       -- (Contra flag)
                                                Multiplied By:
                                                    IF account type increases by debits THEN
                                                        1
                                                    ELSE
                                                        -1                                            
                                            )
                                            /*  __END__  - Debits  */
                                            /* __START__ - Credits */
                                            PLUS
                                            (
                                                Sum of:
                                                            {Transactions - Budget; Column E; Row 3}                    -- (Credits)
                                                    through {Transactions - Budget; Column E; Row 5000}
                                                    where:
                                                            {Transactions - Budget; Column A: Row <corresponding>}      -- (Budget Date)
                                                                > end of month of TODAY
                                                        AND {Transactions - Budget; Column A: Row <corresponding>}      -- (Budget Date)
                                                                <= end of month prior to {Column: <current>; Row: 1}    -- (Cash Flow date)
                                                        AND {Transactions - Budget; Column C: Row <corresponding>}      -- (Full account name)
                                                                = {Chart of Accounts; Column: X; Row: <current>}        -- (Full account name)
                                                        AND {Transactions - Budget; Column K: Row <corresponding>}      -- (Spread flag/days)
                                                                is blank
                                                Multipiled By:
                                                    {Chart of Accounts; Column Z; Row: <current>}                       -- (Contra flag)
                                                Multiplied By:
                                                    IF account type increases by debits THEN
                                                        1
                                                    ELSE
                                                        -1
                                            )
                                            /*  __END__  - Credits */
                                        )
                                        /*
                                         *  __END__  -      Budget date *after* end of month of TODAY
                                         *              AND Budget date *on or before* end of momnth prior to Cash Flow date
                                         */
                                        
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
                          ) 
                        * $'Chart of Accounts'.$Z3
                        * IF(
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
                          )
                        * $'Chart of Accounts'.$Z3
                        * IF(
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
                              )
                            + (
                                  (

                                      SUMIFS(
                                          INDIRECT(
                                              "$'Transactions - Budget'."
                                            & ADDRESS(
                                                  3
                                                ,   19
                                                  + (
                                                          IF(
                                                              E$1 <= EOMONTH(TODAY(), 0)
                                                            , DAY(E$1)
                                                            , DAY(EOMONTH(TODAY(), 0))
                                                          )
                                                        * 2
                                                    )
                                              )
                                            & ":"
                                            & ADDRESS(
                                                  5000
                                                ,   19
                                                  + (
                                                          IF(
                                                              E$1 <= EOMONTH(TODAY(), 0)
                                                            , DAY(E$1)
                                                            , DAY(EOMONTH(TODAY(), 0))
                                                          )
                                                        * 2
                                                    )
                                              )
                                          )
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , ">" & EOMONTH(TODAY(), -1)
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , "<=" & EOMONTH(TODAY(), 0)
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
                                                ,   19
                                                  + (
                                                          IF(
                                                              E$1 <= EOMONTH(TODAY(), 0)
                                                            , DAY(E$1)
                                                            , DAY(EOMONTH(TODAY(), 0))
                                                          )
                                                        * 2
                                                    )
                                                  + 1
                                              )
                                            & ":"
                                            & ADDRESS(
                                                  5000
                                                ,   19
                                                  + (
                                                          IF(
                                                              E$1 <= EOMONTH(TODAY(), 0)
                                                            , DAY(E$1)
                                                            , DAY(EOMONTH(TODAY(), 0))
                                                          )
                                                        * 2
                                                    )
                                                  + 1
                                              )
                                          )
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , ">" & EOMONTH(TODAY(), -1)
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , "<=" & EOMONTH(TODAY(), 0)
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
                              )
                            + (
                                  -(
                                      SUMIFS(
                                          $'Transactions - Budget'.$P$3:$P$5000
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , ">" & EOMONTH(TODAY(), -1)
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , "<=" & EOMONTH(TODAY(), 0)
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
                                + -(
                                      SUMIFS(
                                          $'Transactions - Budget'.$Q$3:$Q$5000
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , ">" & EOMONTH(TODAY(), -1)
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , "<=" & EOMONTH(TODAY(), 0)
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
                              )
                            + (
                                  (
                                      SUMIFS(
                                          INDIRECT(
                                              "$'Transactions - Budget'."
                                            & ADDRESS(
                                                  3
                                                , 19 + (DAY(E$1) * 2)
                                              )
                                            & ":"
                                            & ADDRESS(
                                                  5000
                                                , 19 + (DAY(E$1) * 2)
                                              )
                                          )
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , ">" & EOMONTH(TODAY(), 0)
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
                                                , 19 + (DAY(E$1) * 2) + 1
                                              )
                                            & ":"
                                            & ADDRESS(
                                                  5000
                                                , 19 + (DAY(E$1) * 2) + 1
                                              )
                                          )
                                        , $'Transactions - Budget'.$A$3:$A$5000
                                        , ">" & EOMONTH(TODAY(), 0)
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
                                        , ">" & EOMONTH(TODAY(), 0)
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
                                        , ">" & EOMONTH(TODAY(), 0)
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
                                          ) = "Cr"
                                        , 1
                                        , -1
                                      )
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