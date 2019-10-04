# Markowitz-Portfolio-Diversification-with-SURE-CAPM-Model
 A model to show the effects of diversification on a Dow Jones 30 stock portfolio with a SURE CAPM model without the intercept and with Gaussian errors

## Methodology
## Data Dump
library(rlist)
thedowurl=getURL("https://finance.yahoo.com/quote/%5EDJI/components?p=%5EDJI")
djicomp =readHTMLTable(thedowurl)
dowdf=djicomp[[1]]
dowsymbolist=dowdf[,1]
downames=dowdf[,2]
dowsymbolist

##  [1] JNJ  CVX  KO   VZ   TRV  XOM  PG   DIS  MCD  AXP  UTX  WMT  HD   JPM 
## [15] MRK  WBA  MMM  PFE  GS   BA   UNH  V    NKE  AAPL IBM  CAT  DWDP MSFT
## [29] CSCO INTC
## 30 Levels: AAPL AXP BA CAT CSCO CVX DIS DWDP GS HD IBM INTC JNJ JPM ... XOM

    Download 4 years of weekly price data for each stock from June 1, 2014 to June 1, 2018. Remember this requires that all 30 stocks you select in step 1 should be available for these 4 years.

datadf=getfinwdat(symbols = c(as.character(dowsymbolist), "^gspc"), symnames = c(as.character(dowsymbolist), "sp500"), from = "2014-06-01", to = "2018-06-01")

## 'getSymbols' currently uses auto.assign=TRUE by default, but will
## use auto.assign=FALSE in 0.5-0. You will still be able to use
## 'loadSymbols' to automatically load data. getOption("getSymbols.env")
## and getOption("getSymbols.auto.assign") will still be checked for
## alternate defaults.
## 
## This message is shown once per session and may be disabled by setting 
## options("getSymbols.warning4.0"=FALSE). See ?getSymbols for details.

## 
## WARNING: There have been significant changes to Yahoo Finance data.
## Please see the Warning section of '?getSymbols.yahoo' for details.
## 
## This message is shown once per session and may be disabled by setting
## options("getSymbols.yahoo.warning"=FALSE).

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning in to.period(datxts, indexAt = "endof", period = "weeks"): missing
## values removed from data

rskfree = getrskfree(from = "2014-06-01", to = "2018-06-01")

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning: missing values removed from data

head(datadf)

