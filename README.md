# GapFindFill.jl Documentation
- [Motivation](https://github.com/varnerlab/GapFindFill.jl/blob/master/README.md#statement-of-need)
- [Installation](https://github.com/varnerlab/GapFindFill.jl/blob/master/README.md#installation-instruction)
- [Examples](https://github.com/varnerlab/GapFindFill.jl/blob/master/README.md#example)
- [API documentation](https://github.com/varnerlab/GapFindFill.jl/blob/master/README.md#api-documentation)
- [References](https://github.com/varnerlab/GapFindFill.jl/blob/master/README.md#reference)
- [Contact](https://github.com/varnerlab/GapFindFill.jl/blob/master/README.md#support-or-contact)


## Motivation
The current implementation of `GapFind` and `GapFill` is in GAMS, which charges a significant license fee from each user, even though many solvers are free for academic purposes. To promote the usage of this computational tool, we developed this open-source Julia package, `GapFindFill.jl`, to enable researchers to use *GapFind* and *GapFill* for free by harnessing the power of academic free solvers provided by [Gurobi](https://www.gurobi.com/) and [IBM](https://www.ibm.com/analytics/cplex-optimizer).
`GapFindFill.jl` is implemented in Julia and makes use of the high-level interface [JuMP.jl](https://github.com/JuliaOpt/JuMP.jl).
JuMP is a domain-specific modeling language for mathematical optimization embedded in Julia. With JuMP, it is easier for users to specify and call different optimizers to solve optimization problems in `GapFindFill.jl` than using interfaces provided by solvers directly. Built upon the generic high-level programming language Julia, users can embed `GapFindFill.jl` in their complex workflows to simplify task processing. GAMS, as a specific optimization tool, does not provide support for processing other tasks or integrate with other programming languages.


## Installation
To use this package, the user needs to [install Julia](https://julialang.org/downloads/platform.html) first. This version is built on [Julia v1.1](https://julialang.org/downloads/oldreleases.html).
[Gurobi.jl](https://github.com/JuliaOpt/Gurobi.jl) is used in GapFindFill.jl as the default solver for both gap finding and filling. [Gurobi](http://www.gurobi.com/) provides a free academic license for non-commercial use.
Users can also choose to use [GLPK.jl](https://github.com/JuliaOpt/GLPK.jl) or [CPLEX.jl](https://github.com/JuliaOpt/CPLEX.jl) for gap finding. GLPK is free for all users, while CPLEX provides a free academic license. But note that compared to Gurobi and CPLEX, it takes GLPK much more time and memory to solve the same problem. For gap filling, Gurobi is set as the only solver since it worked way better than the other two in our tests.

For [Julia v1.0](https://julialang.org/downloads/) users, any package compatibility issues while testing GapFindFill.jl can be resolved by pinning CPLEX.jl and Gurobi.jl to a specific version by running the following commands in `pkg>` mode:
```julia
pin Gurobi@0.6.0
pin CPLEX@0.5.0
```

For [Julia v1.2](https://julialang.org/downloads/) users, GapFindFill.jl will be compatible with Julia v1.2 once [CPLEX.jl](https://github.com/JuliaOpt/CPLEX.jl) is updated for Julia v1.2.

**Installation**.
Within Julia, press `]` to enter `pkg>` mode.
To install GapFindFill.jl, issue the command:
```julia
add GapFindFill
```
or issue 
```julia
add https://github.com/varnerlab/GapFindFill.jl.git
```
To use GapFindFill in your project simply issue the command:
```julia
using GapFindFill
```

**Test**.
To test GapFindFill installation, use the following command in `pkg>` mode:
```julia
test GapFindFill
```
which runs two examples under [test](https://github.com/varnerlab/GapFindFill/tree/master/test) directory.
The expected outcomes from this test set are illustrated in [Example](https://github.com/varnerlab/GapFindFill/blob/master/README.md#example) section.

**Uninstallation**.
To delete GapFindFill package use the following command in `pkg>` mode:
```julia
rm GapFindFill
```


## Examples
Two examples are provided under [test](https://github.com/varnerlab/GapFindFill/tree/master/test) showing how to use GapFindFill.jl to solve two problems in [Manaras paper]((https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-8-212)).
[testGapFind.jl](https://github.com/varnerlab/GapFindFill/blob/master/test/testGapFind.jl) and
[testGapFill.jl](https://github.com/varnerlab/GapFindFill/blob/master/test/testGapFill.jl) demonstrate how to set up *GapFind* and *GapFill* models, respectively.
We reported our experimental results here for users' reference. All experiments were run on an Intel Core i7-6700 CPU with Ubuntu 18.04.3 LTS.

For `find_gaps,` the testing example contains 1668 compounds and 2383 reactions, which is of the size of real problems.
The expected outcome is to find 115 blocked metabolites in the network, namely, 1553 non-blocked metabolites. The following table shows a run-time comparison between GAMS and GapFindFill.jl on gap finding.

Software | Solver | Running time (s)
:--- | :--- | :---
GAMS | CPLEX | 3.3
GAMS | Gurobi | 0.6
GapFindFill.jl | CPLEX | 68.4
GapFindFill.jl | Gurobi | 64.5

GAMS is fast in solving large-size problems like the testing example, but GapFindFill.jl finished the job in less than 2 minutes, which is also acceptable for real problems as large as this example. Using either Gurobi or CPLEX in GapFindFill.jl did not make much difference in running time, while GLPK was unable to solve the testing example within 1 hour. Thus, although `find_gaps` allows users to specify GLPK as the solver, it is recommended only for small-scale problems.

For `fill_gaps_min,` the testing example contains 1822 compounds and 2888 reactions. The outcomes are summarized in the following table:

No-production-metabolite | Solution 1 | Solution 2
:--- | :--- | :---
2doxg6p[c] | EX_2DOXG6P(e) & 2DOXG6P_t |
2dglc[c] | EX_2DGLC(e) & 2DGLC_t | EX_2DOXG6P(e) & 2DOXG6P_t
alatrna[c] | TRNAALA_t & EX_TRNAALA(e) | ALATRNA_t & EX_ALATRNA(e)
2dr5p[c] | DRIB_t & EX_DRIB(e) | 2DR5P_t & EX_2DR5P(e)
4gudbutn[c] | 4GUDBD_t & EX_4GUDBD(e) | 4GUDBUTN_t & EX_4GUDBUTN(e)

The following table shows a run-time comparison between GAMS and GapFindFill.jl on gap filling:

Software | Solver | Running time (s)
:--- | :--- | :---
GAMS | Gurobi | 6.2
GapFindFill.jl | Gurobi | 28.7

The difference between GAMS and GapFindFill.jl is smaller than solving gap-finding tasks.
This is largely because GapFindFill.jl benefited from Julia's just-in-time feature, as similar problems were solved repeatedly.

## API documentation
Two interfaces are provided, `find_gaps()` and `fill_gaps_min()`, for gap finding and filling, respectively.

The `find_gaps()` interface:
```julia
function find_gaps(isRev::Array{Bool}, isCyt::Array{Bool}, isExt::Array{Bool},
    stoiMatrix::Array{Float64}, fluxLB::Array{Float64}, fluxUB::Array{Float64};
    epsilon::Float64 =0.001, bigM::Float64 =1000.0, nonZero::Float64 =1e-8,
    solver::Module=Gurobi)
```

Input arguments:

Argument | Required | Description
:--- | :--- | :---
isRev | yes | true if the corresponding reaction is reversible
isCyt | yes | true if the corresponding metabolite is in the cytosol
isExt | yes | true if the corresponding metabolite is in the extracellular compartment;
stoiMatrix | yes | stoichiometric matrix, \|compounds\| * \|reactions\|
fluxLB | yes | flux lower bound;
fluxUB | yes | flux upper bound;
epsilon | optional | minimum amount to be considered active;
bigM | optional | constant used in MILP model;
nonZero | optional | minimum stoichiometric coefficient to be considered non-zero;
solver | optional | CPLEX, Gurobi, or GLPK.

Outputs Description:

Argument | Description
:--- | :---
m | the JuMP model;
objVal | objective value, i.e., number of non-blocked compounds;
status | termination status;
binX | 1 if the corresponding compound is non-blocked;

The `fill_gaps_min()` interface:
```julia
function fill_gaps_min(isMd::Array{Bool}, isDb::Array{Bool}, isRev::Array{Bool},
    isCyt::Array{Bool}, isExt::Array{Bool}, noProdID::Array{Integer},
    stoiMatrix::Array{Float64}, fluxLB::Array{Float64}, fluxUB::Array{Float64};
    epsilon::Float64 =0.001, bigM::Float64 =1000.0, nonZero::Float64 =1e-9)
```
Inputs description:

Argument | Required | Description
:--- | :--- | :---
isMd | yes | true if the corresponding reaction is in the model;
isDb | yes | true if the corresponding reaction is in the database, i.e., not in the model;
isRev | yes | true if the corresponding reaction is reversible
isCyt | yes | true if the corresponding metabolite is in the cytosol
isExt | yes | true if the corresponding metabolite is in the extracellular compartment;
noProdID | yes | indices of no-production-metabolites;
stoiMatrix | yes | stoichiometric matrix, \|compounds\| * \|reactions\|
fluxLB | yes | flux lower bound;
fluxUB | yes | flux upper bound;
epsilon | optional | minimum amount to be considered active;
bigM | optional | constant used in MILP model;
nonZero | optional | minimum stoichiometric coefficient to be considered non-zero.

Outputs Description:

Argument | Description
:--- | :---
results | a dictionary, each key is an index of no-production-metabolites, and each value is a Set containing all possible gap-filling ways with a minimum number of added reactions.

## References
- Maranas, Costas D., and Ali R. Zomorrodi. Optimization methods in metabolic networks. John Wiley & Sons, 2016.
- [Satish Kumar V, Dasika MS, Maranas CD. Optimization based automated curation of metabolic reconstructions. BMC Bioinformatics. 2007 Jun 20;8:212. doi: 10.1186/1471-2105-8-212. PMID: 17584497; PMCID: PMC1933441.](https://pubmed.ncbi.nlm.nih.gov/17584497/)
- [GapFind/GapFill](http://www.maranasgroup.com/software.htm) in [GAMS](https://www.gams.com/)

## Contact
Need help with installation or function? Feel free to contact [VarnerLab](https://github.com/varnerlab) or [authors](https://www.cheme.cornell.edu/faculty-directory/jeffrey-d-varner).

## Funding
The work described was supported by the [Center on the Physics of Cancer Metabolism at Cornell University](https://psoc.engineering.cornell.edu) through Award Number 1U54CA210184-01 from the [National Cancer Institute](https://www.cancer.gov).
The content is solely the responsibility of the authors and does not necessarily
represent the official views of the [National Cancer Institute](https://www.cancer.gov) or the [National Institutes of Health](https://www.nih.gov).  
