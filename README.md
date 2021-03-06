# Causal Expectation-Maximisation

This bundle contains the manuscript preseted at the WHY workshop (NeurIPS2021) and entitled  "Causal Expectation-Maximisation". 
The organisation  is the following:

- _example_: code and data for replicating the examples in the paper.
- _experiments_: Java files for replicating the experiments.
- _lib_: packages needed for running the code.
- _models_: set of structural causal models in UAI format considered in the experimentation.
- _pom.xml_: maven configuration file required for building a jar file with the sources.


## Setup

The code has been tested with **Java openjdk 12.0.1**. For checking The Java version running in your system use the 
following command:

```
$ java --version
```

```
openjdk 12.0.1 2019-04-16
OpenJDK Runtime Environment (build 12.0.1+12)
OpenJDK 64-Bit Server VM (build 12.0.1+12, mixed mode, sharing)
```


## Running EMCC

The experiments provided in the paper can be replicated with the code in `experiments/RunCEM.java` which
will require the functionality at `lib/causalem.jar`. Basically, this JAR package contains the code
in the sources folder together with the third party dependencies. 


The beforehand mentioned java file allows to compute the _probability of necessity and sufficiency (PNS)_ using EMCC.
It takes as input a SCM and a dataset. If the later is not specified, the data will be sampled from the model, which shoul
be fully-specified in that case (with the marginals over the exogenous variables). For a further description of how to run this code, we can simply run the help menu as follows. 

```
java -cp lib/causalem.jar experiments/RunEMCC.java --help
```

```
Usage: <main class> [-hq] [--simpleOutput] [-d=<dataSize>] [-f=<datafile>]
                    [-g=<infGroundTruth>] [-l=<logfile>] [-m=<maxiter>]
                    [-o=<outputFolder>] [-p=<method>] [-s=<seed>]
                    [-t=<timeout>] [-x=<executions>] [-X=<cause>] [-Y=<effect>]
                    <modelPath>
      <modelPath>           Model path in UAI format.
      --simpleOutput        If activated, log-likelihood and kl statistics are not
                              computed.
  -d, --datasize=<dataSize> Size of the sampled data. Default 1000
  -f, --datafile=<datafile> CSV file with the data. Default null
  -g, --groundtruth=<infGroundTruth>
                            Inference method for ground truth
  -h, --help                display a help message
  -l, --logfile=<logfile>   Output file for the logs.
  -m, --maxiter=<maxiter>   Maximum EM iterations per execution. Default 200
  -o, --output=<outputFolder>
                            Output folder for the results.
  -p, --policy=<method>     Selection Policy: LAST, BISECTION_BORDER_SAME_PATH,
                              BISECTION_BORDER, BISECTION_ALL
  -q, --quiet               Controls if log messages are printed to standard output.
  -s, --seed=<seed>         Random seed. Default 0
  -t, --timeout=<timeout>   Timeout in seconds. Default 6000s.
  -x, --executions=<executions>
                            Number of EM executions. Default 10
  -X, --cause=<cause>       Cause endogenous variable. Default is 0.
  -Y, --effect=<effect>     Effect endogenous variable. Default is the one with the
                              higher id.
```


### Experiments with the synthetic models

As an example, let us consider the following command will run EMCC with the quasi-markovian model `./models/synthetic/s1b_chain_twExo1_nEndo5_15.uai` with
10 EM runs and a sample size of 1000. The endogenous variables considered for the PNS query are the first and the last one
in the chain.

```
java -cp lib/causalem.jar experiments/RunEMCC.java --executions 10 --datasize 1000 --simpleOutput ./models/synthetic/s1b_chain_twExo1_nEndo5_15.uai
```

The output is:

```
[2021-06-03T11:53:15.302092][INFO][java] _x10_pLAST_m200_s0
[2021-06-03T11:53:15.317823][INFO][java] Input args: --executions;10;--datasize;1000;--simpleOutput;./models/synthetic/s1b_chain_twExo1_nEndo5_15.uai
[2021-06-03T11:53:15.323682][INFO][java] Reading model at ./models/synthetic/s1b_chain_twExo1_nEndo5_15.uai
[2021-06-03T11:53:15.400182][INFO][java] Loaded SCM: ([0, 1, 2, 3, 4, 5, 6, 7], [(6,0), (0,1), (6,1), (1,2), (5,2), (2,3), (5,3), (3,4), (7,4)])
[2021-06-03T11:53:15.430436][INFO][java] Endo C-components: [[0, 1], [2, 3], [4]]
[2021-06-03T11:53:15.432970][INFO][java] Exo C-components: [[6], [5], [7]]
[2021-06-03T11:53:15.435427][INFO][java] Sampling 1000 instances
[2021-06-03T11:53:15.633649][INFO][java] Model statistics: {seed=0, markovian=false, exoTW=1, nExo=3, nEndo=5}
[2021-06-03T11:53:15.635827][INFO][java] Running exact method: cve
[2021-06-03T11:53:16.684164][INFO][java] Exact PSN      :       [0.0, 0.02879561999999999] in 1043 ms.
[2021-06-03T11:53:16.685239][INFO][java] Compatible data: false
[2021-06-03T11:53:16.693564][INFO][java] Building model with EM
[2021-06-03T11:53:30.118242][INFO][java] Finished 10 EM executions in 13421 ms.
[2021-06-03T11:53:30.136847][INFO][java] Approx PSN with 1 points:      [0.01750783969720409, 0.01750783969720409]       ( 200 iter. 7 ms.)
[2021-06-03T11:53:30.147855][INFO][java] Approx PSN with 2 points:      [0.01750783969720409, 0.017689946329473898]      ( 200 iter. 8 ms.)
[2021-06-03T11:53:30.155904][INFO][java] Approx PSN with 3 points:      [1.7969845934609707E-4, 0.017689946329473898]    ( 200 iter. 4 ms.)
[2021-06-03T11:53:30.167413][INFO][java] Approx PSN with 4 points:      [1.7969845934609707E-4, 0.017689946329473898]    ( 200 iter. 6 ms.)
[2021-06-03T11:53:30.180226][INFO][java] Approx PSN with 5 points:      [1.7969845934609707E-4, 0.02845926422090442]     ( 200 iter. 7 ms.)
[2021-06-03T11:53:30.194369][INFO][java] Approx PSN with 6 points:      [1.7969845934609707E-4, 0.02845926422090442]     ( 200 iter. 9 ms.)
[2021-06-03T11:53:30.208884][INFO][java] Approx PSN with 7 points:      [1.7969845934609707E-4, 0.02845926422090442]     ( 200 iter. 8 ms.)
[2021-06-03T11:53:30.225500][INFO][java] Approx PSN with 8 points:      [1.7969845934609707E-4, 0.02845926422090442]     ( 200 iter. 10 ms.)
[2021-06-03T11:53:30.242967][INFO][java] Approx PSN with 9 points:      [1.7969845934609707E-4, 0.02845926422090442]     ( 200 iter. 13 ms.)
[2021-06-03T11:53:30.261002][INFO][java] Approx PSN with 10 points:     [1.7969845934609707E-4, 0.02845926422090442]     ( 200 iter. 10 ms.)
[2021-06-03T11:53:30.262813][INFO][java] Done 10 counterfactual queries in 82 ms.
results=dict(seed=0, markovian=False, innerPoints=[0, 0, 0, 0, 0, 0, 0, 0, 0, 0], pnsExact_u=0.02879561999999999, error=False, nEndo=5, pnsEM_l=[0.01750783969720409, 0.01750783969720409, 1.7969845934609707E-4, 1.7969845934609707E-4, 1.7969845934609707E-4, 1.7969845934609707E-4, 1.7969845934609707E-4, 1.7969845934609707E-4, 1.7969845934609707E-4, 1.7969845934609707E-4], errorMsg='', timeExact=1043, timeQuery=[7, 8, 4, 6, 7, 9, 8, 10, 13, 10], compatible=False, file='s1b_chain_twExo1_nEndo5_15.uai', folder='synthetic', timeBuild=13421, pnsEM_u=[0.01750783969720409, 0.017689946329473898, 0.017689946329473898, 0.017689946329473898, 0.02845926422090442, 0.02845926422090442, 0.02845926422090442, 0.02845926422090442, 0.02845926422090442, 0.02845926422090442], exoTW=1, groundtruth='cve', nExo=3, pnsExact_l=0.0, iterEM=[200, 200, 200, 200, 200, 200, 200, 200, 200, 200])
```

Note that the last line is a Python sentence for creating a dictionary containing all the statistics from the execution.


## Creating a deliverable

In order to create a deliverable JAR file with all the dependencies, run the following command.

```
$ mvn clean package
```

_Note: maven should be installed in the system_ 



## Troublesshouting

- When running a Java command, the error `Error: Could not find or load main class` means that the Java path is not correctly defined in your system.