##                  prmJNJ       prmCVX         prmKO        prmVZ
## 2014-06-13 -0.006305118  0.024714620 -0.0077241136 -0.004861548
## 2014-06-20  0.026718216  0.039912652  0.0326917024  0.004263874
## 2014-06-27 -0.002661941 -0.014963385  0.0119914948 -0.001418929
## 2014-07-03  0.004092270  0.006363487  0.0009444203  0.007904050
## 2014-07-11 -0.003035973 -0.020734059 -0.0061573586  0.023170612
## 2014-07-18 -0.031401556  0.014942444  0.0109572843  0.008542513
##                  prmTRV       prmXOM         prmPG        prmDIS
## 2014-06-13  0.011122231  0.010329429 -0.0048785188 -0.0213976139
## 2014-06-20 -0.008600243  0.011489486  0.0036358153  0.0002356821
## 2014-06-27 -0.007085344 -0.025235496 -0.0113870430  0.0299424595
## 2014-07-03  0.009792346  0.013631411  0.0121452936  0.0180504671
## 2014-07-11 -0.001687702 -0.008285995  0.0147530862  0.0005751924
## 2014-07-18  0.006967878  0.009727957  0.0004047467 -0.0124324841
##                  prmMCD        prmAXP       prmUTX       prmWMT
## 2014-06-13 -0.014422607 -0.0006375125 -0.017751327 -0.025001975
## 2014-06-20  0.014224435  0.0072690939  0.010268902  0.005307501
## 2014-06-27 -0.004515539 -0.0063867941 -0.011952022 -0.004494510
## 2014-07-03 -0.004734036  0.0095824665 -0.009010088  0.005438559
## 2014-07-11 -0.006041150 -0.0115778544 -0.012118708  0.014124990
## 2014-07-18 -0.013752412 -0.0099531205 -0.004909545  0.003511759
##                   prmHD        prmJPM       prmMRK       prmWBA
## 2014-06-13 -0.031875242  0.0012232486  0.014325182 -0.026261648
## 2014-06-20  0.026892825  0.0089353029  0.011669961  0.020251526
## 2014-06-27  0.011972856 -0.0003492974 -0.023593300 -0.005904733
## 2014-07-03  0.011336402 -0.0014149115  0.029025010 -0.001622828
## 2014-07-11 -0.029738458 -0.0219110041 -0.012838496 -0.031360470
## 2014-07-18  0.005900723  0.0435453326 -0.001713948  0.004323275
##                  prmMMM       prmPFE        prmGS         prmBA
## 2014-06-13 -0.008854823  0.003733518 -0.001810441 -0.0431157211
## 2014-06-20  0.012550062  0.008460092  0.023805035 -0.0014418314
## 2014-06-27 -0.010128796 -0.003695708 -0.018018694 -0.0269513257
## 2014-07-03  0.011966317  0.028982201  0.016065669 -0.0002368683
## 2014-07-11 -0.007565024 -0.015067849 -0.027499887 -0.0032687863
## 2014-07-18  0.003600493  0.021945964  0.040470503 -0.0035158954
##                  prmUNH         prmV       prmNKE      prmAAPL
## 2014-06-13 -0.004733295 -0.008033532 -0.021125687 -0.010244584
## 2014-06-20  0.028158118 -0.008524920  0.006426822 -0.004059326
## 2014-06-27  0.007245444 -0.001004327  0.034352452  0.011768115
## 2014-07-03  0.016459540  0.034543650  0.009908429  0.022283756
## 2014-07-11 -0.005758970  0.002262413 -0.014786530  0.012655007
## 2014-07-18  0.031974717  0.014743848  0.002972878 -0.008299348
##                   prmIBM       prmCAT      prmDWDP      prmMSFT
## 2014-06-13 -0.0204488199 -0.013039166 -0.013745292 -0.006032390
## 2014-06-20 -0.0055377838  0.024439210  0.001330000  0.010908680
## 2014-06-27  0.0008792806 -0.005487209 -0.009425667  0.013673542
## 2014-07-03  0.0375286388  0.021140020  0.008522253 -0.010654126
## 2014-07-11 -0.0028115879 -0.010083157 -0.008646349  0.006937116
## 2014-07-18  0.0239331104  0.008242223  0.001741510  0.061769329
##                 prmCSCO     prmINTC      prmsp500     hml     smb
## 2014-06-13 -0.005240978 0.060342547 -0.0068175501 -0.0065  0.0073
## 2014-06-20  0.005257379 0.011042149  0.0137895576 -0.0031  0.0078
## 2014-06-27 -0.005237516 0.024170124 -0.0009750054 -0.0023  0.0018
## 2014-07-03  0.027619648 0.006786064  0.0124802099 -0.0032  0.0050
## 2014-07-11  0.013099897 0.003531884 -0.0090010985  0.0060 -0.0305
## 2014-07-18  0.015278984 0.078397165  0.0054098962  0.0088 -0.0145

    Select a desired portfolio mean return level.

desr=(.1)/52


1. We form 6 portfolios. Each portfolio has 5, 10, 15, 20, 25 and 30 stocks. The smaller set of assets should be a subset of the larger set. For example, if you have IBM in the set of 5 stocks, you must include IBM in the portfolios of 10, 15, 20, 25 and 30 stocks.

thirtyport= as.character(dowsymbolist)
fiveport=as.character(sample(dowsymbolist,5))
newpop=thirtyport[!(thirtyport%in%fiveport)]
tenport=c(fiveport,as.character(sample(newpop,5)))
newpop1=thirtyport[!(thirtyport%in%tenport)]
fifteenport=c(tenport,as.character(sample(newpop1,5)))
newpop2=thirtyport[!(thirtyport%in%fifteenport)]
twentyport=c(fifteenport,as.character(sample(newpop2,5)))
newpop3=thirtyport[!(thirtyport%in%twentyport)]
twentyfiveport=c(twentyport,as.character(sample(newpop3,5)))

