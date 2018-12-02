# utl-no-need-for-sql-or-sort-merge-use-a-elegant-hash-excel-vlookup
No need for sql or sort merge use a elegant hash excel vlookup
    No need for sql or sort merge use a elegant hash excel vlookup

      Simplest possible HASH algorithm?

      Two Solutions

          1. HASH (I dont think there is a sipler HASH)
          2. SQL
          3. Sort merge not shown


    Stackoverflow
    https://stackoverflow.com/questions/53523026/sas-vlookup-in-the-same-table

    Kiran  (I made slight changes)
    https://stackoverflow.com/users/4619477/kiran

    Tom
    https://stackoverflow.com/users/4965549/tom


    INPUT
    =====

     WORK.HAVE total obs=5

                          |              RULES
                          |              -----
      col    col2    val  | val2                       col2  col
                          |                            ----  ---
      V1      V2      3   |  6   val2=6 when col2=col ('V2'='V2')
      V2      V1      6   |  3   val2=3 when col2=col ('V1'='V1')
      V3      V5      4   |  5   val2=5 when col2=col ('V5'='V5')
      V4      V1      3   |  3   val2=3 when col2=col ('V1'='V1')
      V5      V2      6   |  6   val2=6 when col=col2 ('V2'='V2')


    EXAMPLE OUTPUT
    --------------

     WORK.WANT total obs=5

       col    col2    val    new

       V1      V2      3      6
       V2      V1      6      3
       V3      V5      4      5
       V4      V1      1      3
       V5      V2      5      6


    PROCESS
    =======

    1. HASH (I dont think there is a sipler HASH)
    ---------------------------------------------

    data want ;
      set have;
      if _n_=1 then do;
        declare hash h(dataset: 'have');
        h.definekey('col');
        h.definedata('val');
        h.definedone();
      end;
      if h.find(key: col2)=0 then new=val; * match col2=col1 get value;
    run;quit;


    2. SQL
    ------

    proc sql;
      create
         table want as
      select
         l.col
        ,l.val
        ,l.col2
        ,r.val as val2
      from
         have l left join have r
       on
        l.col2 =r.col
      order
        by col
    ;quit;

    OUTPUT
    ======
    see above

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;


    data have;
     retain col col2 val;
     input col$ val col2$;
    cards4;
    V1 3 V2
    V2 6 V1
    V3 4 V5
    V4 1 V1
    V5 5 V2
    ;;;;
    run;quit;

