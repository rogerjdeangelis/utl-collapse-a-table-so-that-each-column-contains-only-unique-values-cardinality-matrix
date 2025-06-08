# utl-collapse-a-table-so-that-each-column-contains-only-unique-values-cardinality-matrix
Collapse a table so that each column contains only unique values
    %let pgm=utl-collapse-a-table-so-that-each-column-contains-only-unique-values-cardinality-matrix;

    %stop_submission;

    Collapse a table so that each column contains only unique values

    Create this ouput with unique values on each column is a table

       CONTENTS
         1 uniques macro

         2 summary and update
           nice combination of proc summary and update
           Keintz, Mark
           mkeintz@outlook.com

         3 Save macro in autocall

    INPUT
    WORK.PRDSALE total obs=1,440

    OUTPUT
    PRODUCT COUNTRY REGION DIVISION  PRODTYPE

    BED      CANADA  EAST  CONSUMER  FURNITURE
    CHAIR    GERMANY WEST  EDUCATION OFFICE
    DESK     U.S.A.
    SOFA
    TABLE

    github
    https://tinyurl.com/mu9synkw
    https://github.com/rogerjdeangelis/utl-collapse-a-table-so-that-each-column-contains-only-unique-values-cardinality-matrix


    /***************************************************************************************************************************/
    /*              INPUT                             |        PROCESS             |            OUTPUT                         */
    /*              =====                             |        =======             |            ======                         */
    /*                                                |                            |                                           */
    /* WORK.PRDSALE total obs=1,440                   |1 UNIQUES MACRO             |                                           */
    /*                                                |===============             | If you call without any datasets(log)     */
    /*  Obs COUNTRY REGION  DIVISION  PRODTYPE PRODUCT|                            |                                           */
    /*                                                | 1 use freq to get nlevels  | **** Please provide inp dataset  ****     */
    /*    1  CANADA   EAST  EDUCATION FURNITURE  SOFA |   sort fesc nlevels        | **** Please provide out dataset  ****     */
    /*    2  CANADA   EAST  EDUCATION FURNITURE  SOFA |   COLUMN     NLEVELS       |                                           */
    /*    3  CANADA   EAST  EDUCATION FURNITURE  SOFA |                            | If you call without inp dataset           */
    /*    4  CANADA   EAST  EDUCATION FURNITURE  SOFA |   PRODUCT       5          |                                           */
    /*    5  CANADA   EAST  EDUCATION FURNITURE  SOFA |   COUNTRY       3          | **** Please provide inp agument  ****     */
    /*  ....                                          |   REGION        2          |                                           */
    /* 1436  U.S.A.   WEST  CONSUMER  OFFICE     DESK |   DIVISION      2          | If you call without out dataset           */
    /* 1437  U.S.A.   WEST  CONSUMER  OFFICE     DESK |   PRODTYPE      2          |                                           */
    /* 1438  U.S.A.   WEST  CONSUMER  OFFICE     DESK |                            | **** Please provide out dataset  ****     */
    /* 1439  U.S.A.   WEST  CONSUMER  OFFICE     DESK | 2 call dosubl for each row |                                           */
    /* 1440  U.S.A.   WEST  CONSUMER  OFFICE     DESK |   table of nlevels         |                                           */
    /*                                                |   select                   | PRODUCT COUNTRY REGION DIVISION  PRODTYPE */
    /*                                                |      distinct &column      |                                           */
    /* data prdsale;                                  |    from                    | BED      CANADA  EAST  CONSUMER  FURNITURE*/
    /*   set sashelp.prdsale(keep=_character_);       |       prdsale              | CHAIR    GERMANY WEST  EDUCATION OFFICE   */
    /* run;quit;                                      |                            | DESK     U.S.A.                           */
    /*                                                | Sample call                | SOFA                                      */
    /*                                                |                            | TABLE                                     */
    /*                                                | %utl_uniques(prdsale,want) |                                           */
    /*                                                |                            |                                           */
    /*                                                |------------------------------------------------------------------------*/
    /*                                                |                            |                                           */
    /*                                                | 2 SUMMARY AND UPDATE       | SUMMARY OUTPUT  WORK.NEED                 */
    /*                                                |   Keintz, Mark             |                                           */
    /*                                                |   mkeintz@outlook.com      | COUNTRY REGION DIVISION  PRODTYPE  _TYPE_ */
    /*                                                |   also see below           |                                           */
    /*                                                | =====================      |                                           */
    /*                                                |                            |                                           */
    /*                                                | %let vars=country          |                          FURNITURE    1   */
    /*                                                |          region            |                          OFFICE       1   */
    /*                                                |         division           |                CONSUMER               2   */
    /*                                                |         prodtype;          |                EDUCATION              2   */
    /*                                                |                            |          EAST                         4   */
    /*                                                |  proc summary data=        |          WEST                         4   */
    /*                                                |    sashelp.prdsale missing;| CANADA                                8   */
    /*                                                |   class &vars;             | GERMANY                               8   */
    /*                                                |   ways 1;                  | U.S.A.                                8   */
    /*                                                |   output out=need(         |-------------------------------------------*/
    /*                                                |     drop=_freq_);          | WORK.NEED2                                */
    /*                                                |  run;                      |                                    ROW    */
    /*                                                |                            |                                TO UPDATE  */
    /*                                                |  data need2(drop=_type_) ; |                                           */
    /*                                                |    set need;               | COUNTRY REGION DIVISION  PRODTYPE   _ROW  */
    /*                                                |    by _type_;              |                                           */
    /*                                                |    _row+1;                 |                          FURNITURE    1   */
    /*                                                |    if first._type_         |                          OFFICE       2   */
    /*                                                |       then _row=1;         |                CONSUMER               1   */
    /*                                                |  run;                      |                EDUCATION              2   */
    /*                                                |                            |          EAST                         1   */
    /*                                                |  proc sort ;               |          WEST                         2   */
    /*                                                |    by _row ;               | CANADA                                1   */
    /*                                                |  run;                      | GERMANY                               2   */
    /*                                                |                            | U.S.A.                                3   */
    /*                                                |  data want (keep=&vars);   |                                           */
    /*                                                |    update need2 (obs=0)    |-------------------------------------------*/
    /*                                                |           need2;           | FINAL WOR.WANT                            */
    /*                                                |    by _row;                |                                           */
    /*                                                |  run;                      | COUNTRY REGION DIVISION  PRODTYPE         */
    /*                                                |                            |                                           */
    /*                                                |                            | CANADA   EAST  CONSUMER  FURNITURE        */
    /*                                                |                            | GERMANY  WEST  EDUCATION OFFICE           */
    /*                                                |                            | U.S.A.                                    */
    /*------------------------------------------------|------------------------------------------------------------------------*/
    /*                                                | 3 SAVE MACRO IN AUTOCALL                                               */
    /*                                                |   see below                                                            */
    /*                                                | ========================                                               */
    /*                                                |                                                                        */
    /*                                                |  proc datasets lib=work                                                */
    /*                                                |   nolist nodetails ;                                                   */
    /*                                                |   delete sasmac1 sasmac2 sasmac3 /  mt=cat;                            */
    /*                                                |   delete want prewant nlevels ;                                        */
    /*                                                |  run;quit;                                                             */
    /*                                                |                                                                        */
    /*                                                |  proc catalog catalog=work.sasmacr;                                    */
    /*                                                |   delete utl_uniques.macro / et=macro;                                 */
    /*                                                |  run;quit;                                                             */
    /*                                                |                                                                        */
    /*                                                |  %symdel want tablevar recno / nowarn;                                 */
    /*                                                |                                                                        */
    /*                                                |                                                                        */
    /*                                                |  %macro utl_uniques(inp,out)                                           */
    /*                                                |    /des="collapse to unique values";                                   */
    /*                                                |                                                                        */
    /*                                                |   /*----                                                               */
    /*                                                |     %let inp=prdsale;                                                  */
    /*                                                |     %let out=want;                                                     */
    /*                                                |   ----*/                                                               */
    /*                                                |                                                                        */
    /*                                                |   %local recno;                                                        */
    /*                                                |                                                                        */
    /*                                                |   %put %sysfunc(ifc(%sysevalf(%superq(inp )=,boolean)                  */
    /*                                                |        ,**** Please provide inp dataset ****,));                       */
    /*                                                |   %put %sysfunc(ifc(%sysevalf(%superq(out )=,boolean)                  */
    /*                                                |        ,**** Please provide out dataset ****,));                       */
    /*                                                |                                                                        */
    /*                                                |    %let res= %eval                                                     */
    /*                                                |    (                                                                   */
    /*                                                |        %sysfunc(ifc(%sysevalf(%superq(inp )=,boolean),1,0))            */
    /*                                                |      + %sysfunc(ifc(%sysevalf(%superq(out )=,boolean),1,0))            */
    /*                                                |    );                                                                  */
    /*                                                |                                                                        */
    /*                                                |     %if &res = 0 %then %do;                                            */
    /*                                                |                                                                        */
    /*                                                |        data _null_;                                                    */
    /*                                                |                                                                        */
    /*                                                |          /*---  levels for each column ----*/                          */
    /*                                                |          %dosubl('                                                     */
    /*                                                |            ods exclude all;                                            */
    /*                                                |            ods output nlevels=nlevels;                                 */
    /*                                                |            proc freq data=&inp nlevels;                                */
    /*                                                |            run;quit;                                                   */
    /*                                                |            ods exclude none;                                           */
    /*                                                |            ods listing;                                                */
    /*                                                |                                                                        */
    /*                                                |            proc sort data=nlevels;                                     */
    /*                                                |              by descending nlevels;                                    */
    /*                                                |            run;quit;                                                   */
    /*                                                |            ')                                                          */
    /*                                                |                                                                        */
    /*                                                |          set nlevels;                                                  */
    /*                                                |                                                                        */
    /*                                                |          call symputx("tablevar",tablevar);                            */
    /*                                                |          call symputx("recno",_n_);                                    */
    /*                                                |                                                                        */
    /*                                                |          rc=dosubl('                                                   */
    /*                                                |                                                                        */
    /*                                                |            proc sql;                                                   */
    /*                                                |              create                                                    */
    /*                                                |                table &out  as                                          */
    /*                                                |              select                                                    */
    /*                                                |                distinct &tablevar                                      */
    /*                                                |             from                                                       */
    /*                                                |                &inp                                                    */
    /*                                                |             ;quit;                                                     */
    /*                                                |                                                                        */
    /*                                                |            %if &recno > 1 %then %do;                                   */
    /*                                                |              data &out;                                                */
    /*                                                |                 merge                                                  */
    /*                                                |                   prewant(in=unq) &out ;                               */
    /*                                                |                 if unq;                                                */
    /*                                                |              run;quit;                                                 */
    /*                                                |            %end;                                                       */
    /*                                                |                                                                        */
    /*                                                |            data prewant;                                               */
    /*                                                |              set &out;                                                 */
    /*                                                |            run;quit;                                                   */
    /*                                                |                                                                        */
    /*                                                |                                                                        */
    /*                                                |            ');                                                         */
    /*                                                |        run;quit;                                                       */
    /*                                                |     %end;                                                              */
    /*                                                |                                                                        */
    /*                                                |  %mend utl_uniques;                                                    */
    /***************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data prdsale;
      set sashelp.prdsale(keep=_character_);
    run;quit;

    /***************************************************************************************************************************/
    /* WORK.PRDSALE total obs=1,440                                                                                            */
    /*                                                                                                                         */
    /*  Obs COUNTRY REGION  DIVISION  PRODTYPE PRODUCT                                                                         */
    /*                                                                                                                         */
    /*    1  CANADA   EAST  EDUCATION FURNITURE  SOFA                                                                          */
    /*    2  CANADA   EAST  EDUCATION FURNITURE  SOFA                                                                          */
    /*    3  CANADA   EAST  EDUCATION FURNITURE  SOFA                                                                          */
    /*    4  CANADA   EAST  EDUCATION FURNITURE  SOFA                                                                          */
    /*    5  CANADA   EAST  EDUCATION FURNITURE  SOFA                                                                          */
    /*  ....                                                                                                                   */
    /* 1436  U.S.A.   WEST  CONSUMER  OFFICE     DESK                                                                          */
    /* 1437  U.S.A.   WEST  CONSUMER  OFFICE     DESK                                                                          */
    /* 1438  U.S.A.   WEST  CONSUMER  OFFICE     DESK                                                                          */
    /* 1439  U.S.A.   WEST  CONSUMER  OFFICE     DESK                                                                          */
    /* 1440  U.S.A.   WEST  CONSUMER  OFFICE     DESK                                                                          */
    /***************************************************************************************************************************/

    /*               _
    / |  _   _ _ __ (_) __ _ _   _  ___  ___   _ __ ___   __ _  ___ _ __ ___
    | | | | | | `_ \| |/ _` | | | |/ _ \/ __| | `_ ` _ \ / _` |/ __| `__/ _ \
    | | | |_| | | | | | (_| | |_| |  __/\__ \ | | | | | | (_| | (__| | | (_) |
    |_|  \__,_|_| |_|_|\__, |\__,_|\___||___/ |_| |_| |_|\__,_|\___|_|  \___/
                          |_|
    */


    %utl_uniques(prdsale,want);

    /**************************************************************************************************************************/
    /* PRODUCT    COUNTRY    REGION    DIVISION     PRODTYPE                                                                  */
    /*                                                                                                                        */
    /*  BED       CANADA      EAST     CONSUMER     FURNITURE                                                                 */
    /*  CHAIR     GERMANY     WEST     EDUCATION    OFFICE                                                                    */
    /*  DESK      U.S.A.                                                                                                      */
    /*  SOFA                                                                                                                  */
    /*  TABLE                                                                                                                 */
    /**************************************************************************************************************************/

    /*___                                                                     _                  _       _
    |___ \   ___ _   _ _ __ ___  _ __ ___   __ _ _ __ _   _    __ _ _ __   __| | _   _ _ __   __| | __ _| |_ ___
      __) | / __| | | | `_ ` _ \| `_ ` _ \ / _` | `__| | | |  / _` | `_ \ / _` || | | | `_ \ / _` |/ _` | __/ _ \
     / __/  \__ \ |_| | | | | | | | | | | | (_| | |  | |_| | | (_| | | | | (_| || |_| | |_) | (_| | (_| | ||  __/
    |_____| |___/\__,_|_| |_| |_|_| |_| |_|\__,_|_|   \__, |  \__,_|_| |_|\__,_| \__,_| .__/ \__,_|\__,_|\__\___|
                                                      |___/                           |_|
    */

    %let vars=country region division prodtype;

    proc summary data=
      sashelp.prdsale missing;
     class &vars;
     ways 1;
     output out=need(
       drop=_freq_);
    run;

    data need2(drop=_type_) ;
      set need;
      by _type_;
      _row+1;
      if first._type_
         then _row=1;
    run;

    proc sort ;
      by _row ;
    run;

    data want (keep=&vars);
      update need2 (obs=0)
             need2;
      by _row;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   SUMMARY OUTPUT  WORK.NEED                                                                                            */
    /*                                                                                                                        */
    /*   COUNTRY REGION DIVISION  PRODTYPE  _TYPE_                                                                            */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                            FURNITURE    1                                                                              */
    /*                            OFFICE       1                                                                              */
    /*                  CONSUMER               2                                                                              */
    /*                  EDUCATION              2                                                                              */
    /*            EAST                         4                                                                              */
    /*            WEST                         4                                                                              */
    /*   CANADA                                8                                                                              */
    /*   GERMANY                               8                                                                              */
    /*   U.S.A.                                8                                                                              */
    /*---------------------------------------------                                                                           */
    /*   WORK.NEED2                                                                                                           */
    /*                                      ROW                                                                               */
    /*                                  TO UPDATE                                                                             */
    /*                                                                                                                        */
    /*   COUNTRY REGION DIVISION  PRODTYPE   _ROW                                                                             */
    /*                                                                                                                        */
    /*                            FURNITURE    1                                                                              */
    /*                            OFFICE       2                                                                              */
    /*                  CONSUMER               1                                                                              */
    /*                  EDUCATION              2                                                                              */
    /*            EAST                         1                                                                              */
    /*            WEST                         2                                                                              */
    /*   CANADA                                1                                                                              */
    /*   GERMANY                               2                                                                              */
    /*   U.S.A.                                3                                                                              */
    /*                                                                                                                        */
    /*---------------------------------------------                                                                           */
    /*   FINAL WOR.WANT                                                                                                       */
    /*                                                                                                                        */
    /*   COUNTRY REGION DIVISION  PRODTYPE                                                                                    */
    /*                                                                                                                        */
    /*   CANADA   EAST  CONSUMER  FURNITURE                                                                                   */
    /*   GERMANY  WEST  EDUCATION OFFICE                                                                                      */
    /**************************************************************************************************************************/

    /*____   _                 _                                               _                  _ _
    |___ /  | | ___   __ _  __| |  _ __ ___   __ _  ___ _ __ ___    __ _ _   _| |_ ___   ___ __ _| | |
      |_ \  | |/ _ \ / _` |/ _` | | `_ ` _ \ / _` |/ __| `__/ _ \  / _` | | | | __/ _ \ / __/ _` | | |
     ___) | | | (_) | (_| | (_| | | | | | | | (_| | (__| | | (_) || (_| | |_| | || (_) | (_| (_| | | |
    |____/  |_|\___/ \__,_|\__,_| |_| |_| |_|\__,_|\___|_|  \___/  \__,_|\__,_|\__\___/ \___\__,_|_|_|
    */

    /*---- for development ----*/
    proc datasets lib=work
     nolist nodetails ;
     delete sasmac1 sasmac2 sasmac3 /  mt=cat;
     delete want prewant nlevels ;
    run;quit;

    proc catalog catalog=work.sasmacr;
     delete utl_uniques.macro / et=macro;
    run;quit;

    %symdel want tablevar recno / nowarn;

    filename ft15f001 "c:/oto/utl_uniques.sas";
    parmcards4;
    %macro utl_uniques(inp,out)
      /des="collapse to unique values";

     /*----
       %let inp=prdsale;
       %let out=want;
     ----*/

     %local recno;

     %put %sysfunc(ifc(%sysevalf(%superq(inp )=,boolean)
          ,**** Please provide inp dataset ****,));
     %put %sysfunc(ifc(%sysevalf(%superq(out )=,boolean)
          ,**** Please provide out dataset ****,));

      %let res= %eval
      (
          %sysfunc(ifc(%sysevalf(%superq(inp )=,boolean),1,0))
        + %sysfunc(ifc(%sysevalf(%superq(out )=,boolean),1,0))
      );

       %if &res = 0 %then %do;

          data _null_;

            /*---  levels for each column ----*/
            %dosubl('
              ods exclude all;
              ods output nlevels=nlevels;
              proc freq data=&inp nlevels;
              run;quit;
              ods exclude none;
              ods listing;

              proc sort data=nlevels;
                by descending nlevels;
              run;quit;
              ')

            set nlevels;

            call symputx("tablevar",tablevar);
            call symputx("recno",_n_);

            rc=dosubl('

              proc sql;
                create
                  table &out  as
                select
                  distinct &tablevar
               from
                  &inp
               ;quit;

              %if &recno > 1 %then %do;
                data &out;
                   merge
                     prewant(in=unq) &out ;
                   if unq;
                run;quit;
              %end;

              data prewant;
                set &out;
              run;quit;


              ');
          run;quit;
       %end;

    %mend utl_uniques;
    ;;;;
    run;quit;
    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
