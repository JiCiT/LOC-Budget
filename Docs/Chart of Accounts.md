<header>
<font size="+12"><center>
    Chart of Accounts
</center></font>
</header>

<main>

# Purpose

This is where you customize your list accounts to fit your needs.  A sample set of accounts is included to get you started.

# Usage

## Rows

One account per row should be created.

## Columns
 
### A - Type

This must match one of the account types listed on the [Info](./Info.md) sheet.

### B - Category

Free-form.  Use to create whatever account categories make sense for your situation.

### C - Account

Free-form.  Make whatever accounts make sense for your situation.

### D - Contra

An X (or any character) in this column indicates that this is a contra account.  That means if the account type for this account normally increases by debits, this account will, instead, increase by credits.

These are typically used for "holding" accounts that are used strictly to "hold" amounts already *effectively* but not *actually* spent.  For example, to show that something purchased via a credit card also ***immediately** effectively* also reduces the amount in, say, a checking account.

### E - Opening balance

Enter here the amount in each account at the beginning of the day you're first accounting for in this spreadsheet.

### All Other Columns (F - Z)

The remaining columns exist solely for use by other sheets.  Essentially, they "spread" columns A - E across multiple rows for display on other sheets (e.g., [Actual](./Docs/Actual.md)).

# Technical

## Styling

### Row 1

Accent 3 + Underline + Center text

#### Columns F, J, P, U

Accent 3

## Data Validity

* Allow
    * Cell range
* Allow Empty Cells
    * False
* Show selection list
    * True
* Source
    * $Info.$A$5:$A$9

## Column A - Account Type


## Code

### Column G; Rows 3 - 165

#### Pseudo Code

    If
        {Column: A; Row: <current>} isn't blank
    THEN
        Copy to current cell

#### LCO Code (from Row 3)

    =IF($A3<>"",$A3,"")

### Column H; Rows 3 - 165

#### Pseudo Code

    If
            {Column: A; Row: <current>} isn't blank
        AND {Column: B; Row: <current>} isn't blank
    THEN
        Join {Column: A} and {Column: B} via "::"

#### LCO Code (from Row 3)

    =IF(AND($A3<>"",$B3<>""), $A3 & "::" & $B3)

### Column I; Rows 3 - 165

#### Pseudo Code

    If
            {Column: A; Row: <current>} isn't blank
        AND {Column: B; Row: <current>} isn't blank
        AND {Column: C; Row: <current>} isn't blank
    THEN
        Join {Column: A}, {Column: B}, and {Column: C} via "::"

