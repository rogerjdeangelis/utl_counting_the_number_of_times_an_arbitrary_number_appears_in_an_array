# utl_counting_the_number_of_times_an_arbitrary_number_appears_in_an_array
Counting the number of times an arbitrary number appears in an array. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Counting the number of times an arbitrary number appears in an array

    github
    https://tinyurl.com/ydzb7oqk
    https://github.com/rogerjdeangelis/utl_counting_the_number_of_times_an_arbitrary_number_appears_in_an_array

    see SAS Forum
    https://tinyurl.com/y8shgx4e
    https://communities.sas.com/t5/Base-SAS-Programming/Array-to-replace-if-then-statement-9-4/m-p/446578

       Two Solutions

         1 Base SAS  (failed in WPS? Not sure why? passed=5 without error)
         2 WPS/PROC R or IML/R


    INPUT
    =====

      Count the number of 64s in array scores

      array scores[5] (64,2,64,3,4);


      EXAMPLE OUTPUT

        passed=2 (because two elements have 64)


    PROCESS
    =======

      1. Base SAS

         data _null_;
          array scores[5] (64,2,64,3,4);
          * get address of scores and check 40 bytes for the count of binary float 64;
          adr    = addrlong(scores[1]);
          chrs   = peekclong(adr,dim(scores)*8);
          passed = count(chrs,put(64,rb8.));

          put passed=;
         run;quit;

      2. WPS/PROC R (Working code)

         sum(scores==64L);


    OUTPUT  (in the log)
    ======

      SAS
      PASSED=2

      WPS/Proc R
      The WPS System

         [1] 2

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    * SAS;
    data want;
      array scores[5] (64,2,64,3,4);
      adr=addrlong(scores[1]);
      chrs=peekclong(adr,40);
      passed=count(chrs,put(64,rb8.));
      put passed=;
    run;quit;


    * R;

    %utl_submit_wps64('
    options set=R_HOME "C:/Program Files/R/R-3.3.1";
    proc r;
    submit;
    scores=c(64,2,64,3,4);
    sum(scores==64L);
    endsubmit;
    run;quit;
    ');


    Bartosz Jablonski <yabwon@gmail.com>
    5:46 PM (15 hours ago)
    to me
    Hi Sir,

    I've just read your post and (since it inspired me a lot)
    I dare to show you my solution (which combines DoW-loop and
    Hash tables). I hope you enjoy it :-)

    regards
    Bart


    /* data */
    data have;
    input ID NAME $ :1. VAR $ :1.;
    cards;
    1 a x
    1 a y
    1 b y
    1 c x
    1 c y
    1 d z
    2 e x
    2 e y
    2 f x
    2 f z
    2 g z
    ;
    run;


    /* solution */

    data want;
    put "###";

    declare hash V(suminc: "counter");
    V.DefineKey("VAR");
    V.DefineDone();
    declare hiter IV("V");

    declare hash N();
    N.DefineKey("NAME");
    N.DefineDone();

    counter = 1;
    do until(last.ID);
        set have;
        by ID;

        rc_V = V.REF(); /*populate hashtables*/
        rc_N = N.REF();

        put (ID NAME VAR) (=);

        if last.ID then
        do; /* when in last record in group */
            put "@@@";
            totalitems = N.NUM_ITEMS; /* get total number of different names */
            put totalitems=;

            /**/
            rc_IV = IV.FIRST();
            do until(rc_IV); /* iterate through VAR's values and calculate ratio */
                rc_V  = V.sum(sum: total);

                RATIO = total / totalitems;
                put  ID= VAR= RATIO= best32.;
                keep ID  VAR  RATIO;
                output;
                rc_IV = IV.NEXT();
            end;
            /**/
        end;
    end;
    put "***";
    run;