fiveport

## [1] "KO"   "INTC" "MCD"  "MRK"  "DIS"

tenport

##  [1] "KO"   "INTC" "MCD"  "MRK"  "DIS"  "XOM"  "AXP"  "PG"   "UNH"  "MSFT"

fifteenport

##  [1] "KO"   "INTC" "MCD"  "MRK"  "DIS"  "XOM"  "AXP"  "PG"   "UNH"  "MSFT"
## [11] "JPM"  "IBM"  "HD"   "V"    "GS"

twentyport

##  [1] "KO"   "INTC" "MCD"  "MRK"  "DIS"  "XOM"  "AXP"  "PG"   "UNH"  "MSFT"
## [11] "JPM"  "IBM"  "HD"   "V"    "GS"   "WBA"  "VZ"   "CVX"  "UTX"  "DWDP"

twentyfiveport

##  [1] "KO"   "INTC" "MCD"  "MRK"  "DIS"  "XOM"  "AXP"  "PG"   "UNH"  "MSFT"
## [11] "JPM"  "IBM"  "HD"   "V"    "GS"   "WBA"  "VZ"   "CVX"  "UTX"  "DWDP"
## [21] "CSCO" "CAT"  "WMT"  "NKE"  "MMM"

thirtyport

##  [1] "JNJ"  "CVX"  "KO"   "VZ"   "TRV"  "XOM"  "PG"   "DIS"  "MCD"  "AXP" 
## [11] "UTX"  "WMT"  "HD"   "JPM"  "MRK"  "WBA"  "MMM"  "PFE"  "GS"   "BA"  
## [21] "UNH"  "V"    "NKE"  "AAPL" "IBM"  "CAT"  "DWDP" "MSFT" "CSCO" "INTC"

2. We assume that the assume stock premium is explained by the SURE CAPM model without the intercept and with Gaussian errors.

portlist=list(fiveport, tenport, fifteenport,twentyport, twentyfiveport, thirtyport)

capmfmls=vector()
g=length(portlist)
for (i in 1:g)
{
  fmls=vector()
  fmls=lapply(portlist[i], function(i) paste0("prm",i,"~prmsp500-1"))
  assign(paste0("capmls",i*5),fmls[[1]])
}


3. Now for each portfolio, use the default training sample prior. 

capmlslist=list(capmls5, capmls10, capmls15, capmls20, capmls25, capmls30)
c=length(capmlslist)
for (f in 1:c)
{
  priorls=vector()
  plist=lapply(capmlslist[[f]], function(f) as.formula(f))
  priorls=trainpriorsureg(modelfrmls = plist,data = datadf, trainpct = .25)
  assign(paste0("priorlsport",f*5),priorls)
} 
summary(priorlsport15)

##         Length Class  Mode   
## beta0_     15  -none- numeric
## B0_       225  -none- numeric
## rho         1  -none- numeric
## Sigma0_   225  -none- numeric
## datls       5  -none- list   
## ytr       780  -none- numeric
## Xtr     11700  -none- numeric
## y        2340  -none- numeric
## X       35100  -none- numeric
## nt          1  -none- numeric
## n           1  -none- numeric
## nn          1  -none- numeric
## k           1  -none- numeric
## d           1  -none- numeric

print("beta0_ is a 15 by 1 matrix summarizing the mean return for the fifteen stocks in the portfolio in the prior")

## [1] "beta0_ is a 15 by 1 matrix summarizing the mean return for the fifteen stocks in the portfolio in the prior"

priorlsport15$beta0_

