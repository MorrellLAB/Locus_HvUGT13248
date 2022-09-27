# VeP Analysis

VeP only works on bgzipped and tabix indexed VCF files, so if they are not bgzipped and tabix indexed, please do so first.

Curently VeP requires tabix indexed VCF files, meaning the pseudomolecular positions cannot be stored in a tbi index. So, we'll need to run VeP on the parts positions VCF (i.e., chromosomes are split into two parts).

Run VeP with scripts:

```bash
# In dir: ~/GitHub/Locus_HvUGT13248/VEP
./Vep-cohort.job
```
