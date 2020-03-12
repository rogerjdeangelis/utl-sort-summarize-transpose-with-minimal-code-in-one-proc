# utl-sort-summarize-transpose-with-minimal-code-in-one-proc
Sort summarize transpose with minimal code in one proc
    Sort summarize transpose with minimal code in one proc

    github
    https://tinyurl.com/v5qy2xt
    https://github.com/rogerjdeangelis/utl-sort-summarize-transpose-with-minimal-code-in-one-proc

    related github repos
    https://github.com/rogerjdeangelis?tab=repositories&q=transpose&type=&language=

    I prefer an output table, rather than a static report?

       Three solutions

             1. SQL arrays (only a single proc solution maybe the fastest?- teradata, exadata))
             2. proc summary and transose macro
             3. 'proc report' output table is difficult to work with.
                 Best if all you need is a staic report.


    This is an interesting problem that 'proc report' does best, but the bug in proc report'
    makes the report output dataset hard to use.
    Proc report does not honor ods output.

    sas forum
    https://tinyurl.com/slb7l6x
    https://communities.sas.com/t5/SAS-Programming/chnage-structure-of-table-by-row-and-columns-points/m-p/631195


    Cynthia_sas
    https://communities.sas.com/t5/user/viewprofilepage/user-id/13549

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    Data have;
    infile datalines dsd;
    input Cat2_Y $ Cat1_Y $ Cat1_X $ Reduce Row Column ;
    cards4;
    No,SecondMax,No,15000,1,1
    No,SecondMax,10,14500,1,2
    No,SecondMax,20,12000,1,3
    No,Max,No,11500,1,4
    Yes,SecondMax,No,11000,2,1
    No,Max,10,10500,1,5
    Yes,SecondMax,10,10000,2,2
    No,Max,20,9500,1,6
    Yes,SecondMax,20,9000,2,3
    Yes,Max,No,8500,2,4
    Yes,Max,10,8000,2,5
    Yes,Max,20,5000,2,6
    Yes,SecondMax,No,4500,3,1
    Yes,SecondMax,10,4000,3,2
    Yes,Max,No,3500,3,4
    Yes,SecondMax,20,3000,3,3
    Yes,Max,10,2500,3,5
    Yes,Max,20,2000,3,6
    ;;;;
    run;quit;

    Up to 40 obs WORK.HAVE total obs=18

    Obs    Cat2_Y    Cat1_Y      Cat1_X    Reduce    Row    Column

      1     No       SecondMa      No       15000     1        1
      2     No       SecondMa      10       14500     1        2
      3     No       SecondMa      20       12000     1        3
      4     No       Max           No       11500     1        4
      5     Yes      SecondMa      No       11000     2        1
      6     No       Max           10       10500     1        5
      7     Yes      SecondMa      10       10000     2        2
      8     No       Max           20        9500     1        6
      9     Yes      SecondMa      20        9000     2        3
     10     Yes      Max           No        8500     2        4
     11     Yes      Max           10        8000     2        5
     12     Yes      Max           20        5000     2        6
     13     Yes      SecondMa      No        4500     3        1
     14     Yes      SecondMa      10        4000     3        2
     15     Yes      Max           No        3500     3        4
     16     Yes      SecondMa      20        3000     3        3
     17     Yes      Max           10        2500     3        5
     18     Yes      Max           20        2000     3        6

    *           _
     _ __ _   _| | ___  ___
    | '__| | | | |/ _ \/ __|
    | |  | |_| | |  __/\__ \
    |_|   \__,_|_|\___||___/

    ;

    * first sort for easy documentation (not done in all solutions)

    proc sort data=have out=havSrt noequals ;
    by row column;
    run;quit;

    Up to 40 obs WORK.HAVSRT total obs=18

    Obs    CAT2_Y    CAT1_Y      CAT1_X    REDUCE    ROW    COLUMN

      1     No       SecondMa      No       15000     1        1     First four colums below

      2     No       SecondMa      10       14500     1        2     Next four columns below ...

      3     No       SecondMa      20       12000     1        3     not shown
      4     No       Max           No       11500     1        4
      5     No       Max           10       10500     1        5
      6     No       Max           20        9500     1        6

      7     Yes      SecondMa      No       11000     2        1
      8     Yes      SecondMa      10       10000     2        2
      9     Yes      SecondMa      20        9000     2        3
     10     Yes      Max           No        8500     2        4
     11     Yes      Max           10        8000     2        5
     12     Yes      Max           20        5000     2        6


    Transpose by roow usin column for column names


    ROW    CAT2_Y_1    CAT1_Y_1    CAT1_X_1    REDUCE_1    CAT2_Y_2    CAT1_Y_2    CAT1_X_2    REDUCE_2

     1       No        SecondMa       No         15000       No          SecondMa     10        14500
     2       Yes       SecondMa       No         11000       ....


    *            _               _     _    ___     ____
      ___  _   _| |_ _ __  _   _| |_  / |  ( _ )   |___ \
     / _ \| | | | __| '_ \| | | | __| | |  / _ \/\   __) |
    | (_) | |_| | |_| |_) | |_| | |_  | | | (_>  <  / __/
     \___/ \__,_|\__| .__/ \__,_|\__| |_|  \___/\/ |_____|
                    |_|
    ;

    These two solutions give a a table we can work with;

    WORK.WANT total obs=3  (has useful variable names - slight rename would help ie C2Y C1Y C1X )

      ROW    CAT2_Y_1    CAT1_Y_1    CAT1_X_1    REDUCE_1    ...   CAT2_Y_6    CAT1_Y_6    CAT1_X_6    REDUCE_6

       1       No        SecondMa       No         15000     ...     No          Max          20         9500
       2       Yes       SecondMa       No         11000     ...     Yes         Max          20         5000
       3       Yes       SecondMa       No          4500     ...     Yes         Max          20         2000

    *            _               _                               _
      ___  _   _| |_ _ __  _   _| |_   _ __ ___ _ __   ___  _ __| |_
     / _ \| | | | __| '_ \| | | | __| | '__/ _ \ '_ \ / _ \| '__| __|
    | (_) | |_| | |_| |_) | |_| | |_  | | |  __/ |_) | (_) | |  | |_
     \___/ \__,_|\__| .__/ \__,_|\__| |_|  \___| .__/ \___/|_|   \__|
                    |_|                        |_|
    ;

    Could output to excel then import to get meaningful names;

    Up to 40 obs from HACRPT total obs=3

    Obs ROW _C2_   _C3_   _C4_  _C5_ _C6_ ... _C22_ _C23_ _C24_ _C25_

     1   1  No   SecondMa  No  15000 No   ...  No    Max   20    9500
     2   2  Yes  SecondMa  No  11000 Yes  ...  Yes   Max   20    5000
     3   3  Yes  SecondMa  No   4500 Yes  ...  Yes   Max   20    2000

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

     _               _
    / |    ___  __ _| |   __ _ _ __ _ __ __ _ _   _ ___
    | |   / __|/ _` | |  / _` | '__| '__/ _` | | | / __|
    | |_  \__ \ (_| | | | (_| | |  | | | (_| | |_| \__ \
    |_(_) |___/\__, |_|  \__,_|_|  |_|  \__,_|\__, |___/
                  |_|                         |___/
    ;

    * fastest with muti-core systems;

    %arraydelete(six);  * delete just un case;

    %array(six,values=1-6);

    proc sql;
      create
         table havSql1 as
      select
        *
      from
      %do_over(six,phrase=%str(
      (
      select
        row
       ,column
       ,CAT2_Y  as  CAT2_Y_?
       ,CAT1_Y  as  CAT1_Y_?
       ,CAT1_X  as  CAT1_X_?
       ,sum(REDUCE)  as  REDUCE_?
      from
        have
      where
        column=1
      group
        by row ,CAT2_Y ,CAT1_Y ,CAT1_X
      ) as t?),between=comma)
      where
      %do_over(six,phrase=%str(t1.row=t?.row),between=and);
    ;quit;

    NOTE: Table WORK.HAVSQL1 created, with 3 rows and 26 columns.

    *____      _
    |___ \    | |_ _ __ __ _ _ __  ___ _ __   ___  ___  ___
      __) |   | __| '__/ _` | '_ \/ __| '_ \ / _ \/ __|/ _ \
     / __/ _  | |_| | | (_| | | | \__ \ |_) | (_) \__ \  __/
    |_____(_)  \__|_|  \__,_|_| |_|___/ .__/ \___/|___/\___|
                                      |_|
     _ __ ___   __ _  ___ _ __ ___
    | '_ ` _ \ / _` |/ __| '__/ _ \
    | | | | | | (_| | (__| | | (_) |
    |_| |_| |_|\__,_|\___|_|  \___/

    ;

    * sum reduce;
    proc summary data=have missing nway sum;
    class row Cat2_Y Cat1_Y Cat1_X Reduce Column ;
    var reduce;
    output out=havsum(drop=_:) sum=reduce;
    run;quit;

    %utl_transpose(data=havsum, out=want, by=row, id=column, guessingrows=1000,
     delimiter=_, var=Cat2_Y Cat1_Y Cat1_X Reduce);

    *_____                              _
    |___ /    _ __ ___ _ __   ___  _ __| |_
      |_ \   | '__/ _ \ '_ \ / _ \| '__| __|
     ___) |  | | |  __/ |_) | (_) | |  | |_
    |____(_) |_|  \___| .__/ \___/|_|   \__|
                      |_|
    ;

    proc sort data=have out=havSrt noequals ;
    by row column;
    run;quit;

    proc report data=havSrt nowd missing out=havRpt;
    cols row column, (cat2_y cat1_y cat1_x reduce);
    define row / group;
    define column / across;
    define reduce / sum;
    run;quit;

    Not very useful output;

    Up to 40 obs from HAVRPT total obs=3

    Obs ROW _C2_   _C3_   _C4_  _C5_ _C6_   _C7_   _C8_  _C9_ _C10_  _C11_   _C12_ _C13_ _C14_ _C15_ _C16_ _C17_ _C18_ _C19_ _C20_ _C21_ _C22_ _C23_ _C24_ _C25_ _BR

     1   1  No   SecondMa  No  15000 No   SecondMa  10  14500  No   SecondMa  20   12000  No    Max   No   11500  No    Max   10   10500  No    Max   20    9500
     2   2  Yes  SecondMa  No  11000 Yes  SecondMa  10  10000  Yes  SecondMa  20    9000  Yes   Max   No    8500  Yes   Max   10    8000  Yes   Max   20    5000
     3   3  Yes  SecondMa  No   4500 Yes  SecondMa  10   4000  Yes  SecondMa  20    3000  Yes   Max   No    3500  Yes   Max   10    2500  Yes   Max   20    2000