##            [,1]
##  [1,] 0.6569820
##  [2,] 1.0590104
##  [3,] 0.5955533
##  [4,] 0.8404766
##  [5,] 0.9975466
##  [6,] 1.1354815
##  [7,] 0.9947240
##  [8,] 0.6798170
##  [9,] 0.9424174
## [10,] 1.5687835
## [11,] 1.1515729
## [12,] 0.5329241
## [13,] 0.9563941
## [14,] 1.1760914
## [15,] 1.1209666

print("B0_ is a 15 by 15 matrix summarizing variance/covariance between the fifteen stocks in the portfolio in the prior")

## [1] "B0_ is a 15 by 15 matrix summarizing variance/covariance between the fifteen stocks in the portfolio in the prior"

head(priorlsport15$B0_)

##              [,1]         [,2]          [,3]          [,4]         [,5]
## [1,]  0.126924302  0.016912154  0.0299765302  0.0289545310 -0.023624681
## [2,]  0.016912154  0.250638844 -0.0416116179  0.0519678553 -0.002195162
## [3,]  0.029976530 -0.041611618  0.0829267708 -0.0002832149 -0.011985649
## [4,]  0.028954531  0.051967855 -0.0002832149  0.1731629842 -0.009021726
## [5,] -0.023624681 -0.002195162 -0.0119856487 -0.0090217260  0.108271066
## [6,] -0.001683648 -0.002520472 -0.0038503276  0.0157830878 -0.018464129
##              [,6]         [,7]         [,8]         [,9]       [,10]
## [1,] -0.001683648  0.002665133  0.023062815  0.013491305  0.02086267
## [2,] -0.002520472 -0.044291636  0.010432975  0.021218883  0.06086306
## [3,] -0.003850328  0.006634232 -0.010348902 -0.024764438 -0.01536926
## [4,]  0.015783088 -0.004323446  0.018650990  0.004928771  0.01906743
## [5,] -0.018464129  0.018707652  0.009653035 -0.006634239  0.02710335
## [6,]  0.104586509 -0.012452064 -0.002217336 -0.027421710 -0.01679897
##             [,11]        [,12]        [,13]        [,14]        [,15]
## [1,] -0.009353231  0.057264814 -0.002898436 -0.009271038 -0.018557809
## [2,] -0.003563520 -0.006675437 -0.042843879 -0.015529290 -0.005174267
## [3,] -0.006006315  0.022277580  0.004746505  0.016090060 -0.015950941
## [4,] -0.023668905 -0.020511206 -0.002025527 -0.031005177 -0.033126899
## [5,]  0.010723990 -0.003027393  0.026294773  0.008438018  0.011015763
## [6,]  0.022464535  0.002408262 -0.034954929 -0.023405937  0.008164753

print("rho is a parameter controlling the tightness of the prior")

## [1] "rho is a parameter controlling the tightness of the prior"

priorlsport15$rho

## [1] 20

print("Sigma0_ is a 15 by 15 matrix summarizing the means of the error term Sigma0 between the fifteen stocks")

## [1] "Sigma0_ is a 15 by 15 matrix summarizing the means of the error term Sigma0 between the fifteen stocks"

head(priorlsport15$Sigma0_)

