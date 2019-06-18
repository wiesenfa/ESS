Quantification of prior informativeness in terms of prior effective and effective current sample size
================

-   [Installation](#installation)
-   [Visualization of the impact of priors in terms of effective sample size](#visualization-of-the-impact-of-priors-in-terms-of-effective-sample-size)
-   [References](#references)

<!-- README.md is generated from README.Rmd. Please edit that file -->
Computes prior effective and effective current sample size. The concept of prior effective sample sizes (prior ESS) is convenient to quantify and communicate informativeness of prior distributions as it equates the information provided by a prior to a sample size. Prior information can arise from historical observations, thus the traditional approach identifies the ESS with such historical sample size (prior ESS). However, this measure is independent from newly observed data, and thus would not capture an actual \`\`loss of information'' induced by the prior in case of prior-data conflict. The effective current sample size (ECSS) of a prior relates prior information to a number of (virtual) samples from the current data model and describes the impact of the prior capturing prior-data conflict. Supports mixture and empirical Bayes power and commensurate priors. See Wiesenfarth and Calderazzo (2019).

Installation
------------

<!-- To get the current released version from CRAN: -->
<!-- ```{r} -->
<!-- ## install ESS from CRAN -->
<!-- install.packages("ESS") -->
<!-- ## load ESS package -->
<!-- library(ESS) -->
<!-- ``` -->
To get the current development version from Github:

``` r
devtools::install_github("wiesenfa/ESS")
```

``` r
## load ESS package
library(ESS)
#> Loading required package: RBesT
#> Loading required package: Rcpp
#> This is RBesT version 1.3.7
#> 
#> priorESS 0.4.4.0 loaded.
#> 
#> Attaching package: 'ESS'
#> The following object is masked from 'package:RBesT':
#> 
#>     ess
```

Visualization of the impact of priors in terms of effective sample size
=======================================================================

This section (same as `vignette("vignettePlanning", package = "ESS")`) visualizes how `plotECSS()` can be used to quantify the impact of a prior (including mixture priors and empirical Bayes power priors/commensurate priors) in terms of effective current sample sizes on a grid of true values of the data generating process.

The effective current sample size (ECSS) is defined as follows:

Let *π* be the prior of interest (specified by `priorlist`) with mean *θ*<sub>*π*</sub>, *π*<sub>*b*</sub> a baseline prior (an objective or reference prior) (specified by `prior.base`) and *f*<sub>*n*</sub>(*y*<sub>1 : *n*</sub>|*θ*<sub>0</sub>) be the data distribution.

The ECSS at target sample size *k* (specified by `n.target`) is defined as the sample size *m* which minimizes
*E**C**S**S* = *a**r**g**m**i**n*<sub>*m*</sub>|*D*<sub>*M**S**E*</sub><sup>*θ*<sub>0</sub></sup>(*π*(*θ*|*y*<sub>1 : (*k* − *m*)</sub>)) − *D*<sub>*M**S**E*</sub><sup>*θ*<sub>0</sub></sup>(*π*<sub>*b*</sub>(*θ*|*y*<sub>1 : *k*</sub>))|

where *D*<sub>*M**S**E*</sub><sup>*θ*<sub>0</sub></sup> is the mean squared error measure induced by the posterior mean estimate E<sub>*π*</sub>(*θ*|*y*<sub>1 : *k*</sub>),
*D*<sub>*M**S**E*</sub><sup>*θ*<sub>0</sub></sup>(*π*(*θ*|*y*<sub>1 : *k*</sub>)) = *E*<sub>*y*|*θ*<sub>0</sub></sub>(*E*<sub>*π*</sub>(*θ*|*y*<sub>1 : *k*</sub>)−*θ*<sub>0</sub>)<sup>2</sup>
 or a different target measure specified by `D`. The true parameter value *θ*<sub>0</sub> (specified by `grid`) is assumed to be known.

Normal Outcome
--------------

``` r
  # data SD
    sigma=1
  # baseline
    rob=c(0,10)
    vague <-mixnorm(vague=c(1, rob), sigma=sigma)
  # prior with nominal prior ESS=50
    inf=c(0,1/sqrt(50))
    info <-mixnorm(informative=c(1, inf), sigma=sigma)
  # robust mixture
    mix50 <-mixnorm(informative=c(.5, inf),vague=c(.5, rob), sigma=sigma)
  # emprirical Bayes power prior / emprirical Bayes commensurate prior
    pp <-as.powerprior(info)

    plotECSS(priorlist=list(informative=info,mixture=mix50,powerprior=pp), 
             grid=((0:100)/100), n.target=200, min.ecss=-150, sigma=sigma, 
             prior.base=vague, progress="none", D=MSE)
```

![](README_files/figure-markdown_github/unnamed-chunk-4-1.png)

Binary outcome
--------------

``` r
# uniform baseline prior
  rob=c(1,1)
  vague <- mixbeta(rob=c(1.0, rob))
# prior with nominal EHSS=20
  inf=c(4, 16)
  info=mixbeta(inf=c(1, inf))
  prior.mean=inf[1]/sum(inf)
 
# robust mixture
  mix50 <-mixbeta(informative=c(.5, inf), vague=c(.5, rob))
# emprirical Bayes power prior 
  pp <-as.powerprior(info)
## emprirical Bayes commensurate prior 
#  cp <-as.commensurateEB(info)

 plotECSS(priorlist=list(informative=info,mixture=mix50,powerprior=pp), 
          grid=((0:40)/40), n.target=40, min.ecss=-50,
          prior.base=vague, progress="none", D=MSE)
```

![](README_files/figure-markdown_github/unnamed-chunk-5-1.png)

<!-- # Adjusting the control sample size in an adaptive trial design: Two-arm design with interim analysis for sample size recalculation in control arm -->
<!-- This section (same as `vignette("vignetteDesign", package = "ESS")`) reproduces the adaptive trial design presented in Wiesenfarth and Calderazzo (2019) similar to the design presented in Schmidli et. al (2014). -->
<!-- Specifically, a two arm Bayesian clinical trial design is considered, where it is assumed that prior information on the control arm exists. In this situation it is desirable to make use of this prior information to reduce the final sample size in the control group, while keeping operating characteristics close to an analysis with full sample size and without prior information. At the same time, robustness with respect to deviations from prior beliefs is aimed for. -->
<!-- With this in mind, the following adaptive randomization scheme has been proposed for designs which aims at adapting the final sample size in the control arm according to -->
<!-- the information contained in the prior distribution, as measured -->
<!-- at an interim analysis.  -->
<!-- Variants of this design have been studied by several authors. All of them consider some version of the EHSS as a measure of prior informativeness with priors which adapt to prior-data conflict. -->
<!-- In this context, we argue that the ECSS is intuitively more appropriate than the EHSS as it answers the question of how many control samples are offset by the prior after stage two, i.e. how many control patients are added or subtracted from the analysis by inclusion of prior information. In contrast, the prior ESS is independent of the currently observed control samples, while the data-dependent EHSS depends on the currently observed control samples according to a measure which may be problematic in situations of moderate conflict and is not among the trial's targets. -->
<!-- We simulate a two arm trial assuming normal outcomes  $y_{control}\sim N(\mu_0,1)$ and $y_{treat}\sim N(\mu_0+\tau,1)$ for varying control means $\mu_0$ and effect sizes $\tau\in\{0,0.28\}$. -->
<!-- The following priors are considered: An informative prior $N(0,1/50)$, a baseline prior $N(0,10^2)$, a mixture of the two with equal component weights $0.5 N(0,1/50)+0.5 N(0,10^2)$  and power prior based on the informative prior $N(0,1/(\delta\cdot 50))$ where $\delta$ is estimated by empirical Bayes. Note that results for empirical Bayes power prior are equivalent to those under an empirical Bayes commensurate prior (see Wiesenfarth and Calderazzo, 2019). Variances are assumed to be known. -->
<!-- We conduct an interim analysis after 100 samples in each arm have been collected and compute the ESS in the control arm according to the different approaches. Then, additional 100 patients are recruited in the treatment arm  -->
<!-- while in the control arm we subtract from the target stage-two sample size of 100 a number of patients equal to the prior ESS measures. -->
<!-- At the end of the trial, we evaluate the MSE of $\tau$  -->
<!-- and frequentist type I error and power -->
<!-- for hypotheses $H_0: \tau\leq 0\mbox{ versus }H_1: \tau> 0$, as based on the posterior probability with decision threshold $c$, $P(\tau>0 | y_{treat},y_{control})> c$. We consider a common fixed decision threshold $c=0.975$. Thus, using the baseline prior, frequentist type I error is controlled at $2.5\%$ and power for $\tau=0.28$ is $80\%$. -->
<!-- A Monte Carlo simulation approach is used to allow visualization of variability in supplementary figures of the paper. Numerical integration may improve speed. -->
<!-- Reproduction of results for binomial outcomes in Schmidli et. al (2014) can be achieved by replacing `adaptiveDesign_normal()` by `adaptiveDesign_binomial()` and appropriate adjustment of design settings. -->
<!-- # Settings -->
<!-- ```{r, echo=FALSE} -->
<!-- library(foreach) -->
<!-- library(ESS) -->
<!-- library(ggplot2) -->
<!-- library(RBesT) -->
<!-- ``` -->
<!-- ```{r settings, echo=T} -->
<!-- # Specify list of priors -->
<!--   prior <- list() -->
<!--   prior$inf  <- mixnorm(inf=c(1.0, 0,1/sqrt(50)))  -->
<!--   prior$mix50 <-mixnorm(inf=c(0.5, 0,1/sqrt(50)), rob=c(0.5, 0,10)) -->
<!--   # if unit information prior as baseline prior should also be evaluated -->
<!--   #prior$mix50unitInfo <-mixnorm(inf=c(0.5, 0,1/sqrt(50)), rob=c(0.5, 0,1)) -->
<!--   prior$vague  <- mixnorm(rob= c(1.0, 0,10)) -->
<!-- # Specify vector of true control group means \mu_0 -->
<!--   muc.vec=(-33:33)/30 -->
<!-- # Vector of effect sizes -->
<!--   tau.vec=c(0,.28) -->
<!-- # number of patients enrolled to control and treatment in stage 1 -->
<!--   N1 = M1=100 -->
<!-- # minimum of patients enrolled in control in stage 2 -->
<!--   Nmin <- 0#10 -->
<!-- # target number of patients in control group -->
<!--   Ntarget = 200 -->
<!-- # target number of patients in treatment group (fixed) -->
<!--   M =200 -->
<!-- # Specify whether standard deviations in control (sc) and treatment group (st)  -->
<!-- #  are assumed to be known and their magnitudes -->
<!--   sc.known=st.known=TRUE -->
<!--   st=1 -->
<!--   sc=1 -->
<!-- # decision function: P(x1 - x2 > 0) > 0.975 -->
<!--   dec <- decision2S(0.975, 0, lower.tail=FALSE) -->
<!-- # number of parallel CPUs -->
<!--   cores=50 -->
<!-- # number of Monte Carlo iterations (10,000 in paper) -->
<!--   nsim=2000 -->
<!-- design=expand.grid(muc=muc.vec, delta=tau.vec) -->
<!-- cases.fix <- grep("mix", names(prior), value=TRUE, invert=TRUE)[-2] -->
<!-- cases.mix <- grep("mix", names(prior), value=TRUE, invert=F) -->
<!-- ``` -->
<!-- # Computation -->
<!-- Compute the adaptive design for different EHSS approaches and store results as list objects. -->
<!-- ```{r ehss, echo=T} -->
<!-- obj.resEHSS.mix=list() -->
<!-- for (i in c(cases.fix,cases.mix,"vague")){ -->
<!-- #  print(i) -->
<!--   obj.resEHSS.mix[[i]]<-    -->
<!--     adaptiveDesign_normal( -->
<!--       ess = "ehss", ehss.method = "mix.moment",  -->
<!--       ctl.prior=prior[[i]], treat.prior=prior$vague, -->
<!--       N1=N1, Ntarget=Ntarget, Nmin=Nmin, M=M,  -->
<!--       muc=design$muc, mut=design$muc+design$delta, -->
<!--       sc=sc,st=st,sc.known=sc.known,st.known=st.known, -->
<!--       discard.prior = TRUE, -->
<!--       vague = prior$vague, -->
<!--       decision=dec, -->
<!--       nsim=nsim,cores=cores, seed = 123, progress = "none" -->
<!--     ) -->
<!-- } -->
<!-- obj.resEHSS.morita=list() -->
<!-- for (i in cases.mix ){ -->
<!-- #  print(i) -->
<!--   obj.resEHSS.morita[[i]]<- -->
<!--     adaptiveDesign_normal( -->
<!--       ess = "ehss", ehss.method = "morita",  -->
<!--       ctl.prior=prior[[i]], treat.prior=prior$vague, -->
<!--       N1=N1, Ntarget=Ntarget, Nmin=Nmin, M=M,  -->
<!--       muc=design$muc, mut=design$muc+design$delta, -->
<!--       sc=sc,st=st,sc.known=sc.known,st.known=st.known, -->
<!--       discard.prior = TRUE, -->
<!--       vague = prior$vague, -->
<!--       decision=dec, -->
<!--       nsim=nsim,cores=cores, seed = 123, progress = "none" -->
<!--     ) -->
<!-- } -->
<!-- obj.resEHSS.pp=list() -->
<!-- for (i in cases.fix){ -->
<!--  #   print(i) -->
<!--     obj.resEHSS.pp[[i]]<-    -->
<!--       adaptiveDesign_normal( -->
<!--         ess = "ehss", ehss.method = "moment",  -->
<!--         ctl.prior=as.powerprior(prior[[i]]), treat.prior=prior$vague, -->
<!--         N1=N1, Ntarget=Ntarget, Nmin=Nmin, M=M,  -->
<!--         muc=design$muc, mut=design$muc+design$delta, -->
<!--         sc=sc,st=st,sc.known=sc.known,st.known=st.known, -->
<!--         discard.prior = TRUE, -->
<!--         vague = prior$vague, -->
<!--         decision=dec, -->
<!--         nsim=nsim,cores=cores, seed = 123, progress = "none" -->
<!--       ) -->
<!-- } -->
<!-- ``` -->
<!-- Compute the design using ECSS and store results. -->
<!-- ```{r ecss, echo=T} -->
<!-- obj.resECSS=list() -->
<!-- for (i in c(cases.fix,cases.mix)){ -->
<!--   obj.resECSS[[i]]<- suppressMessages(   -->
<!--       adaptiveDesign_normal( -->
<!--         ess = "ecss", min.ecss=-110, D=MSE,  -->
<!--         ctl.prior=prior[[i]], treat.prior=prior$vague, -->
<!--         N1=N1, Ntarget=Ntarget, Nmin=Nmin, M=M,  -->
<!--         muc=design$muc, mut=design$muc+design$delta, -->
<!--         sc=sc,st=st,sc.known=sc.known,st.known=st.known, -->
<!--         discard.prior = TRUE, -->
<!--         vague = prior$vague, -->
<!--         decision=dec, -->
<!--         nsim=nsim,cores=cores, seed = 123, progress = "none" -->
<!--       )) -->
<!-- } -->
<!-- minESS=-40 -->
<!-- obj.resECSS.pp=list() -->
<!-- for (i in cases.fix){ -->
<!--   obj.resECSS.pp[[i]]<-    -->
<!--       adaptiveDesign_normal( -->
<!--         ess = "ecss", min.ecss=-110, D=MSE, -->
<!--         ctl.prior=as.powerprior(prior[[i]]), treat.prior=prior$vague, -->
<!--         N1=N1, Ntarget=Ntarget, Nmin=Nmin, M=M,  -->
<!--         muc=design$muc, mut=design$muc+design$delta, -->
<!--         sc=sc,st=st,sc.known=sc.known,st.known=st.known, -->
<!--         discard.prior = TRUE, -->
<!--         vague = prior$vague, -->
<!--         decision=dec, -->
<!--         nsim=nsim,cores=cores, seed = 123, progress = "none" -->
<!--       ) -->
<!-- } -->
<!-- ``` -->
<!-- # Results -->
<!-- Collect results and reformat as appropriate for ggplot2. -->
<!-- ```{r , echo=T} -->
<!-- gatherResults=function(priors,name.extension,obj){ -->
<!--   res=foreach(i=priors, .combine=rbind) %do% { -->
<!--     data.frame(prior=paste0(i,name.extension),muc=design$muc,delta=design$delta,  -->
<!--                t(sapply(obj[[i]]$list,rowMeans)[-(1:2),])) -->
<!--   } -->
<!--   colnames(res)[5]="samp" -->
<!--   res -->
<!-- } -->
<!-- resEHSS.morita=gatherResults(priors=cases.mix,name.extension =".morita", -->
<!--                              obj=obj.resEHSS.morita ) -->
<!-- resEHSS.mix=gatherResults(priors=c(cases.fix,cases.mix,"vague"),name.extension =".mix", -->
<!--                           obj=obj.resEHSS.mix ) -->
<!-- resEHSS.pp=gatherResults(priors=cases.fix,name.extension =".pp", -->
<!--                          obj=obj.resEHSS.pp ) -->
<!-- resECSS.pp=gatherResults(priors=cases.fix,name.extension =".ecss.pp", -->
<!--                          obj=obj.resECSS.pp ) -->
<!-- resECSS=gatherResults(priors=c(cases.fix,cases.mix),name.extension =".ecss", -->
<!--                       obj=obj.resECSS ) -->
<!-- colnames(resEHSS.morita)[which(colnames(resEHSS.morita)=="ESS.ehss")]="ESS" -->
<!-- colnames(resEHSS.mix)[which(colnames(resEHSS.mix)=="ESS.ehss")]="ESS" -->
<!-- colnames(resEHSS.pp)[which(colnames(resEHSS.pp)=="ESS.ehss")]="ESS" -->
<!-- powerTable <- rbind(resEHSS.morita, resEHSS.mix, resEHSS.pp, resECSS.pp, resECSS) -->
<!-- powerTable$prior= ordered(powerTable$prior,c( "vague.mix" , -->
<!--                                               "inf.mix" , "inf.ecss" ,  -->
<!--                                               "mix50.morita","mix50.mix"  , "mix50.ecss", -->
<!--                                               "inf.pp","inf.ecss.pp" -->
<!--                                                 )) -->
<!-- levels(powerTable$prior)=c( "vague (prior ESS=ECSS)" , -->
<!--                             "informative (prior ESS)" , -->
<!--                             "informative (ECSS)" , -->
<!--                             "EHSS (MTM)", -->
<!--                             "mixture (EHSS (mix))" ,  -->
<!--                             "mixture (ECSS)", -->
<!--                             "power prior (EHSS)" ,  -->
<!--                             "power prior (ECSS)" -->
<!--                               ) -->
<!-- ``` -->
<!-- Plot results for reproduction of Figures in Section 4 of Wiesenfarth and Calderazzo (2019). -->
<!-- ```{r, echo=T} -->
<!-- lightbrown="#CD5B45" -->
<!-- darkbrown="#8B3E2F" -->
<!-- lightblue="#6CA6CD" -->
<!-- darkblue="#27408B" -->
<!-- lightpurple="#AB82FF" -->
<!-- darkpurple="#7D26CD" -->
<!-- colors=c( -->
<!--   "vague (prior ESS=ECSS)"= "black",                    -->
<!--   "informative (prior ESS)"= lightpurple,         -->
<!--   "informative (ECSS)"=darkpurple ,         -->
<!--   "mixture (EHSS (mix))"=lightbrown ,      -->
<!--   "EHSS (MTM)"="orange", -->
<!--   "mixture (ECSS)"=darkbrown ,             -->
<!--   "power prior (EHSS)"=lightblue ,   -->
<!--   "power prior (ECSS)"=darkblue -->
<!-- ) -->
<!-- ltys=c( -->
<!--   "vague (prior ESS=ECSS)"= "dotted",                    -->
<!--   "informative (prior ESS)"= "dotted",         -->
<!--   "informative (ECSS)"="dotted" ,         -->
<!--   "mixture (EHSS (mix))"="dashed" , -->
<!--   "EHSS (MTM)"="dashed", -->
<!--   "mixture (ECSS)"="dashed" ,             -->
<!--   "power prior (EHSS)"="solid" ,   -->
<!--   "power prior (ECSS)"="solid"       -->
<!-- ) -->
<!-- theme=ggplot()+ -->
<!--       xlab("mu0")+ -->
<!--       geom_vline(xintercept=0,colour="grey")+ -->
<!--       scale_colour_manual(values=colors)+scale_linetype_manual(values=ltys) -->
<!-- powerTable.trunc=subset(powerTable,muc>=0) -->
<!-- theme+geom_line(aes(muc, ESS, colour=prior,linetype=prior), -->
<!--                 data=subset(powerTable.trunc,delta==0))+ -->
<!--   ylab("ESS")+ylim(c(-100,100))+ -->
<!--   theme(legend.position = "left") -->
<!-- theme+geom_line(aes(muc, samp, colour=prior,linetype=prior), -->
<!--                 data=subset(powerTable.trunc,delta==0))+ -->
<!--   ylab("Expected final sample size")+ -->
<!--   theme(legend.position = "left") -->
<!-- theme+geom_line(aes(muc, biasSq.mean, colour=prior,linetype=prior), -->
<!--                 data=subset(powerTable.trunc,delta==0))+ -->
<!--   ylab("MSE")+ylim(c(0.005,.025))+ -->
<!--   theme(legend.position = "left") -->
<!-- theme+geom_line(aes(muc, power,  colour=prior,linetype=prior), -->
<!--                 data=subset(powerTable,delta==0))+ -->
<!--   ylab("Type I error")+ylim(c(0.0,.14))+ -->
<!--   theme(legend.position = "left") -->
<!-- theme+geom_line(aes(muc, power,  colour=prior,linetype=prior), -->
<!--                 data=subset(powerTable,delta==tau.vec[2]))+ -->
<!--   ylab(paste0("Power for tau=",tau.vec[2]))+ylim(c(0.0,1))+ -->
<!--   theme(legend.position = "left") -->
<!-- ``` -->
References
==========

Wiesenfarth, M., Calderazzo, S. (2019). Quantification of Prior Information in Terms of Prior Effective Historical and Current Sample Size. Submitted.