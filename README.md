# utl-find-area-under-curve-and-compute-regression-slope-and-intercept-using-sqllite-r-python
Find area under curve and compute regression slope and intercept using sqllite r python
    %let pgm=utl-find-area-under-curve-and-compute-regression-slope-and-intercept-using-sqllite-r-python;

    Find area under curve and compute regression slope and intercept using sqllite r python;

    NOT RECOMMENDED, BUT INTERESTING (ONLY IN A PINCH IF NO OTHER LANGUAGE AVAILABLE)

        AREA UNDER CURVE TRAPEZOID RULE (Clinical AUC - ROC Curves)

           1 r sql trapozoid
           2 python sql trapozoid
           X sas sql  (could not get it to work, sas does not support a nested order clause?

        Regression Y=X

           3 r sql regression
           4 python sql regression
           5 sas proc sql

           6 related repos on end

    github
    https://tinyurl.com/yp7vzdd3
    https://github.com/rogerjdeangelis/utl-find-area-under-curve-and-compute-regression-slope-and-intercept-using-sqllite-r-python

    Related (non sql)
    https://github.com/rogerjdeangelis/utl-linear-regression-in-python-R-and-sas
    https://github.com/rogerjdeangelis/utl-area-under-y-equal-x-squared-from-0-to-1-trapezoid-auc-clinical

    /*                   _      __                    _ _
    (_)_ __  _ __  _   _| |_   / _| ___  _ __    __ _| | |
    | | `_ \| `_ \| | | | __| | |_ / _ \| `__|  / _` | | |
    | | | | | |_) | |_| | |_  |  _| (_) | |    | (_| | | |
    |_|_| |_| .__/ \__,_|\__| |_|  \___/|_|     \__,_|_|_|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.xy;
    input x y;
    cards4;
    0   0
    .2  .2
    .4  .4
    .6  .6
    .8  .8
    1  1

    ;;;;
    run;quit;

    /*                    _   _                                 _     _
    / |  _ __   ___  __ _| | | |_ _ __ __ _ _ __   ___ _______ (_) __| |
    | | | `__| / __|/ _` | | | __| `__/ _` | `_ \ / _ \_  / _ \| |/ _` |
    | | | |    \__ \ (_| | | | |_| | | (_| | |_) |  __// / (_) | | (_| |
    |_| |_|    |___/\__, |_|  \__|_|  \__,_| .__/ \___/___\___/|_|\__,_|
                       |_|                 |_|
    */

    %utl_rbeginx;
    parmcards4;
    library(sqldf);
    library(haven);
    xy<-read_sas("d:/sd1/xy.sas7bdat")
    area<-sqldf('
    SELECT (
     (SELECT MAX(x) FROM xy) - (SELECT MIN(x) FROM xy) )
       / (COUNT(*) - 1) *
       (
        0.5 * (SELECT y FROM xy ORDER BY x ASC LIMIT 1) +
        0.5 * (SELECT y FROM xy ORDER BY x DESC LIMIT 1) +
       (SELECT SUM(y) FROM xy
         WHERE x NOT IN (SELECT MIN(x) FROM xy)
           AND x NOT IN (SELECT MAX(x) FROM xy))
       ) AS integral
    FROM xy;
    ')
    why<-as.character(area$integral);
    print(why)
    str(why)
    why
    writeClipboard(paste("Area under y=x from 0 to 1 =", as.character(why)))
    ;;;;
    %utl_rendx(return=area);

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* From R                                                                                                                 */
    /* ======                                                                                                                 */
    /*                                                                                                                        */
    /* > area                                                                                                                 */
    /*   integral                                                                                                             */
    /*        0.5                                                                                                             */
    /*                                                                                                                        */
    /* From SAS                                                                                                               */
    /* ========                                                                                                               */
    /*                                                                                                                        */
    /* %put &=area;                                                                                                           */
    /*                                                                                                                        */
    /* AREA=Area under y=x from 0 to 1 = 0.5                                                                                  */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                _   _                             _   _                                 _     _
    |___ \   _ __  _   _| |_| |__   ___  _ __    ___  __ _| | | |_ _ __ __ _ _ __   ___ _______ (_) __| |
      __) | | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | | | __| `__/ _` | `_ \ / _ \_  / _ \| |/ _` |
     / __/  | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | | | |_| | | (_| | |_) |  __// / (_) | | (_| |
    |_____| | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|  \__|_|  \__,_| .__/ \___/___\___/|_|\__,_|
            |_|    |___/                                |_|                 |_|
    */

    %utl_pybeginx;
    parmcards4;
    import pyperclip;
    pyperclip.copy("roger");
    ;;;;
    %utl_pyendx(return=ret);

    %put &=ret;

    %utl_pybeginx;
    parmcards4;
    import pyperclip
    from os import path
    import pandas as pd
    import pyreadstat
    import numpy as np
    import pandas as pd
    from pandasql import sqldf
    mysql = lambda q: sqldf(q, globals())
    from pandasql import PandaSQL
    pdsql = PandaSQL(persist=True)
    sqlite3conn = next(pdsql.conn.gen).connection.connection
    sqlite3conn.enable_load_extension(True)
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll')
    mysql = lambda q: sqldf(q, globals())
    xy, meta = pyreadstat.read_sas7bdat("d:/sd1/xy.sas7bdat")
    print(xy);
    res = pdsql("""
    SELECT (
     (SELECT MAX(x) FROM xy) - (SELECT MIN(x) FROM xy) )
       / (COUNT(*) - 1) *
       (
        0.5 * (SELECT y FROM xy ORDER BY x ASC LIMIT 1) +
        0.5 * (SELECT y FROM xy ORDER BY x DESC LIMIT 1) +
       (SELECT SUM(y) FROM xy
         WHERE x NOT IN (SELECT MIN(x) FROM xy)
           AND x NOT IN (SELECT MAX(x) FROM xy))
       ) AS integral
    FROM xy;
    """)
    print(res)
    pyperclip.copy(str(res))
    ;;;;
    %utl_pyendx(return=area)

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Python                                                                                                                */
    /*  ======                                                                                                                */
    /*                                                                                                                        */
    /*     integral                                                                                                           */
    /*  0       0.5                                                                                                           */
    /*                                                                                                                        */
    /*  SAS                                                                                                                   */
    /*  ===                                                                                                                   */
    /*                                                                                                                        */
    /*     integral                                                                                                           */
    /*  0       0.5                                                                                                           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____                    _                                    _
    |___ /   _ __   ___  __ _| |  _ __ ___  __ _ _ __ ___  ___ ___(_) ___  _ __
      |_ \  | `__| / __|/ _` | | | `__/ _ \/ _` | `__/ _ \/ __/ __| |/ _ \| `_ \
     ___) | | |    \__ \ (_| | | | | |  __/ (_| | | |  __/\__ \__ \ | (_) | | | |
    |____/  |_|    |___/\__, |_| |_|  \___|\__, |_|  \___||___/___/_|\___/|_| |_|
                           |_|             |___/
    */

    %utl_rbeginx;
    parmcards4;
    library(sqldf);
    library(haven);
    xy<-read_sas("d:/sd1/xy.sas7bdat")
    reg<-sqldf('
    SELECT
        ((COUNT(*) * SUM(X*Y)) - (SUM(X) * SUM(Y))) /
        ((COUNT(*) * SUM(X*X)) - (SUM(X) * SUM(X))) AS SLOPE,
        (SUM(Y) - (((COUNT(*) * SUM(X*Y)) - (SUM(X) * SUM(Y))) /
        ((COUNT(*) * SUM(X*X)) - (SUM(X) * SUM(X)))) * SUM(X)) / COUNT(*) AS INTERCEPT
    FROM xy;
    ')
    reg;
    writeClipboard(paste("Intercept and Intercept =", as.character(reg$INTERCEPT), as.character(reg$SLOPE)))
    ;;;;
    %utl_rendx(return=slope_intercept);

    %put &=


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* R                                                                                                                      */
    /* =                                                                                                                      */
    /*                                                                                                                        */
    /* > reg;                                                                                                                 */
    /*   SLOPE INTERCEPT                                                                                                      */
    /* 1     1         0                                                                                                      */
    /*                                                                                                                        */
    /* SAS                                                                                                                    */
    /* ===                                                                                                                    */
    /*                                                                                                                        */
    /*  %put &=slope_intercept;                                                                                               */
    /*                                                                                                                        */
    /*  SLOPE_INTERCEPT=Intercept and Intercept = 0 1                                                                         */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*  _                 _   _                             _                                   _
    | || |    _ __  _   _| |_| |__   ___  _ __    ___  __ _| | _ __ ___  __ _ _ __ ___  ___ ___(_) ___  _ __
    | || |_  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | || `__/ _ \/ _` | `__/ _ \/ __/ __| |/ _ \| `_ \
    |__   _| | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | || | |  __/ (_| | | |  __/\__ \__ \ | (_) | | | |
       |_|   | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_||_|  \___|\__, |_|  \___||___/___/_|\___/|_| |_|
             |_|    |___/                                |_|            |___/
    */

    %utl_pybeginx;
    parmcards4;
    import pyperclip
    from os import path
    import pandas as pd
    import pyreadstat
    import numpy as np
    import pandas as pd
    from pandasql import sqldf
    mysql = lambda q: sqldf(q, globals())
    from pandasql import PandaSQL
    pdsql = PandaSQL(persist=True)
    sqlite3conn = next(pdsql.conn.gen).connection.connection
    sqlite3conn.enable_load_extension(True)
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll')
    mysql = lambda q: sqldf(q, globals())
    xy, meta = pyreadstat.read_sas7bdat("d:/sd1/xy.sas7bdat")
    print(xy);
    res = pdsql("""
    SELECT
        ((COUNT(*) * SUM(X*Y)) - (SUM(X) * SUM(Y))) /
        ((COUNT(*) * SUM(X*X)) - (SUM(X) * SUM(X))) AS SLOPE,
        (SUM(Y) - (((COUNT(*) * SUM(X*Y)) - (SUM(X) * SUM(Y))) /
        ((COUNT(*) * SUM(X*X)) - (SUM(X) * SUM(X)))) * SUM(X)) / COUNT(*) AS INTERCEPT
    FROM xy;
    """)
    res.info()
    print(res)
    SLOPE_INTERCEPT="SLOPE= " + str(res.iloc[0, 0]) + " INTERCEPT =" + " " + str(res.iloc[0, 1])
    pyperclip.copy(SLOPE_INTERCEPT)
    ;;;;
    %utl_pyendx(return=SLOPE_INTERCEPT);

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* PYTHON                                                                                                                 */
    /* ======                                                                                                                 */
    /*                                                                                                                        */
    /*    SLOPE  INTERCEPT                                                                                                    */
    /* 0    1.0        0.0                                                                                                    */
    /*                                                                                                                        */
    /* SAS                                                                                                                    */
    /* ===                                                                                                                    */
    /*                                                                                                                        */
    /* %put &=SLOPE_INTERCEPT;                                                                                                */
    /*                                                                                                                        */
    /* SLOPE_INTERCEPT=SLOPE= 1.0 INTERCEPT = 0.0                                                                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*___                              _                                   _
    | ___|   ___  __ _ ___   ___  __ _| | _ __ ___  __ _ _ __ ___  ___ ___(_) ___  _ __
    |___ \  / __|/ _` / __| / __|/ _` | || `__/ _ \/ _` | `__/ _ \/ __/ __| |/ _ \| `_ \
     ___) | \__ \ (_| \__ \ \__ \ (_| | || | |  __/ (_| | | |  __/\__ \__ \ | (_) | | | |
    |____/  |___/\__,_|___/ |___/\__, |_||_|  \___|\__, |_|  \___||___/___/_|\___/|_| |_|
                                    |_|            |___/
    */
    libname sd1 "d:/sd1";

    proc sql;
    SELECT
        ((COUNT(*) * SUM(X*Y)) - (SUM(X) * SUM(Y))) /
        ((COUNT(*) * SUM(X*X)) - (SUM(X) * SUM(X))) AS SLOPE,
        (SUM(Y) - (((COUNT(*) * SUM(X*Y)) - (SUM(X) * SUM(Y))) /
        ((COUNT(*) * SUM(X*X)) - (SUM(X) * SUM(X)))) * SUM(X)) / COUNT(*) AS INTERCEPT
    FROM sd1.xy;
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The SAS System                                                                                                         */
    /*                                                                                                                        */
    /*    SLOPE  INTERCEPT                                                                                                    */
    /* -------------------                                                                                                    */
    /*       1          0                                                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*        _       _           _
     _ __ ___| | __ _| |_ ___  __| |  _ __ ___ _ __   ___  ___
    | `__/ _ \ |/ _` | __/ _ \/ _` | | `__/ _ \ `_ \ / _ \/ __|
    | | |  __/ | (_| | ||  __/ (_| | | | |  __/ |_) | (_) \__ \
    |_|  \___|_|\__,_|\__\___|\__,_| |_|  \___| .__/ \___/|___/
                                              |_|
    */

    REPO
    ------------------------------------------------------------------------------------------------------------------------------------
    https://github.com/rogerjdeangelis/utl-betas-for-rolling-regressions
    https://github.com/rogerjdeangelis/utl-calculate-regression-coeficients-in-base-sas-fcmp-proc-reg-r-and-python
    https://github.com/rogerjdeangelis/utl-calculate-the-regression-slope-for-each-patient-by-treatment
    https://github.com/rogerjdeangelis/utl-drop-down-to-python-for-a-regression-sas-python-interface
    https://github.com/rogerjdeangelis/utl-generate-all-possible-paiwise-interactions-products-regression
    https://github.com/rogerjdeangelis/utl-linear-regression-in-python-R-and-sas
    https://github.com/rogerjdeangelis/utl-locating-breakpoints-for-dogleg-mutiple-regression-lines
    https://github.com/rogerjdeangelis/utl-maximum-liklihood-regresssion-wps-python-sympy
    https://github.com/rogerjdeangelis/utl-outlier-analysis-based-on-robust-regression
    https://github.com/rogerjdeangelis/utl-piecewise-regression-find-the-breakpoint
    https://github.com/rogerjdeangelis/utl-random-forest-regression-vs-linear-regression-with-uncorrelated-independent-variables-in-r
    https://github.com/rogerjdeangelis/utl-regression-line-plus-and-minus-the-interquartile-range-of-dependent-variable
    https://github.com/rogerjdeangelis/utl-scatter-plot-with-regression-line-coefficients-and-pvalue-in-one-datastep-sgplot
    https://github.com/rogerjdeangelis/utl-simple-example-of-meta-regression-using-SAS-and-R
    https://github.com/rogerjdeangelis/utl-using-linear-regression-with-base-sas-and-r-to-interpolate-missimg-values
    https://github.com/rogerjdeangelis/utl-using-the-regression-equation-to-score-a-table-like_a_weighted-sum
    https://github.com/rogerjdeangelis/utl_dosubl_do_regressions_when_data_is_between_dates
    https://github.com/rogerjdeangelis/utl_excluding_rolling_regressions_with_one_on_more_missing_values_in_the_window
    https://github.com/rogerjdeangelis/utl_how_to_automate_a_series_of_logistic_regressions


    https://github.com/rogerjdeangelis/utl-area-of-intersection-of-arbitrary-polygons-R-AI
    https://github.com/rogerjdeangelis/utl-area-under-y-equal-x-squared-from-0-to-1-trapezoid-auc-clinical
    https://github.com/rogerjdeangelis/utl-calculate-the-area-within-a-latittude-longitude-polygon
    https://github.com/rogerjdeangelis/utl-r-compute-the-area--of-an-image-which-is-under-a-curve-AI-image-processing-AI
    https://github.com/rogerjdeangelis/utl-r-python-compute-the-area-between-two-curves-AI-sympy-trapezoid
    https://github.com/rogerjdeangelis/utl_calculating_the_area_between_a_curve_and_a_straight_line
    https://github.com/rogerjdeangelis/utl_monte_carlo_simulation_to_determine_the_area_under_a_non_integrable_function

     /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