#### LCO Code (from Row 3)

    =IF(AND($A3<>"",$B3<>"",$C3<>""), $A3 & "::" & $B3 & "::" & $C3

### Column K; Rows 3 - 165

#### Pseudo Code

    IF
        {Column: G, Row: <current>} != {Column G; Row: <current - 1>}
    THEN
        TRUE
    ELSE
        FALSE

#### LCO Code (from Row 3)

    =COUNTIF($G$3:$G3,$G$3:$G3)=1

### Column L; Row 3

#### Hardcoded

3

### Column L; Row 4 - 165

#### Pseudo Code

    If
            {Column: A; Row: <current>} is not blank
        AND {Column: K; Row: <current>} is TRUE
    THEN
        {Column: O; Row: <previous>} + 1
    ELSE
        0

#### LCO Code (from Row 4)

    =IF(AND($A4<>"",$K4=1), $O3+1, 0)

### Column M; Rows 3 - 165

#### Pseudo Code

    IF
        {Column: H, Row: <current>} != {Column: H; Row: <previous>}
    THEN
        TRUE
    ELSE
        FALSE

#### LCO Code (from Row 3)

    =COUNTIF($H$3:$H3,$H$3:$H3)=1

### Column N; Row 3

#### Pseudo Code

    {Column: L; Row 3} PLUS 1

#### LOC Code

    = $L$3+1

### Column N; Row 4 - 165

#### Pseudo Code

    If
            {Column: B; Row: <current> is not blank
        AND {Column: M; Row: <current>} is TRUE
    THEN
          {Column: O; Row: <previous>}                    -- (Previous Cat 2 Row)
        + {Column: K; Row: <current>}                     -- (Current  Account Type Change)   (0 or 1)
        + {Column: M; Row: <current}                      -- (Current  Cat 1 Change)          (0 or 1)
    ELSE:
        0

#### LCO Code (from Row 4)

    =IF(AND($B4<>"",$M4=1), $O3+$K4+$M4, 0)

### Column O; Row 3

#### Pseudo Code

    {Column: N; Row: 3} PLUS 1

#### LCO Code

    = $N$3+1

### Column O; Row 4 - 165

#### Pseudo Code

    IF
        {Column: C; Row: <current} is not blank
    THEN
          {Column: O; Row: <previous>}
        + 1
        + {Column: K; Row: <current>}                     -- (Current  Account Type Change)   (0 or 1)
        + {Column: M; Row: <current}                      -- (Current  Cat 1 Change)          (0 or 1)
    ELSE
        0

#### LCO Code (from Row 4)

    =IF($C4<>"", $O3+1+$K4+$M4, 0)

### Column Q; Row 3 - 165

#### Pseudo Code

    {Column: A, Row: <{Column: L; Row: <row where value = <current row>}>}
    EXCEPT
        "" if no matching row found

#### LCO Code (from Row 3)

    =IFNA(INDIRECT("$A$"&MATCH(ROW(),$L$1:$L$100,0)),"")

### Column R; Row 3 - 165

#### Pseudo Code

    {Column: B, Row: <{Column: N; Row: <row where value == <current row>}>}
    EXCEPT
        "" if no matching row found

#### LCO Code (from Row 3)

    =IFNA(INDIRECT("$B$"&MATCH(ROW(),$N$1:$N$100,0)),"")

### Column S; Row 3 - 165

#### Pseudo Code

    JOIN
        (
                (
                    {Column: A, Row: <{Column: L; Row: <row where value == <current row>}>}
                    EXCEPT
                        "" if no matching row found
                )
            AND (
                {Column: B, Row: <{Column: N; Row: <row where value == <current row>}>}
                    EXCEPT
                        "" if no matching row found
                )
        )
    WITH
        "::"

#### LCO Code (from Row 3)

    =IFNA(INDIRECT("$A$"&MATCH(ROW(),$N$1:$N$100,0)) & "::" & INDIRECT("$B$"&MATCH(ROW(),$N$1:$N$100,0)),"")

### Column T; Row 3 - 165

#### Pseudo Code

    {Column: C, Row: <{Column: O; Row: <row where value == <current row>}>}
    EXCEPT
        "" if no matching row found

#### LCO Code (from Row 3)

    =IFNA(INDIRECT("$C$"&MATCH(ROW(),$O$1:$O$100,0)),"")

### Column V; Row 3 - 165

#### Pseudo Code

    {Column: A, Row: <{Column: O; Row: <row where value == <current row>}>}
    EXCEPT
        "" if no matching row found

#### LCO Code (from Row 3)

    =IFNA(INDIRECT("$A$"&MATCH(ROW(),$O$1:$O$100,0)),"")

### Column W; Row 3 - 165

#### Pseudo Code

    JOIN
        (
                (
                    {Column: A; Row <{Column O; Row: <row where value == <current row>}>}
                    EXCEPT
                        "" if no matching row found
                )
            AND (
                    {Column: B: Row: <{Column O; Row: <row where value == <current row>}>}
                    EXCEPT
                        "" if no matching row found
                )
    WITH
        "::"

#### LCO Code (from Row 3)

    =  IFNA(INDIRECT("$A$"&MATCH(ROW(),$O$1:$O$100,0))
     & "::"
     & INDIRECT("$B$"&MATCH(ROW(),$O$1:$O$100,0)),"")

### Column X; Row 3 - 165

#### Pseudo Code

    JOIN
        (
                (
                    {Column: A; Row <{Column O; Row: <row where value == <current row>}>}
                    EXCEPT
                        "" if no matching row found
                )
            AND (
                    {Column: B: Row: <{Column O; Row: <row where value == <current row>}>}
                    EXCEPT
                        "" if no matching row found
                )
            AND (
                    {Column: C: Row: <{Column O; Row: <row where value == <current row>}>}
                    EXCEPT
                        "" if no matching row found
                )
    WITH
        "::"

#### LCO Code (from Row 3)

    =  IFNA(INDIRECT("$A$"&MATCH(ROW(),$O$1:$O$100,0))
     & "::"
     & INDIRECT("$B$"&MATCH(ROW(),$O$1:$O$100,0)),"")
     & "::"
     & INDIRECT("$$"&MATCH(ROW(),$O$1:$O$100,0)),"")

### Column Y; Row 3 - 165

#### Pseudo Code

    {Column: E, Row: <{Column: O; Row: <row where value == <current row>}>}
    EXCEPT
        "" if no matching row found

#### LCO Code (from Row 3)

    =IFNA(INDIRECT("$E$"&MATCH(ROW(),$O$1:$O$100,0)),"")

### Column Z; Row 3 - 165

#### Pseudo Code

    IF
        {Column: D, Row: <{Column: O; Row: <row where value == <current row>}>} = "X"
    THEN
        -1
    ELSE
        0
    EXCEPT
        "" if no matching row found
#### LCO Code (from Row 3)

    =IFNA(IF(INDIRECT("$D$"&MATCH(ROW(),$O$1:$O$100,0))="X",-1,1), "")

</main>