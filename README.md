# utl_select-first-occurance-in-an-unsorted-list
A nice hash example. Select first occurance in an unsorted list

    A nice hash example. Select first occurance in an unsorted list

    github
    https://tinyurl.com/u4zsy6n
    https://github.com/rogerjdeangelis/utl_select-first-occurance-in-an-unsorted-list

    SAS Forum
    https://tinyurl.com/s2ow2mx
    https://communities.sas.com/t5/SAS-Programming/Selecting-rows-with-first-unique-observation/m-p/626414

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;
    data have;
    informat id $2. medicines $11. start_date end_date date10.;
    input id medicines Start_date End_date;
    format Start_date End_date date9.;
    cards4;
    1 a,b,c,d 01jan2012 02dec2012
    1 a,e,g 03dec2012 03dec2014
    1 d,b 04dec2014 .
    2 a,b,c,d,f 01jan2012 02dec2012
    2 a,e,g 03dec2012 03dec2014
    2 b,a,c,d 04dec2014 06dec2016
    2 f,a 07dec2017 11mar2019
    2 f 12mar2019 .
    ;;;;
    run;quit;


    HAVE total obs=8

                               START_
    Obs    ID    MEDICINES      DATE        END_DATE

     1     1     a,b,c,d      01JAN2012    02DEC2012
     2     1     a,e,g        03DEC2012    03DEC2014
     3     1     d,b          04DEC2014            .

     4     2     a,b,c,d,f    01JAN2012    02DEC2012
     5     2     a,e,g        03DEC2012    03DEC2014
     6     2     b,a,c,d      04DEC2014    06DEC2016
     7     2     f,a          07DEC2017    11MAR2019
     8     2     f            12MAR2019            .


    *           _
     _ __ _   _| | ___  ___
    | '__| | | | |/ _ \/ __|
    | |  | |_| | |  __/\__ \
    |_|   \__,_|_|\___||___/

    ;

    Dates are refreshed for all members of aset id at least one token is new.

                               START_
    Obs    ID    MEDICINES      DATE        END_DATE

     1     1     a,b,c,d      01JAN2012    02DEC2012
     2     1     a,e,g        03DEC2012    03DEC2014

     3     1     d,b          04DEC2014            .  Do not output since we already have d and b above


     4     2     a,b,c,d,f    01JAN2012    02DEC2012  Always output the first there are no priors
     5     2     a,e,g        03DEC2012    03DEC2014  Output because e and g are new
     6     2     b,a,c,d      04DEC2014    06DEC2016  We have them all do not output
     7     2     f,a          07DEC2017    11MAR2019  We have them all do not output
     8     2     f            12MAR2019            .

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    WORK.WANT total obs=4

                               start_
    Obs    id    medicines      date       end_date

     1     1     a,b,c,d      01JAN2012    02DEC2012
     2     1     a,e,g        03DEC2012    03DEC2014
     3     2     a,b,c,d,f    01JAN2012    02DEC2012
     4     2     a,e,g        03DEC2012    03DEC2014

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;


    data want (drop=m i flag);
        declare hash h ();
        h.definekey ('id', 'm');
        h.definedone();

        do until (lr);
            set have end=lr;
            flag=0;
            do i = 1 to countw(medicines, ',');
               m = scan(medicines, i, ',');
               if h.check() ne 0 then flag=1;
               h.ref();
            end;
            if flag then output;
        end;
    run;

