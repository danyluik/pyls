# pyls

This package provides a Python interface for partial least squares (PLS) analysis, a multivariate statistical technique used to relate two sets of variables.

Original repository, with installation instructions: https://github.com/rmarkello/pyls

Guide for running a PLS analysis (these docs will eventually be integrated): https://github.com/CoBrALab/documentation/wiki/Running-PLS-in-Python:-pyls


# Split-half resampling

Implemented as in Nakua et al. (2023): https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10028915/

All of this assumes you're following the docs as described here: https://github.com/CoBrALab/documentation/wiki/Running-PLS-in-Python:-pyls#for-plsc

To run the analysis, make sure you specify the number of splits to perform, as indicated by the `n_split` argument:

```python
from pyls import behavioral_pls

bpls = behavioral_pls(X, Y, n_perm=n_perm, n_boot=n_boot, n_split=n_split, seed=123)
```

To access your results:
```python
os.makedirs(f'{out_dir}/splitres/', exist_ok=True)
np.savetxt(f'{out_dir}/splitres/splitres_ucorr.csv', bpls['splitres']['ucorr'], delimiter=',')
np.savetxt(f'{out_dir}/splitres/splitres_vcorr.csv', bpls['splitres']['ucorr'], delimiter=',')
np.savetxt(f'{out_dir}/splitres/splitres_ucorr_avg.csv', bpls['splitres']['ucorr_avg'], delimiter=',')
np.savetxt(f'{out_dir}/splitres/splitres_vcorr_avg.csv', bpls['splitres']['vcorr_avg'], delimiter=',')
np.savetxt(f'{out_dir}/splitres/splitres_ucorr_pvals.csv', bpls['splitres']['ucorr_pvals'], delimiter=',')
np.savetxt(f'{out_dir}/splitres/splitres_vcorr_pvals.csv', bpls['splitres']['vcorr_pvals'], delimiter=',')
np.savetxt(f'{out_dir}/splitres/splitres_ucorr_lo-uplim.csv', np.column_stack((bpls['splitres']['ucorr_lolim'], bpls['splitres']['ucorr_uplim'])),delimiter=',')
np.savetxt(f'{out_dir}/splitres/splitres_vcorr_lo-uplim.csv', np.column_stack((bpls['splitres']['vcorr_lolim'], bpls['splitres']['vcorr_uplim'])),delimiter=',')
```

The p-values come from a permutation testing procedure. Here, split-half resampling is run on each of your reshuffled datasets from the main permutation test. For each permutation instance, we get an average correlation across split halves for each LV. Then, for each LV, we take the average correlation across splits from the original analysis, and test how many times this value was exceeded across permutations, giving a p-value. This is done separately for U (brain) and V (behaviour).

The lower/upper limits are the 95% confidence intervals of the average split-half values across permutations. Not sure yet why we'd need this. 

The outputs are summarized below:


* L: number of components
* split: number of splits

* **splitres_ucorr.csv, splitres_vcorr.csv** (L x split): value of the split-half correlation for each LV and split instance.
* **splitres_ucorr_avg.csv, splitres_vcorr_avg.csv** (L x 1): the above, but averaged across split-halves.
* **splitres_ucorr_pvals.csv, splitres_vcorr_pvals.csv** (L x 1): stability p-values by LV from the permutation testing procedure.
* **splitres_ucorr_lp-uplim.csv, splitres_ucorr_lp-uplim.csv** (L x 2): 95% confidence intervals of average split-half values across permutations for each LV. Lower limit in the first column, upper limit in the second.




