\#load data

    require("mpath")

    ## Loading required package: mpath

    ## Loading required package: glmnet

    ## Loading required package: Matrix

    ## Loaded glmnet 4.1-7

    ## Loading required package: pamr

    ## Loading required package: cluster

    ## Loading required package: survival

    library("plotrix")
    require("zic")

    ## Loading required package: zic

    require("pscl")

    ## Loading required package: pscl

    ## Classes and Methods for R developed in the
    ## Political Science Computational Laboratory
    ## Department of Political Science
    ## Stanford University
    ## Simon Jackman
    ## hurdle and zeroinfl functions by Achim Zeileis

    library("purrr")
    library("caret")

    ## Loading required package: ggplot2

    ## Loading required package: lattice

    ## 
    ## Attaching package: 'caret'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     lift

    ## The following object is masked from 'package:survival':
    ## 
    ##     cluster

    library("tidyverse")

    ## ── Attaching packages
    ## ───────────────────────────────────────
    ## tidyverse 1.3.2 ──

    ## ✔ tibble  3.2.1     ✔ dplyr   1.1.2
    ## ✔ tidyr   1.3.0     ✔ stringr 1.5.0
    ## ✔ readr   2.1.3     ✔ forcats 0.5.2
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ tidyr::expand() masks Matrix::expand()
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ✖ caret::lift()   masks purrr::lift()
    ## ✖ tidyr::pack()   masks Matrix::pack()
    ## ✖ tidyr::unpack() masks Matrix::unpack()

    data(docvisits)

\#Create Interaction Covariates

    dt <- docvisits[,-(2:3)]
    tmp <- model.matrix(~age30*health+age35*health+age40*health+age45*health+age50*health
    +age55*health+age60*health, data=dt)[,-(1:9)]
    dat <- cbind(dt, tmp)

\#histigram of data

    barplot(with(docvisits,table(docvisits)),ylab="Frequency",xlab="Doctor office visits")

![](bootstrapped_docvisits_files/figure-markdown_strict/unnamed-chunk-3-1.png)

