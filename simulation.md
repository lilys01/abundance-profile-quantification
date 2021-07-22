## Install Mason2 v.2.0.9:
http://packages.seqan.de/mason2/

## Install Kraken2:

```wget http://github.com/DerrickWood/kraken2/archive/v2.0.8-beta.tar.gz```
```tar -xvzf v2.0.8-beta.tar.gz```
```cd kraken2-2.0.8-beta```
```./install_kraken2.sh .```
```PATH=$PATH:~/kraken2-2.0.8-beta```

Reference:
https://github.com/DerrickWood/kraken2/blob/master/docs/MANUAL.markdown#installation

## Data Generation
Followed:
https://github.com/DerrickWood/kraken2-experiment-code/blob/master/README_DataGeneration.md

# Paired Read Simulation
In the "Bacteria" directory, run:
## 45 MB
```bash
for file in *.fa; do
  mason2-2.0.9-Linux-x86_64/bin/mason_simulator -ir $file --seed 42 -n 5000 --num-threads 4 \
    -o $(basename $file .fa)_1.fq \
    -or $(basename $file .fa)_2.fq \
    --read-name-prefix taxid_$(basename $file .fa).
done

cat *_1.fq > simulated_data_1.fq
cat *_2.fq > simulated_data_2.fq
```

# Database Creation
```bash
kraken2-2.0.8-beta/kraken2-build --download-taxonomy --use-ftp --threads 32 --db databases/strex
```
```bash
kraken2-2.0.8-beta/kraken2-build --db databases/strex  --no-masking  --threads 32 --add-to-library /datasets/kraken-public/krakenData/kraken2-experiment-code/strain_excluded.fna
```
```bash
kraken2-2.0.8-beta/kraken2-build --db databases/strex --threads 32 --build
```
# Classification
For each set of paired reads
```bash
kraken2-2.0.8-beta/kraken2 -db databases/strex --memory-mapping --threads 16 --paired --output results_very_small.out data/bacteria_very_small_1.fq data/bacteria_very_small_2.fq
```
```bash
kraken2-2.0.8-beta/kraken2 -db databases/strex --memory-mapping --threads 16 --paired --output results_small.out data/bacteria_small_1.fq data/bacteria_small_2.fq
```
```bash
kraken2-2.0.8-beta/kraken2 -db databases/strex --memory-mapping --threads 16 --paired --output results.out data/bacteria_1.fq data/bacteria_2.fq
```
```bash
kraken2-2.0.8-beta/kraken2 -db databases/strex --memory-mapping --threads 16 --paired --output results_large.out data/bacteria_large_1.fq data/bacteria_large_2.fq
```
# Profiling
```bash
gprof kraken2-2.0.8-beta/classify > analysis.txt
 ```
