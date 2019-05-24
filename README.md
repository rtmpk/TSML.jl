<div align="center"> <img
src="https://ibm.github.io/TSML.jl/tsmllogo/tsmllogo13.png"
alt="TSML Logo" width="250"></img> </div>

| **Documentation** | **Build Status** | **Help** |
|:---:|:---:|:---:|
| [![][docs-dev-img]][docs-dev-url] [![][docs-stable-img]][docs-stable-url] | [![][travis-img]][travis-url] [![][codecov-img]][codecov-url] | [![][slack-img]][slack-url] [![][gitter-img]][gitter-url] |

### TSML is a package for time series data processing, classification, clustering, and prediction written in [Julia](http://julialang.org/).

The design/framework of this package is influenced heavily by Samuel Jenkins' [Orchestra.jl](https://github.com/svs14/Orchestra.jl) and Paulito Palmes [CombineML.jl](https://github.com/ppalmes/CombineML.jl) packages.

## Package Features

- TS aggregation based on date-time interval
- TS imputation based on symmetric Nearest Neighbors
- TS statistical metrics for data quality assessment
- TS classification for automatic data discovery
- TS ML with more than 100+ libraries from caret, scikitlearn, and julia
- TS date-val matrix conversion of 1-d TS using sliding windows for ML input
- Pipeline API for high-level workflow processing
- Easily extensible architecture relying just two main interfaces: fit and transform
- Common API wrappers for ML libs from PyCall and RCall

## Installation
- TSML is part of the Julia package repository
- It can be installed from the `julia>` REPL by typing
`]` to enter into the `pkg>` REPL mode and run:

```julia
pkg> add TSML
```

or by using the `Pkg` API:

```julia
julia> using Pkg
julia> Pkg.add("TSML")
```

## Overview

TSML (Time Series Machine Learning) is package for Time Series data processing, classification, and prediction. It combines ML libraries from Python's ScikitLearn, R's Caret, and Julia using a common API and allows seamless ensembling and integration of heterogenous ML libraries to create complex models for robust time-series prediction.

The package assumes a two-column table composed of Dates and Values. The first part of the workflow aggregates values based on the specified date/time interval which minimizes occurence of missing values and noise. The aggregated data is then left-joined to the complete sequence of dates in a specified date/time interval. Remaining missing values are replaced by k nearest neighbors where k is the symmetric distance from the location of missing value. This approach can be called several times until there are no more missing values.

The next part extracts the date features and convert the values into matrix form parameterized by the _size_ and _stride_ of the sliding window representing the dimension of the input for ML training and prediction.

The final part combines the date features and the matrix of values as input to the ML with the output representing the values of the time periods to be predicted ahead of time.

TSML uses a pipeline which iteratively calls the __fit__ and __transform__ families of functions relying on multiple dispatch to select the correct algorithm from the steps outlined above.

Machine learning functions in TSML are wrappers to the corresponding Scikit-learn, Caret, and native Julia ML libraries. There are more than hundred classifiers and regression functions available using a common API. 

Below is an example of the pipeline workflow: 

- #### Setup different transformers
```julia
# Setup source data and filters to aggregate and impute hourly
fname = joinpath(dirname(pathof(TSML)),"../data/testdata.csv")
csvreader = DataReader(Dict(:filename=>fname,:dateformat=>"dd/mm/yyyy HH:MM"))
valgator = DateValgator(Dict(:dateinterval=>Dates.Hour(1))) # aggregator
valnner = DateValNNer(Dict(:dateinterval=>Dates.Hour(1)))   # imputer
stfier = Statifier(Dict(:processmissing=>true))             # get statistics
```

- #### Load csv data, aggregate, and get statistics
```julia
# Setup pipeline without imputation and run
mpipeline1 = Pipeline(Dict(
  :transformers => [csvreader,valgator,stfier]
 )
)
fit!(mpipeline1)
respipe1 = transform!(mpipeline1)

# Show statistics including blocks of missing data stats
@show respipe1
```

 - #### Load csv data, aggregate, impute, and get statistics
```julia
# Add imputation in the pipeline and rerun
mpipeline2 = Pipeline(Dict(
  :transformers => [csvreader,valgator,valnner,stfier]
 )
)
fit!(mpipeline2)
respipe2 = transform!(mpipeline2)

# Show statistics including blocks of missing data stats
@show respipe2
```

- #### Load csv data, aggregate, impute, and show output after imputation
```julia
# Add imputation in the pipeline and rerun
mpipeline2 = Pipeline(Dict(
  :transformers => [csvreader,valgator,valnner,stfier]
 )
)
fit!(mpipeline2)
respipe2 = transform!(mpipeline2)

# Show statistics including blocks of missing data stats
@show respipe2
```

## Feature Requests and Contributions

We welcome contributions, feature requests, and suggestions. Here is the link to open an [issue][issues-url] for any problems you encounter. If you want to contribute, please follow the guidelines in [contributors page][contrib-url].

## Help usage

Usage questions can be posted in:
- [Julia Slack](https://julialang.org/community/) 
- [Gitter TSML Community][gitter-url]
- [Julia Discourse forum][discourse-tag-url]


[contrib-url]: https://github.com/IBM/TSML.jl/blob/master/CONTRIBUTORS.md
[issues-url]: https://github.com/IBM/TSML.jl/issues

[discourse-tag-url]: https://discourse.julialang.org/

[gitter-url]: https://gitter.im/TSMLearning/community
[gitter-img]: https://badges.gitter.im/ppalmes/TSML.jl.svg

[slack-img]: https://img.shields.io/badge/chat-on%20slack-yellow.svg
[slack-url]: https://julialang.slack.com

[docs-stable-img]: https://img.shields.io/badge/docs-stable-blue.svg
[docs-stable-url]: https://ibm.github.io/TSML.jl/stable/
[docs-dev-img]: https://img.shields.io/badge/docs-dev-blue.svg
[docs-dev-url]: https://ibm.github.io/TSML.jl/latest/

[travis-img]: https://travis-ci.org/ppalmes/TSML.jl.svg?branch=master
[travis-url]: https://travis-ci.org/ppalmes/TSML.jl

[codecov-url]: https://codecov.io/gh/IBM/TSML.jl
[codecov-img]: https://codecov.io/gh/IBM/TSML.jl/branch/master/graph/badge.svg
