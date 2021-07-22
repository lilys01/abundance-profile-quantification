## Install Mason2 v.2.0.9:
http://packages.seqan.de/mason2/

For linux (64bit):
```bash
wget http://packages.seqan.de/mason2/mason2-2.0.9-Linux-x86_64.tar.xz
tar -xf mason2-2.0.9-Linux-x86_64.tar.xz
PATH=$PATH:~/mason2-2.0.9-Linux-x86_64/bin
```

## Install Kraken2:

```bash
wget http://github.com/DerrickWood/kraken2/archive/v2.0.8-beta.tar.gz
tar -xvzf v2.0.8-beta.tar.gz
cd kraken2-2.0.8-beta
./install_kraken2.sh .
PATH=$PATH:~/kraken2-2.0.8-beta
```

Installation Docs:
https://github.com/DerrickWood/kraken2/blob/master/docs/MANUAL.markdown#installation

## Data Generation
Followed steps in directions below, with exception to the last step which is detailed under [Paired Read Simulation](#Paired-Read-Simulation):
https://github.com/DerrickWood/kraken2-experiment-code/blob/master/README_DataGeneration.md

# Paired Read Simulation
In the "Selected" directory, run the following commands. To change the size of the data generated, change the input to the -n flag. 5000 will generate 45 MB of read data. 

```bash
for group in bacteria; do
  for file in $group/*.fa; do
    ~/krakenstuff/mason2-2.0.9-Linux-x86_64/bin/mason_simulator -ir $file --seed 42 -n 5000 --num-threads 4 \
      -o $group/$(basename $file .fa)_1.fq \
      -or $group/$(basename $file .fa)_2.fq \
      --read-name-prefix taxid_$(basename $file .fa).
  done
done
cd bacteria
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