##               [,1]          [,2]          [,3]          [,4]          [,5]
## [1,]  4.347570e-04  5.792962e-05  1.026794e-04  9.917867e-05 -8.092221e-05
## [2,]  5.792962e-05  8.585195e-04 -1.425333e-04  1.780068e-04 -7.519144e-06
## [3,]  1.026794e-04 -1.425333e-04  2.840511e-04 -9.701030e-07 -4.105474e-05
## [4,]  9.917867e-05  1.780068e-04 -9.701030e-07  5.931395e-04 -3.090234e-05
## [5,] -8.092221e-05 -7.519144e-06 -4.105474e-05 -3.090234e-05  3.708636e-04
## [6,] -5.767040e-06 -8.633437e-06 -1.318862e-05  5.406220e-05 -6.324564e-05
##               [,6]          [,7]          [,8]          [,9]         [,10]
## [1,] -5.767040e-06  9.128948e-06  7.899763e-05  4.621210e-05  7.146141e-05
## [2,] -8.633437e-06 -1.517132e-04  3.573633e-05  7.268157e-05  2.084757e-04
## [3,] -1.318862e-05  2.272440e-05 -3.544835e-05 -8.482624e-05 -5.264470e-05
## [4,]  5.406220e-05 -1.480921e-05  6.388570e-05  1.688264e-05  6.531213e-05
## [5,] -6.324564e-05  6.407979e-05  3.306478e-05 -2.272442e-05  9.283778e-05
## [6,]  3.582428e-04 -4.265236e-05 -7.595095e-06 -9.392827e-05 -5.754193e-05
##              [,11]         [,12]         [,13]         [,14]         [,15]
## [1,] -3.203785e-05  1.961506e-04 -9.928085e-06 -3.175632e-05 -6.356652e-05
## [2,] -1.220621e-05 -2.286554e-05 -1.467542e-04 -5.319286e-05 -1.772355e-05
## [3,] -2.057358e-05  7.630795e-05  1.625832e-05  5.511368e-05 -5.463715e-05
## [4,] -8.107369e-05 -7.025754e-05 -6.938088e-06 -1.062028e-04 -1.134704e-04
## [5,]  3.673315e-05 -1.036980e-05  9.006814e-05  2.890295e-05  3.773257e-05
## [6,]  7.694833e-05  8.249078e-06 -1.197320e-04 -8.017294e-05  2.796693e-05


4. We compute the optimal portfolios for each of the groups of assets at the target portfolio return level. We present the weights of each asset in each portfolio as well as the standard deviation of the optimal portfolios. 

capmlslist=list(capmls5, capmls10, capmls15, capmls20, capmls25, capmls30)
priorlslist=list(priorlsport5,priorlsport10, priorlsport15,priorlsport20, priorlsport25, priorlsport30)
c=length(capmlslist)
for (g in 1:c)
{
  capmportls=vector()
  plist=lapply(capmlslist[[g]], function(g) as.formula(g, env=globalenv()))
  capmportls = makebayesportfolioaftersureg(portmean = desr,modelfrmls = plist,
  data = datadf, trainprior = FALSE, beta0_ = priorlslist[[g]]$beta0_, 
  B0_ = priorlslist[[g]]$B0_, rho=priorlslist[[g]]$rho, 
  Sigma0_ = priorlslist[[g]]$Sigma0_)
  assign(paste0("capmportls",g*5),capmportls)
  mat=as.matrix(capmportls$weights)
  cat("For the",g*5,"asset portfolio, the weights for each asset are\n")
  print(mat)
  cat("and the portfolio standard deviation is",unlist(capmportls$portsd)," \n")
}

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning in to.period(datxts, indexAt = "endof", period = "weeks"): missing
## values removed from data

## For the 5 asset portfolio, the weights for each asset are
##          wt_KO   wt_INTC    wt_MCD    wt_MRK    wt_DIS wt_rskfree
## [1,] 0.0600982 0.2078616 0.1465729 0.3051099 0.3824863 -0.1021289
## and the portfolio standard deviation is 0.02060368

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning: missing values removed from data

## For the 10 asset portfolio, the weights for each asset are
##          wt_KO    wt_INTC    wt_MCD     wt_MRK    wt_DIS      wt_XOM
## [1,] 0.1660393 0.09159379 0.1910262 0.05244216 0.1627644 -0.01179494
##         wt_AXP      wt_PG     wt_UNH   wt_MSFT wt_rskfree
## [1,] 0.1603275 -0.1274913 0.04997416 0.1586388    0.10648
## and the portfolio standard deviation is 0.01672778

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning: missing values removed from data

## For the 15 asset portfolio, the weights for each asset are
##           wt_KO    wt_INTC     wt_MCD    wt_MRK   wt_DIS   wt_XOM
## [1,] -0.0608445 0.07052297 0.03998475 0.1461258 0.101335 0.148384
##         wt_AXP     wt_PG     wt_UNH    wt_MSFT     wt_JPM     wt_IBM
## [1,] 0.1197173 0.1737823 0.06955432 0.05015804 -0.1679883 0.09288426
##            wt_HD      wt_V      wt_GS   wt_rskfree
## [1,] -0.06015011 0.2465285 0.03268584 -0.002680246
## and the portfolio standard deviation is 0.01677919

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning: missing values removed from data