\#look at null Model

    m1 <- zeroinfl(docvisits~.|., data=dat)


    summary(m1)

    ## 
    ## Call:
    ## zeroinfl(formula = docvisits ~ . | ., data = dat)
    ## 
    ## Pearson residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -3.1508 -0.8285 -0.5223  0.4109 14.9460 
    ## 
    ## Count model coefficients (poisson with log link):
    ##                     Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)         2.362353   0.156329  15.111  < 2e-16 ***
    ## health             -0.148115   0.016075  -9.214  < 2e-16 ***
    ## handicap            0.271989   0.075436   3.606 0.000311 ***
    ## hdegree            -0.003177   0.001295  -2.453 0.014152 *  
    ## married            -0.159862   0.040424  -3.955 7.66e-05 ***
    ## schooling          -0.000437   0.007345  -0.059 0.952557    
    ## hhincome            0.005151   0.007809   0.660 0.509447    
    ## children            0.111799   0.038941   2.871 0.004092 ** 
    ## self               -0.201870   0.072920  -2.768 0.005634 ** 
    ## civil              -0.220877   0.079899  -2.764 0.005702 ** 
    ## bluec               0.044544   0.039861   1.117 0.263792    
    ## employed           -0.118998   0.047074  -2.528 0.011474 *  
    ## public              0.086019   0.069559   1.237 0.216221    
    ## addon               0.231982   0.095560   2.428 0.015199 *  
    ## age30TRUE          -0.060008   0.148876  -0.403 0.686897    
    ## age35TRUE          -0.003373   0.154278  -0.022 0.982556    
    ## age40TRUE          -0.223629   0.177631  -1.259 0.208048    
    ## age45TRUE           1.086201   0.157483   6.897 5.30e-12 ***
    ## age50TRUE          -0.094071   0.118664  -0.793 0.427923    
    ## age55TRUE          -0.556404   0.109640  -5.075 3.88e-07 ***
    ## age60TRUE           0.369637   0.106218   3.480 0.000501 ***
    ## `age30TRUE:health`  0.017434   0.022871   0.762 0.445896    
    ## `health:age35TRUE` -0.002119   0.024605  -0.086 0.931356    
    ## `health:age40TRUE`  0.029417   0.029230   1.006 0.314222    
    ## `health:age45TRUE` -0.171493   0.027869  -6.154 7.57e-10 ***
    ## `health:age50TRUE`  0.040503   0.023169   1.748 0.080441 .  
    ## `health:age55TRUE`  0.133895   0.020707   6.466 1.01e-10 ***
    ## `health:age60TRUE` -0.091054   0.020716  -4.395 1.11e-05 ***
    ## 
    ## Zero-inflation model coefficients (binomial with logit link):
    ##                      Estimate Std. Error z value Pr(>|z|)   
    ## (Intercept)        -1.8361939  0.6253364  -2.936  0.00332 **
    ## health              0.2112736  0.0652813   3.236  0.00121 **
    ## handicap           -0.2690908  0.4488452  -0.600  0.54883   
    ## hdegree            -0.0009545  0.0085910  -0.111  0.91153   
    ## married            -0.2460471  0.1555536  -1.582  0.11371   
    ## schooling           0.0103167  0.0248792   0.415  0.67838   
    ## hhincome           -0.0323795  0.0282908  -1.145  0.25241   
    ## children            0.3612277  0.1460856   2.473  0.01341 * 
    ## self                0.2036483  0.2170296   0.938  0.34807   
    ## civil               0.1698784  0.2328968   0.729  0.46575   
    ## bluec              -0.0261256  0.1422996  -0.184  0.85433   
    ## employed            0.0070865  0.1894403   0.037  0.97016   
    ## public             -0.0271307  0.2019384  -0.134  0.89312   
    ## addon               0.0581831  0.3866550   0.150  0.88039   
    ## age30TRUE          -1.1748285  0.7688712  -1.528  0.12651   
    ## age35TRUE           0.7881839  0.8125592   0.970  0.33205   
    ## age40TRUE          -0.9350359  0.8942795  -1.046  0.29576   
    ## age45TRUE           2.3416781  0.8513992   2.750  0.00595 **
    ## age50TRUE          -2.0717280  0.8899887  -2.328  0.01992 * 
    ## age55TRUE          -0.0499772  0.9406842  -0.053  0.95763   
    ## age60TRUE          -1.0298281  1.0903373  -0.945  0.34491   
    ## `age30TRUE:health`  0.1688793  0.0992245   1.702  0.08876 . 
    ## `health:age35TRUE` -0.1091959  0.1075320  -1.015  0.30988   
    ## `health:age40TRUE`  0.1558353  0.1223679   1.273  0.20284   
    ## `health:age45TRUE` -0.3381910  0.1201087  -2.816  0.00487 **
    ## `health:age50TRUE`  0.2122695  0.1233680   1.721  0.08532 . 
    ## `health:age55TRUE`  0.0703314  0.1296611   0.542  0.58753   
    ## `health:age60TRUE`  0.0876422  0.1530382   0.573  0.56686   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1 
    ## 
    ## Number of iterations in BFGS optimization: 58 
    ## Log-likelihood: -4376 on 56 Df

    cat("loglik of zero-inflated model", logLik(m1))

    ## loglik of zero-inflated model -4375.925

    cat("BIC of zero-inflated model", AIC(m1, k=log(dim(dat)[1])))

    ## BIC of zero-inflated model 9171.971

    AIC(m1)

    ## [1] 8863.849

