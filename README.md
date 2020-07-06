# Baycomse
> The MATLAB packge for "Bayesian compositional regression with structured priors for microbiome feature selection"

The proposed Bayesian sparse regression model addressed the challenges of microbiome data, including the compositional nature of the data, the high dimension, and the relatedness among the features. This packages include two different way of addressing the fixed-sum constraint: the constrast transformation and the generalized transformation. The codes are put separately under "BayesianContrast" and "BayesianGeneralize". Both methods take advantage of the phylogenetic tree information. They formulate a structured prior to link the selection of closely related organisms, which are likely to have a similar effect on the outcome. 

---

# Table of Contents
<!--ts-->
- [Installation](#installation)
- [BayesianContrast](#BayesianContrast)
- [BayesianGeneralize](#BayesianGeneralize)
- [RealDataRelated](#RealDataRelated)
- [GenerateSimData](#GenerateSimData)
- [Contact](#contact)
<!--te-->

# Installation
Please download the packge to your computer and put it in a workpath that your MATLAB can access to it. The codes are developped under MATLAB 2016.

This package includes 4 folders: BayesianContrast, BayesianGeneralize, RealDataRelated and GenerateSimData. We introduce each of these as follows.

# BayesianContrast 
'BayesianContrast' stands for Bayesian compositional regression with contrast transformations. 

Within this folder, 'ContrastCoreFunctions' consists of all the functions that will be called when implementing simulation or real data analysis.
The most important two functions are 'BayesFactor.m' and 'gibbsgamma.m'.
The first one calculates the Bayes factor.
The second one draws samples from the conditional posterior distribution of gamma with excluding or including one model index at each iteration.

'ContrastRealrun' includes the executive functions for real data analysis. Generated numerical and graphical results will be saved in this folder.
You need to prepare the following input data: OTU table, continous outcome and similarity matrix between OTUs.

'ContrastSimulation' includes the executive functions for different simulation scenarios. Generated numerical and graphical results will be saved in 'results/simulation'.
In each sub-folder of 'ContrastSimulation', there are 3 functions--crosstest51.m, simulate51.m and simulate51stable.m
The first one splits the data into 90% training data and 10% testing data. It calculates prediction error, false positives and false negatives.
The second one runs the variable selection on the data once.
The third one runs sensitivity analysis by changing the sparsity parameter a.

# BayesianGeneralize
'BayesianGeneralize' stands for Bayesian compositional regression with generalized transformations. 

Within this folder, 'GeneralizeCoreFunctions' consists of all the functions that will be called when implementing simulation or real data analysis.
The most important two functions are 'BayesFactor.m' and 'gibbsgamma.m'.
The first one calculates the Bayes factor.
The second one draws samples from the conditional posterior distribution of gamma with excluding or including one model index at each iteration.

'GeneralizeRealrun' includes the executive functions for real data analysis. Generated numerical and graphical results will be saved in this folder.
You need to prepare the following input data: OTU table, continous outcome and similarity matrix between OTUs.

'GeneralizeSimulation' includes the executive functions for different simulation scenarios. Generated numerical and graphical results will be saved in 'results/simulation'.
In each sub-folder of 'GeneralizeSimulation', there are 3 functions--crosstest51.m, simulate51.m and simulate51stable.m
The first one splits the data into 90% training data and 10% testing data. It calculates prediction error, false positives and false negatives.
The second one runs the variable selection on the data once.
The third one runs sensitivity analysis by changing the sparsity parameter a.

> %%%load functions
```MATLAB
addpath('C:\Users\lzhang27\Desktop\BayesianCompositionSelection\BayesianGeneralize\GeneralizeCoreFunctions\')
```
> %%%load simulated data
> addpath('C:\Users\lzhang27\Desktop\BayesianCompositionSelection\GenerateSimData\')
> %%%set path
> tmp = matlab.desktop.editor.getActive;
> cd(fileparts(tmp.Filename));
> mkdir('results\simulation');
> cd('results\simulation')

> rng(2018)

> %load data 
> load str2_snr1.mat

%standadize data
[X,stand.mux,stand.Sx] = zscore(log(Z));
[Y,stand.muy,stand.Sy] = zscore(Y);

%specify penalty matrix
c=100;
T=[eye(p);c*ones(1,p)]*diag(1./stand.Sx);

%set the number of burn-in steps and iterations
nburnin=15000;
niter=5000;

%initialize gamma and setsort
nop=floor(n/2);

a0=-11;
a=a0*ones(1,p);

tau=1;
nu=0;
omega=0;
seed=100;

Q=0.002*(ones(p,p)-eye(p));
for i=180:20:380
    for j=(i+20):20:400
        Q(i,j)=4;
    end
end

for i=580:20:780
    for j=(i+20):20:800
        Q(i,j)=4;
    end
end

for i=445:459
    for j=(i+1):460
        Q(i,j)=4;
    end
end

for i=945:959
     for j=(i+1):960
         Q(i,j)=4;
     end
end

for i=45:59
     for j=(i+1):60
         Q(i,j)=4;
     end
end

Q=(Q+Q')/2;

tic
[gamma,betahat,MSE,nselect,Yhat]=gibbsgamma(nburnin,niter,p,nop,Y, X,T, a, Q, n,tau,nu,omega,seed,true,stand,true);
toc



# RealDataRelated 
'RealDataRelated' consists of executive functions that process microbiome data and calculate association matrices based on phylogenetic tree.

These functions are written in R.
'phylodistance.R' calculates the distance and correlation along the phylogenetic tree.
'plotcirculartree.R' plots the phylygenetic tree in a circular manner.
'plotBayesianprediction.R' plots the fitted predictions vs. observations. 
'plotCorPre.R' plots the correlation and precision matrix obtained from microbiome data.


# GenerateSimData
'GenerateSimData' consists of executive functions that generate different simulations scenarios that will be used in 'ContrastSimulation' and 'GeneralizeSimulation'.

The codes are named as follows, for example, datageneration_str1_snr1_n50_p30.m, denotes independent structure with signal noise ratio 1, sample size n=50 and number of variables p=30. 
'CoreFunctions' includes the codes that will be called when implementing data generations. 

# Contact
If you have any questions, please contact me at liangliangzhang.stat@gmail.com
