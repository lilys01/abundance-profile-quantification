# Launch Container with 64 GB RAM
```bash
launch-scipy-ml.sh -m 64 -c 8
```
# Paired Read Simulation
## 45 MB
```bash
for group in bacteria viruses; do
  for file in $group/*.fa; do
    ~/krakenstuff/mason2-2.0.9-Linux-x86_64/bin/mason_simulator -ir $file --seed 42 -n 5000 --num-threads 4 \
      -o $group/$(basename $file .fa)_1.fq \
      -or $group/$(basename $file .fa)_2.fq \
      --read-name-prefix taxid_$(basename $file .fa).
  done
done

cat *_1.fq > bacteria_very_small_1.fq
cat *_2.fq > bacteria_very_small_2.fq
```
## 452 MB
```bash
for group in bacteria viruses; do
  for file in $group/*.fa; do
    ~/krakenstuff/mason2-2.0.9-Linux-x86_64/bin/mason_simulator -ir $file --seed 42 -n 50000 --num-threads 4 \
      -o $group/$(basename $file .fa)_1.fq \
      -or $group/$(basename $file .fa)_2.fq \
      --read-name-prefix taxid_$(basename $file .fa).
  done
done

cat *_1.fq > bacteria_small_1.fq
cat *_2.fq > bacteria_small_2.fq
```
## 2 GB
```bash
for group in bacteria viruses; do
  for file in $group/*.fa; do
    ~/krakenstuff/mason2-2.0.9-Linux-x86_64/bin/mason_simulator -ir $file --seed 42 -n 500000 --num-threads 4 \
      -o $group/$(basename $file .fa)_1.fq \
      -or $group/$(basename $file .fa)_2.fq \
      --read-name-prefix taxid_$(basename $file .fa).
  done
done

cat *_1.fq > bacteria_1.fq
cat *_2.fq > bacteria_2.fq
```
## 6 GB
```bash
for group in bacteria viruses; do
  for file in $group/*.fa; do
    ~/krakenstuff/mason2-2.0.9-Linux-x86_64/bin/mason_simulator -ir $file --seed 42 -n 5000000 --num-threads 4 \
      -o $group/$(basename $file .fa)_1.fq \
      -or $group/$(basename $file .fa)_2.fq \
      --read-name-prefix taxid_$(basename $file .fa).
  done
done

cat *_1.fq > bacteria_large_1.fq
cat *_2.fq > bacteria_large_2.fq
```
# Database Creation
```bash
kraken2-2.0.8-beta/kraken2-build --download-taxonomy --use-ftp --threads 32 --db databases/strex
```
```bash
kraken2-2.0.8-beta/kraken2-build --db databases/strex  --no-masking  --threads 32 --add-to-library /datasets/kraken-public/krakenData/kraken2-experiment-code/strain_excluded.fna
```

# Classification
For each set of paired reads
```bash
kraken2-2.0.8-beta/kraken2 -db databases/16S_SILVA138_k2db --memory-mapping --threads 16 --paired --output results_small.out bacteria_very_small_1.fq bacteria_very_small_2.fq
```
# Profiling
```bash
gprof kraken2-2.0.8-beta/classify > analysis.txt
 ```