\#Use backwackward stepwise variable elimination to fit model

    fitbe <- be.zeroinfl(m1, data=dat, alpha=0.01, trace=FALSE)
    summary(fitbe)

    ## 
    ## Call:
    ## zeroinfl(formula = eval(parse(text = out)), data = data, dist = dist)
    ## 
    ## Pearson residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -2.6653 -0.8109 -0.5439  0.4177 13.3877 
    ## 
    ## Count model coefficients (poisson with log link):
    ##                     Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)         2.284601   0.060405  37.821  < 2e-16 ***
    ## health             -0.131198   0.008867 -14.797  < 2e-16 ***
    ## handicap            0.128771   0.036501   3.528 0.000419 ***
    ## married            -0.138390   0.038326  -3.611 0.000305 ***
    ## children            0.111472   0.037448   2.977 0.002914 ** 
    ## self               -0.288669   0.066143  -4.364 1.28e-05 ***
    ## civil              -0.328204   0.056576  -5.801 6.59e-09 ***
    ## age45TRUE           0.879915   0.081328  10.819  < 2e-16 ***
    ## age55TRUE          -0.637184   0.087676  -7.267 3.66e-13 ***
    ## age60TRUE           0.374720   0.104232   3.595 0.000324 ***
    ## `health:age45TRUE` -0.128181   0.014310  -8.957  < 2e-16 ***
    ## `health:age55TRUE`  0.158220   0.016720   9.463  < 2e-16 ***
    ## `health:age60TRUE` -0.087265   0.020533  -4.250 2.14e-05 ***
    ## 
    ## Zero-inflation model coefficients (binomial with logit link):
    ##             Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept) -2.37162    0.21462 -11.051  < 2e-16 ***
    ## health       0.29846    0.02828  10.552  < 2e-16 ***
    ## age50TRUE   -0.60096    0.12364  -4.861 1.17e-06 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1 
    ## 
    ## Number of iterations in BFGS optimization: 20 
    ## Log-likelihood: -4406 on 16 Df

    cat("loglik of zero-inflated model with backward selection",logLik(fitbe))

    ## loglik of zero-inflated model with backward selection -4406.346

    cat("BIC of zero-inflated model with backward selection", AIC(fitbe,k=log(dim(dat)[1])))

    ## BIC of zero-inflated model with backward selection 8932.727

    AIC(fitbe)

    ## [1] 8844.692

    #AIC is lower than null model indicating a better fit


    #backwards_mod <- zeroinfl(docvisits ~ health + handicap + married + children + self + civil + age45 + age55 + age60 + health*age45 + health*age55 + health*age60| health + age50 , data = dat)

\#bootstrapped not stratified for backward stepwise variable selected
model with 250 iterations

    boot_ns_kfold <- function(i){
      dat_kfold <- caret::createFolds(dat[["docvisits"]], k = 10, list = TRUE)
      # Use the list of row-indices to create data.frames for each fold
      dat_kdf <- purrr::map_dfr(dat_kfold, ~dat[.x,], .id = "Fold")
      k <- 1:10
      cv_function <- function(i) {
      fold_num <- sprintf("Fold%02d", i)  # Create fold name with leading zero if necessary
       Training_min1wf <- dat_kdf %>% filter(Fold != fold_num)
      Training_min1 <- Training_min1wf[-(1:1)]
      Test_1wf <- dat_kdf %>% filter(Fold == fold_num)
      Test_1 <- Test_1wf[-(1:1)]
      fold_mod <- zeroinfl(docvisits ~ health + handicap + married + children + self + civil + age45 + age55 + age60 + health*age45 + health*age55 + health*age60| health + age50, data = Training_min1)
      pred <- predict(fold_mod, newdata = Test_1)
      squared_dif <- (pred - Test_1$docvisits)^2
      mse <- mean(squared_dif)
      #rmse <- sqrt(mse) 
      aic <- AIC(fold_mod)
      return(list(AIC = aic, MSE = mse))
    }

    # Run the function for each fold
    fold_mse <- as.data.frame(t(as.data.frame(sapply(k, cv_function))))

    fold_mse$AIC <- as.numeric(fold_mse$AIC)
    fold_mse$MSE <- as.numeric(fold_mse$MSE)


    null_avgAIC <- mean(fold_mse$AIC)
    null_avgMSE <- mean(fold_mse$MSE)

    RMSE <- sqrt(null_avgMSE)
    return(list(null_avgAIC, RMSE))
    }


    result_ns <- purrr::map_dfr(1:250, function(i) {
      res <- boot_ns_kfold(i)
      list(null_avgAIC = res[[1]], RMSE = res[[2]])
    })

    result_ns <- as.data.frame(result_ns)

    AIC_ns <- mean(result_ns$null_avgAIC)
    #7953.612
    RMSE_ns <- mean(result_ns$RMSE)
    #4.651579

    stnd_error_mean_ns <- sd(result_ns$null_avgAIC)/ sqrt(length(result_ns))
    #0.9260313

    stnd_error_rmse_ns <- sd(result_ns$RMSE)/ sqrt(length(result_ns))
    #0.01560308