## For the 20 asset portfolio, the weights for each asset are
##            wt_KO    wt_INTC     wt_MCD     wt_MRK     wt_DIS     wt_XOM
## [1,] -0.09534931 0.08551362 0.06884929 0.08879913 0.09131957 0.05167027
##         wt_AXP      wt_PG    wt_UNH   wt_MSFT     wt_JPM      wt_IBM
## [1,] 0.2287947 -0.1392857 0.0820037 0.1364578 -0.1665794 -0.04733183
##           wt_HD       wt_V     wt_GS      wt_WBA       wt_VZ     wt_CVX
## [1,] 0.02019888 0.03510156 0.1476477 0.003985288 -0.04286154 0.08489827
##         wt_UTX     wt_DWDP wt_rskfree
## [1,] 0.1094478 -0.06814001  0.3248603
## and the portfolio standard deviation is 0.01586191

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning: missing values removed from data

## For the 25 asset portfolio, the weights for each asset are
##          wt_KO     wt_INTC    wt_MCD     wt_MRK      wt_DIS      wt_XOM
## [1,] 0.1154978 -0.05842186 0.0456123 0.05646048 0.008744754 0.008229027
##          wt_AXP     wt_PG     wt_UNH    wt_MSFT      wt_JPM     wt_IBM
## [1,] -0.1311357 -0.253367 0.09788161 -0.1313895 -0.05094172 -0.1093394
##          wt_HD      wt_V     wt_GS        wt_WBA     wt_VZ    wt_CVX
## [1,] 0.1588633 0.1671412 0.2114125 -8.067913e-06 0.1280796 0.0141031
##           wt_UTX   wt_DWDP    wt_CSCO     wt_CAT      wt_WMT    wt_NKE
## [1,] -0.07584612 0.1121592 0.07349713 0.09725555 -0.02775957 0.0289587
##         wt_MMM wt_rskfree
## [1,] 0.1447341  0.3695786
## and the portfolio standard deviation is 0.01519817

## Warning: ^IRX contains missing values. Some functions will not work if
## objects contain missing values in the middle of the series. Consider using
## na.omit(), na.approx(), na.fill(), etc to remove or replace them.

## Warning: missing values removed from data

## For the 30 asset portfolio, the weights for each asset are
##           wt_JNJ    wt_CVX       wt_KO      wt_VZ      wt_TRV      wt_XOM
## [1,] -0.09597678 0.1377411 -0.03642199 -0.1004964 0.005795886 -0.08053281
##          wt_PG     wt_DIS      wt_MCD      wt_AXP     wt_UTX       wt_WMT
## [1,] 0.2826032 0.02455089 0.001849774 -0.01905766 -0.0888267 -0.007097217
##            wt_HD     wt_JPM      wt_MRK      wt_WBA    wt_MMM    wt_PFE
## [1,] -0.09213032 0.05621228 -0.03161994 -0.04123196 0.2740366 0.1197219
##          wt_GS      wt_BA wt_UNH      wt_V     wt_NKE     wt_AAPL
## [1,] 0.0247456 0.04952468  0.153 0.2316091 0.01020068 -0.04934308
##          wt_IBM      wt_CAT    wt_DWDP    wt_MSFT     wt_CSCO  wt_INTC
## [1,] 0.04857981 -0.08374613 0.02191274 0.07864653 -0.06231776 0.014365
##      wt_rskfree
## [1,]   0.253703
## and the portfolio standard deviation is 0.01497631


5. Use ggplot to plot the standard deviation vs. the number of stocks in the assets. 
<img width="800" src="https://github.com/Twabeeric/Markowitz-Portfolio-Diversification-with-SURE-CAPM-Model/blob/master/DS.png">
