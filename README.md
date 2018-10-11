# dlbayes

The goal of dlbayes is to implement the Dirichlet-Laplace shrinkage prior in Bayesian linear regression and variable selection, featuring: 
- utility functions to implement Dirichlet-Laplace priors; 
- scalability in Bayesian linear regression; 
- variable selection based on penalized credible regions. 

## Installation

You can install the released version of dlbayes from [CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("dlbayes")
```

## Example

``` 
  rho=0.5
  p=1000
  n=100
  # set up correlation matrix
  m<-matrix(NA,p,p)
  for(i in 1:p){
    for(j in i:p){
      m[i,j]=rho^(j-i)
    }
  }
  m[lower.tri(m)]<-t(m)[lower.tri(m)]
  # generate x
  library("mvtnorm")
  x=rmvnorm(n,mean=rep(0,p),sigma=m)
  # generate beta
  beta=c(rep(0,10),runif(n=5,min=-1,max=1),rep(0,20),runif(n=5,min=-1,max=1),rep(0,p-40))
  # generate y
  y=x%*%beta+rnorm(n)
  #tuning hyperparameter [1/max(n,p),1/2]
  hyper=dlhyper(x,y)
  # MCMC sampling
  dlresult=dl(x,y,hyper=hyper)
  
  # visualization of Dirichlet-Lapace priors
  # set "plt=TRUE" to make plots 
  # theta=dlprior(hyper=1/2,p=10000000,plt=TRUE,min=-5,max=5,sigma=1)
  
  # summary of posterior samples 
  da=dlanalysis(dlresult,alpha=0.05)
  da$betamean
  da$betamedian
  da$LeftCI
  da$RightCI
  
  # variable selection
  betaresult=dlvs(dlresult)
  # indices of selected variables 
  num=which(betaresult!=0)
  # coefficients of selected variable
  coef=betaresult[num]
```
## Reference 

Bhattacharya, A., Pati, D., Pillai, N. S., and Dunson, D. B. (2015). "Dirichlet–Laplace priors for optimal shrinkage." *Journal of the American Statistical Association*, 110(512): 1479–1490. 

Bhattacharya, A., Chakraborty, A., and Mallick, B. K. (2016). "Fast sampling with Gaussian scale-mixture priors in high-dimensional regression." *Biomoetrika*, 103(4): 985–991. 

Bondell, H. D. and Reich, B. J. (2012). "Consistent high-dimensional Bayesian variable selection via penalized credible regions." *Journal of the American Statistical Association*, 107(500): 1610–1624. 