\#stratified bootstrap with backwards variable selected model using 250
iterations

    boot_s_kfold <- function(i){
      #create logical variable on if docvisits is = 0
      dat_log <- dat %>% mutate(docvisits_cat = ifelse(docvisits ==0, TRUE, FALSE))
      #create fold indices
      dat_stratkfold <- createFolds(dat_log[["docvisits_cat"]], k = 10, list = TRUE)
      # Use the list of row-indices to create data.frames for each fold
      dat_stratkdf <- purrr::map_dfr(dat_stratkfold, ~dat_log[.x,], .id = "Fold") %>% select(-docvisits_cat)


      k <- 1:10
      cv_function_strat <- function(i) {
      fold_num <- sprintf("Fold%02d", i)  # Create fold name with leading zero if necessary
      
      Training_min1wf_strat <- dat_stratkdf %>% filter(Fold != fold_num)
      Training_min1_strat <- Training_min1wf_strat[-(1:1)]
      
      Test_1wf_strat <- dat_stratkdf %>% filter(Fold == fold_num)
      Test_1_strat <- Test_1wf_strat[-(1:1)]
      
      fold_mod_strat <- zeroinfl(docvisits ~ health + handicap + married + children + self + civil + age45 + age55 + age60 + health*age45 + health*age55 + health*age60| health + age50, data = Training_min1_strat)
      
      pred_strat <- predict(fold_mod_strat, newdata = Test_1_strat)
      
      squared_dif_strat <- (pred_strat - Test_1_strat$docvisits)^2
      
      mse_strat <- mean(squared_dif_strat)
      
      #rmse_strat <- sqrt(mse_strat)
      AIC_strat <- AIC(fold_mod_strat)
      
      return(list(AIC_s = AIC_strat, MSE_s = mse_strat))
      
    }

    fold_mse_strat <- as.data.frame(t(as.data.frame(sapply(k, cv_function_strat))))

    fold_mse_strat$AIC_s <- as.numeric(fold_mse_strat$AIC_s)
    fold_mse_strat$MSE_s <- as.numeric(fold_mse_strat$MSE_s)

    null_avg_strat_AIC <- mean(fold_mse_strat$AIC_s)
    null_avg_strat_MSE <- mean(fold_mse_strat$MSE_s)

    RMSE_strat <- sqrt(null_avg_strat_MSE)

    return(list(null_avg_strat_AIC, RMSE_strat))

    }


    result_s <- map_dfr(1:250, function(i) {
      res <- boot_s_kfold(i)
      list(null_avg_strat_AIC = res[[1]], RMSE_strat = res[[2]])
    })

    result_s <- as.data.frame(result_s)

    AIC_s <- mean(result_s$null_avg_strat_AIC)
    #7953.671
    RMSE_s <- mean(result_s$RMSE_strat)
    #4.650585


    stnd_error_mean_s <- sd(result_s$null_avg_strat_AIC)/ sqrt(length(result_s))
    #1.019674

    stnd_error_rmse_s <- sd(result_s$RMSE_strat)/ sqrt(length(result_s))
    #0.0160189

\#compare

    AIC_ns

    ## [1] 7953.67

    stnd_error_mean_ns

    ## [1] 0.975551

    AIC_s

    ## [1] 7953.509

    stnd_error_mean_s

    ## [1] 0.9352027

    RMSE_ns

    ## [1] 4.650401

    RMSE_s

    ## [1] 4.651822

    #99% confidence intervals for AIC:

    AIC_ns + c(-1,1) * 2.576 * stnd_error_mean_ns

    ## [1] 7951.157 7956.183

    AIC_s + c(-1,1) * 2.576 * stnd_error_mean_s

    ## [1] 7951.099 7955.918

    #significant confidence interval overlap#
