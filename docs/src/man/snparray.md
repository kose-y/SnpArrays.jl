
# SnpArray

`SnpArray` is an array of `Tuple{Bool,Bool}` and adopts the same coding as the [Plink binary format](http://pngu.mgh.harvard.edu/~purcell/plink/binary.shtml). If `A1` and `A2` are the two alleles, the coding rule is  

| Genotype | SnpArray |  
|:---:|:---:|  
| A1,A1 | (false,false) |  
| A1,A2 | (false,true) |  
| A2,A2 | (true,true) |  
| missing | (true,false) |  

The code `(true,false)` is reserved for missing genotype. Otherwise, the bit `true` represents one copy of allele `A2`. In a two-dimensional `SnpArray`, each row is a person and each column is a SNP.

For complete genotype data, for example, after imputation, consider using the [HaplotypeArray](@ref) type.

## Constructor

There are various ways to initialize a `SnpArray`.  

* `SnpArray` can be initialized from [Plink binary files](http://pngu.mgh.harvard.edu/~purcell/plink/binary.shtml), say the sample data set hapmap3:


```julia
;ls -al hapmap3.*
```

    -rw-r--r--  1 hzhou3  staff  1128171 Jul 12 04:45 hapmap3.bed
    -rw-r--r--  1 hzhou3  staff   388672 Jul  7 18:09 hapmap3.bim
    -rw-r--r--  1 hzhou3  staff     7136 Jul  7 18:09 hapmap3.fam
    -rw-r--r--  1 hzhou3  staff   332960 Jul  7 18:09 hapmap3.map



```julia
using SnpArrays
hapmap = SnpArray("hapmap3")
```




    324x13928 SnpArrays.SnpArray{2}:
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (false,true)  (false,true)      (false,true)  (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (false,true)  (true,true)    …  (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)   …  (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     ⋮                                         ⋱                           
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)



By default, the constructor figures out the number of individuals and SNPs from the `.bim` and `.fam` files.


```julia
# rows are people; columns are SNPs
people, snps = size(hapmap)
```




    (324,13928)



Alternatively, users can supply keyword arguments `people` and `snps` to the constructor. In this case only the `.bed` file needs to be present.


```julia
hapmap = SnpArray("hapmap3"; people = 324, snps = 13928)
```




    324x13928 SnpArrays.SnpArray{2}:
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (false,true)  (false,true)      (false,true)  (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (false,true)  (true,true)    …  (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)   …  (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     ⋮                                         ⋱                           
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)



Internally `SnpArray` stores data as `BitArray`s and consumes approximately the same amount of memory as the Plink `bed` file size.


```julia
# memory usage, bed file size
Base.summarysize(hapmap), filesize("hapmap3.bed")
```




    (1128256,1128171)



* `SnpArray` can be initialized from a matrix of A1 allele counts.


```julia
SnpArray(rand(0:2, 5, 3))
```




    5x3 SnpArrays.SnpArray{2}:
     (true,true)    (false,false)  (true,true)  
     (false,false)  (true,true)    (false,false)
     (false,true)   (true,true)    (false,true) 
     (false,true)   (true,true)    (false,true) 
     (false,false)  (false,true)   (false,true) 



* `SnpArray(m, n)` generates an m by n `SnpArray` of all A1 alleles.


```julia
s = SnpArray(5, 3)
```




    5x3 SnpArrays.SnpArray{2}:
     (false,false)  (false,false)  (false,false)
     (false,false)  (false,false)  (false,false)
     (false,false)  (false,false)  (false,false)
     (false,false)  (false,false)  (false,false)
     (false,false)  (false,false)  (false,false)



## Summary statistics

`summarize` function computes the following summary statistics of a `SnpArray`:  

* `maf`: minor allele frequencies, taking into account of missingness.  
* `minor_allele`: a `BitVector` indicating the minor allele for each SNP.   `minor_allele[j]==true` means A1 is the minor allele for SNP j; `minor_allele[j]==false` means A2 is the minor allele for SNP j.  
* `missings_by_snp`: number of missing genotypes for each snp.  
* `missings_by_person`: number of missing genotypes for each person.  


```julia
maf, minor_allele, missings_by_snp, missings_by_person = summarize(hapmap)
# minor allele frequencies
maf'
```




    1x13928 Array{Float64,2}:
     0.0  0.0776398  0.324074  0.191589  …  0.00154321  0.0417957  0.00617284




```julia
# total number of missing genotypes
sum(missings_by_snp), sum(missings_by_person)
```




    (11894,11894)




```julia
# proportion of missing genotypes
sum(missings_by_snp) / length(hapmap)
```




    0.0026356890108565393



## Filtering

In almost all analyses, SNPs and individuals with low genotyping success rates are ignored. This filtering step is an important tool for removing likely false positives from association testing, as genotyping failure often occurs preferentially in cases or controls, or is correlated with the quantitative trait. `filter(s, min_success_rate_per_snp, min_success_rate_per_person)` does filtering according to the specified success rates for SNPs and people. Default is 0.98 for both.


```julia
# filtering SNPs and people to have both success rates above 0.98
snp_idx, person_idx = filter(hapmap, 0.98, 0.98)
# summary statistics of the filtered SnpArray
_, _, missings_by_snp_filtered, missings_by_person_filtered = summarize(sub(hapmap, person_idx, snp_idx));
```


```julia
# minimum SNP genotyping success rate after filtering ≥ 0.98
1.0 - maximum(missings_by_snp_filtered) / length(missings_by_person_filtered)
```




    0.9813084112149533




```julia
# minimum person genotyping success rate after filtering ≥ 0.98
1.0 - maximum(missings_by_person_filtered) / length(missings_by_snp_filtered)
```




    0.9818511796733213



## Random genotypes generation

`randgeno(a1freq)` generates a random genotype according to A1 allele frequency `a1freq`.


```julia
randgeno(0.5)
```




    (false,true)



`randgeno(maf, minor_allele)` generates a random genotype according to minor allele frequency `maf` and whether the minor allele is A1 (`minor_allele==true`) or A2 (`minor_allele==false`).


```julia
randgeno(0.25, true)
```




    (false,true)



`randgeno(n, maf, minor_allele)` generates a vector of random genotypes according to a common minor allele frequency `maf` and the minor allele.


```julia
randgeno(10, 0.25, true)
```




    10-element SnpArrays.SnpArray{1}:
     (true,true)  
     (false,false)
     (true,true)  
     (false,true) 
     (false,true) 
     (true,true)  
     (true,true)  
     (false,true) 
     (false,true) 
     (false,true) 



`randgeno(m, n, maf, minor_allele)` generates a random $m$-by-$n$ `SnpArray` according to a vector of minor allele frequencies `maf` and a minor allele indicator vector. The lengths of both vectors should be `n`.


```julia
# this is a random replicate of the hapmap data
randgeno(size(hapmap), maf, minor_allele)
```




    324x13928 SnpArrays.SnpArray{2}:
     (true,true)  (false,true)  (false,true)   …  (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)       (true,true)   (false,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true) 
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true) 
     (true,true)  (true,true)   (false,true)      (true,true)   (false,true)
     (true,true)  (true,true)   (true,true)    …  (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true) 
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)       (false,true)  (true,true) 
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true) 
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true) 
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true) 
     ⋮                                         ⋱                            
     (true,true)  (true,true)   (false,false)     (false,true)  (true,true) 
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true) 
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)    …  (true,true)   (true,true) 
     (true,true)  (false,true)  (true,true)       (true,true)   (true,true) 
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)    …  (true,true)   (true,true) 
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true) 
     (true,true)  (true,true)   (true,true)       (false,true)  (true,true) 
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true) 



## Subsetting

Subsetting a `SnpArray` works the same way as subsetting any other arrays.


```julia
# genotypes of the 1st person
hapmap[1, :]
```




    1x13928 SnpArrays.SnpArray{2}:
     (true,true)  (true,true)  (false,false)  …  (true,true)  (true,true)




```julia
# genotypes of the 5th SNP
hapmap[:, 5]
```




    324-element SnpArrays.SnpArray{1}:
     (true,true)  
     (true,true)  
     (false,true) 
     (false,true) 
     (true,true)  
     (false,false)
     (false,false)
     (true,true)  
     (true,true)  
     (true,true)  
     (true,true)  
     (true,true)  
     (false,true) 
     ⋮            
     (false,false)
     (true,true)  
     (false,true) 
     (true,true)  
     (true,true)  
     (true,true)  
     (true,true)  
     (true,true)  
     (false,true) 
     (true,true)  
     (true,true)  
     (true,true)  




```julia
# subsetting both persons and SNPs
hapmap[1:5, 5:10]
```




    5x6 SnpArrays.SnpArray{2}:
     (true,true)   (true,true)  (false,true)  …  (true,true)   (false,true)
     (true,true)   (true,true)  (true,true)      (true,true)   (false,true)
     (false,true)  (true,true)  (true,true)      (false,true)  (true,true) 
     (false,true)  (true,true)  (true,true)      (true,true)   (false,true)
     (true,true)   (true,true)  (true,true)      (true,true)   (false,true)




```julia
# filter out rare SNPs with MAF < 0.05
hapmap[:, maf .≥ 0.05]
```




    324x12085 SnpArrays.SnpArray{2}:
     (true,true)   (false,false)  (true,true)   …  (false,true)  (false,true)
     (false,true)  (false,true)   (false,true)     (true,true)   (true,true) 
     (true,true)   (false,true)   (false,true)     (true,true)   (true,true) 
     (true,true)   (false,true)   (true,true)      (false,true)  (false,true)
     (true,true)   (false,true)   (false,true)     (true,true)   (true,true) 
     (false,true)  (true,true)    (true,true)   …  (false,true)  (false,true)
     (true,true)   (true,true)    (true,true)      (true,true)   (true,true) 
     (true,true)   (false,false)  (true,true)      (true,true)   (true,true) 
     (true,true)   (false,true)   (false,true)     (true,true)   (true,true) 
     (true,true)   (false,true)   (true,true)      (false,true)  (false,true)
     (true,true)   (false,true)   (true,true)   …  (true,true)   (true,true) 
     (true,true)   (true,true)    (false,true)     (false,true)  (false,true)
     (true,true)   (false,false)  (true,true)      (false,true)  (false,true)
     ⋮                                          ⋱                            
     (true,true)   (false,true)   (true,true)      (false,true)  (false,true)
     (true,true)   (false,false)  (true,true)      (false,true)  (false,true)
     (true,true)   (false,false)  (true,true)      (true,true)   (true,true) 
     (true,true)   (false,false)  (false,true)  …  (true,true)   (true,true) 
     (true,true)   (false,true)   (true,true)      (true,true)   (true,true) 
     (true,true)   (true,true)    (false,true)     (false,true)  (false,true)
     (true,true)   (false,true)   (false,true)     (false,true)  (false,true)
     (true,true)   (false,true)   (true,true)      (true,true)   (true,true) 
     (true,true)   (false,false)  (true,true)   …  (false,true)  (false,true)
     (true,true)   (false,true)   (false,true)     (false,true)  (false,true)
     (true,true)   (false,false)  (false,true)     (false,true)  (false,true)
     (true,true)   (false,false)  (true,true)      (true,true)   (true,true) 




```julia
# filter out individuals with genotyping success rate < 0.90
hapmap[missings_by_person / people .< 0.1, :]
```




    220x13928 SnpArrays.SnpArray{2}:
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (false,true)  (false,true)      (false,true)  (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)   …  (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (false,true)  (false,true)   …  (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     (true,true)  (true,true)   (true,true)       (true,true)   (true,true)
     ⋮                                         ⋱                           
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)  …  (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)   …  (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,true)      (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)
     (true,true)  (true,true)   (false,false)     (true,true)   (true,true)



`sub()` and `slice()` create views of subarray without copying data and improve efficiency in many calculations.


```julia
mafcommon, = summarize(sub(hapmap, :, maf .≥ 0.05))
mafcommon'
```




    1x12085 Array{Float64,2}:
     0.0776398  0.324074  0.191589  …  0.310937  0.23913  0.23913  0.23913



## Assignment

It is possible to assign specific genotypes to a `SnpArray` entry.


```julia
hapmap[1, 1]
```




    (true,true)




```julia
hapmap[1, 1] = (false, true)
hapmap[1, 1]
```




    (false,true)




```julia
hapmap[1, 1] = NaN
hapmap[1, 1]
```




    (true,false)




```julia
hapmap[1, 1] = 2
hapmap[1, 1]
```




    (true,true)



Subsetted assignment such as `hapmap[:, 1] = Nan` is also valid.

## Convert, copy and imputation

In most analyses we convert a whole `SnpArray` or slices of it to numeric arrays (matrix of **minor allele counts**) for statistical analysis. Keep in mind that the storage of resultant data can be up to 32 fold larger than that of the original `SnpArray`. Fortunately, rich collection of data types in `Julia` allow us choose one that fits into memory. Below are estimates of memory usage for some common data types with `n` persons and `p` SNPs. Here MAF denotes the **average** minor allele frequencies.

* `SnpArray`: $0.25np$ bytes  
* `Matrix{Int8}`: $np$ bytes  
* `Matrix{Float16}`: $2np$ bytes  
* `Matrix{Float32}`: $4np$ bytes  
* `Matrix{Float64}`: $8np$ bytes  
* `SparseMatrixCSC{Float64,Int64}`: $16 \cdot \text{NNZ} + 8(p+1) \approx 16np(2\text{MAF}(1-\text{MAF})+\text{MAF}^2) + 8(p+1) = 16np \cdot \text{MAF}(2-\text{MAF}) + 8(p+1)$ bytes. When the average MAF=0.25, this is about $7np$ bytes. When MAF=0.025, this is about $0.8np$ bypes, 10 fold smaller than the `Matrix{Float64}` type.  
* `SparseMatrixCSC{Int8,UInt32}`: $5 \cdot \text{NNZ} + 4(p+1) \approx 5np(2\text{MAF}(1-\text{MAF})+\text{MAF}^2) + 4(p+1) = 5np \cdot \text{MAF}(2-\text{MAF}) + 4(p+1)$ bytes. When the average MAF=0.25, this is about $2.2np$ bytes. When MAF=0.08, this is about $0.8np$ bypes, 10 fold smaller than `Matrix{Float64}` type.  
* Two `SparseMatrixCSC{Bool,Int64}`: $2np \cdot \text{MAF} \cdot 9 + 16(p+1) = 18 np \cdot \text{MAF} + 16(p+1)$ bytes. When the average MAF=0.25, this is about $4.5np$ bytes. When MAF=0.045, this is about $0.8np$ bytes, 10 fold smaller than `Matrix{Float64}` type.  

To be concrete, consider 2 typical data sets:  
* COPD (GWAS): $n = 6670$ individuals, $p = 630998$ SNPs, average MAF is 0.2454.
* GAW19 (sequencing study): $n = 959$ individuals, $p = 8348674$ SNPs, average MAF is 0.085.  

| Data Type | COPD | GAW19 |  
|---|---:|---:|  
| `SnpArray` | 1.05GB | 2GB |  
| `Matrix{Float64}` | 33.67GB | 64.05GB |  
| `SparseMatrixCSC{Float64,Int64}` | 29GB | 20.82GB |  
| `SparseMatrixCSC{Bool,Int64}` | 18.6GB | 12.386GB |  

Apparently for data sets with a majority of rare variants, converting to sparse matrices saves memory and often brings computational advantages too. In the `SparseMatrixCSC` format, the integer type of the row indices `rowval` and column pointer `colptr` should have maximal allowable value larger than the number of nonzeros in the matrix. The `InexactError()` error encountered during conversion often indicates that the integer type has a too small range. The utility function `estimatesize` conveniently estimates memory usage in bytes for the input data type.


```julia
# estimated memory usage if convert to Matrix{Float64}
estimatesize(people, snps, Matrix{Float64})
```




    3.6101376e7




```julia
# convert to Matrix{Float64}
hapmapf64 = convert(Matrix{Float64}, hapmap)
```




    324x13928 Array{Float64,2}:
     0.0  0.0  2.0  0.0  0.0  0.0  1.0  1.0  …  1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  1.0  1.0  1.0  0.0  0.0  0.0  1.0     0.0  0.0  0.0  0.0  0.0  1.0  0.0
     0.0  0.0  1.0  1.0  1.0  0.0  0.0  2.0     1.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  1.0  0.0  1.0  0.0  0.0  1.0     1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  1.0  1.0  0.0  0.0  0.0  2.0     0.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  1.0  0.0  0.0  2.0  0.0  0.0  0.0  …  2.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  0.0  0.0  2.0  0.0  0.0  2.0     1.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  2.0  0.0  0.0  0.0  0.0  1.0     0.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  1.0  1.0  0.0  0.0  0.0  2.0     0.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  1.0  0.0  0.0  0.0  0.0  2.0     1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  1.0  0.0  0.0  0.0  0.0  2.0  …  0.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  0.0  1.0  0.0  0.0  0.0  2.0     1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  2.0  0.0  1.0  0.0  0.0  1.0     2.0  1.0  1.0  1.0  0.0  0.0  0.0
     ⋮                        ⋮              ⋱                      ⋮            
     0.0  0.0  1.0  0.0  2.0  0.0  0.0  1.0     2.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  2.0  0.0  0.0  0.0  0.0  2.0     2.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  2.0  0.0  1.0  0.0  0.0  1.0     0.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  2.0  1.0  0.0  0.0  0.0  1.0  …  0.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  1.0  0.0  0.0  0.0  0.0  1.0     1.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  0.0  1.0  0.0  0.0  0.0  1.0     2.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  1.0  1.0  0.0  0.0  0.0  2.0     1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  1.0  0.0  0.0  0.0  0.0  2.0     0.0  0.0  0.0  0.0  0.0  0.0  0.0
     0.0  0.0  2.0  0.0  1.0  0.0  0.0  1.0  …  1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  1.0  1.0  0.0  0.0  0.0  1.0     1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  2.0  1.0  0.0  0.0  0.0  2.0     1.0  1.0  1.0  1.0  0.0  0.0  0.0
     0.0  0.0  2.0  0.0  0.0  0.0  0.0  2.0     0.0  0.0  0.0  0.0  0.0  0.0  0.0




```julia
# actual memory usage of Matrix{Float64}
Base.summarysize(hapmapf64)
```




    36101376




```julia
# average maf of the hapmap3 data set
mean(maf)
```




    0.222585591341583




```julia
# estimated memory usage if convert to SparseMatrixCSC{Float32, UInt32} matrix
estimatesize(people, snps, SparseMatrixCSC{Float32, UInt32}, mean(maf))
```




    1.4338389205819245e7




```julia
# convert to SparseMatrixCSC{Float32, UInt32} matrix
hapmapf32sp = convert(SparseMatrixCSC{Float32, UInt32}, hapmap)
```




    324x13928 sparse matrix with 1614876 Float32 entries:
    	[2    ,     2]  =  1.0
    	[6    ,     2]  =  1.0
    	[15   ,     2]  =  1.0
    	[31   ,     2]  =  1.0
    	[33   ,     2]  =  1.0
    	[35   ,     2]  =  1.0
    	[43   ,     2]  =  1.0
    	[44   ,     2]  =  1.0
    	[50   ,     2]  =  1.0
    	[54   ,     2]  =  1.0
    	⋮
    	[135  , 13927]  =  1.0
    	[148  , 13927]  =  1.0
    	[160  , 13927]  =  1.0
    	[164  , 13927]  =  2.0
    	[167  , 13927]  =  1.0
    	[185  , 13927]  =  1.0
    	[266  , 13927]  =  1.0
    	[280  , 13927]  =  1.0
    	[288  , 13927]  =  1.0
    	[118  , 13928]  =  2.0
    	[231  , 13928]  =  2.0




```julia
# actual memory usage if convert to SparseMatrixCSC{Float32, UInt32} matrix
Base.summarysize(hapmapf32sp)
```




    12974764



By default the `convert()` method converts missing genotypes to `NaN`.


```julia
# number of missing genotypes
countnz(isnan(hapmap)), countnz(isnan(hapmapf64))
```




    (11894,11894)



One can enforce **crude imputation** by setting the optional argument `impute=true`. Imputation is done by generating two random alleles according to the minor allele frequency. This is a neutral but not an optimal strategy, and users should impute missing genotypes by more advanced methods.


```julia
hapmapf64impute = convert(Matrix{Float64}, hapmap; impute = true)
countnz(isnan(hapmapf64impute))
```




    0



By default `convert()` translates genotypes according to the *additive* SNP model, which essentially counts the number of **minor allele** (0, 1 or 2) per genotype. Other SNP models are *dominant* and *recessive*, both in terms of the **minor allele**. When `A1` is the minor allele, genotypes are translated to real number according to

| Genotype | `SnpArray` | `model=:additive` | `model=:dominant` | `model=:recessive` |    
|:---:|:---:|:---:|:---:|:---:|  
| A1,A1 | (false,false) | 2 | 1 | 1 |  
| A1,A2 | (false,true) | 1 | 1 | 0 |  
| A2,A2 | (true,true) | 0 | 0 | 0 |  
| missing | (true,false) | NaN | NaN | NaN | 

When `A2` is the minor allele, genotypes are translated according to

| Genotype | `SnpArray` | `model=:additive` | `model=:dominant` | `model=:recessive` |    
|:---:|:---:|:---:|:---:|:---:|  
| A1,A1 | (false,false) | 0 | 0 | 0 |  
| A1,A2 | (false,true) | 1 | 1 | 0 |  
| A2,A2 | (true,true) | 2 | 1 | 1 |  
| missing | (true,false) | NaN | NaN | NaN |


```julia
[convert(Vector{Float64}, hapmap[1:10, 5]; model = :additive) convert(Vector{Float64}, hapmap[1:10, 5]; model = :dominant) convert(Vector{Float64}, hapmap[1:10, 5]; model = :recessive)]
```




    10x3 Array{Float64,2}:
     0.0  0.0  0.0
     0.0  0.0  0.0
     1.0  1.0  0.0
     1.0  1.0  0.0
     0.0  0.0  0.0
     2.0  1.0  1.0
     2.0  1.0  1.0
     0.0  0.0  0.0
     0.0  0.0  0.0
     0.0  0.0  0.0



By default `convert()` does **not** center and scale genotypes. Setting the optional arguments `center=true, scale=true` centers genotypes at 2MAF and scales them by $[2 \cdot \text{MAF} \cdot (1 - \text{MAF})]^{-1/2}$. Mono-allelic SNPs (MAF=0) are not scaled.


```julia
[convert(Vector{Float64}, hapmap[:, 5]) convert(Vector{Float64}, hapmap[:, 5]; center = true, scale = true)]
```




    324x2 Array{Float64,2}:
     0.0  -1.25702 
     0.0  -1.25702 
     1.0   0.167017
     1.0   0.167017
     0.0  -1.25702 
     2.0   1.59106 
     2.0   1.59106 
     0.0  -1.25702 
     0.0  -1.25702 
     0.0  -1.25702 
     0.0  -1.25702 
     0.0  -1.25702 
     1.0   0.167017
     ⋮             
     2.0   1.59106 
     0.0  -1.25702 
     1.0   0.167017
     0.0  -1.25702 
     0.0  -1.25702 
     0.0  -1.25702 
     0.0  -1.25702 
     0.0  -1.25702 
     1.0   0.167017
     0.0  -1.25702 
     0.0  -1.25702 
     0.0  -1.25702 



`copy!()` is the in-place version of `convert()`. Options such as GWAS loop over SNPs and perform statistical anlaysis for each SNP. This can be achieved by


```julia
g = zeros(people)
for j = 1:snps
    copy!(g, hapmap[:, j]; model = :additive, impute = true)
    # do statistical anlaysis
end
```

## Empirical kinship matrix

`grm` function computes the empirical kinship matrix using either the genetic relationship matrix, `grm(A, model=:GRM)`, or the method of moment method, `grm(A, model=:MoM)`. 

!!! note

    Missing genotypes are imputed according to minor allele frequencies on the fly.  
    



```julia
# GRM using all SNPs
grm(hapmap)
```




    324x324 Array{Float64,2}:
     0.566524   0.04431    0.0187306  …  0.0622867  0.0687052  0.0623734
     0.04431    0.530373   0.0312323     0.0497046  0.0432528  0.0608642
     0.0187306  0.0312323  0.510791      0.045015   0.0291232  0.0351878
     0.0463994  0.0356446  0.0277324     0.0574487  0.0632991  0.0576694
     0.0504432  0.0418174  0.0246636     0.0689248  0.0557189  0.0632692
     0.0427006  0.0306623  0.0373823  …  0.0679383  0.0547188  0.0627049
     0.0381117  0.0215367  0.0117409     0.042681   0.0366743  0.0357035
     0.0395397  0.0369642  0.0206019     0.0555926  0.0526215  0.0637111
     0.028737   0.0295557  0.0160576     0.0320136  0.044383   0.0362744
     0.0379938  0.0407265  0.0256978     0.0643465  0.0554608  0.0471915
     0.0458165  0.0440068  0.0224789  …  0.0561538  0.0642461  0.0584295
     0.0578648  0.0377148  0.0357752     0.06352    0.0564101  0.0692569
     0.0347068  0.0423464  0.025328      0.055259   0.067383   0.0614259
     ⋮                                ⋱                                 
     0.063317   0.0534229  0.0368553     0.0832486  0.0831328  0.093519 
     0.064809   0.0501189  0.0321454     0.0885677  0.0768979  0.0853617
     0.0651386  0.053962   0.0370123     0.086435   0.080916   0.0819761
     0.0627899  0.0550938  0.0380069  …  0.0841653  0.0736459  0.0858754
     0.0604787  0.0534449  0.0351682     0.0757659  0.0705576  0.0785646
     0.0623749  0.0582096  0.0381049     0.0852801  0.0884385  0.0794518
     0.0603683  0.0588081  0.0370305     0.079761   0.0814714  0.0782544
     0.0598038  0.0604263  0.0320275     0.0893622  0.086034   0.0825794
     0.0634584  0.0608424  0.0315355  …  0.0936972  0.0763907  0.0831321
     0.0622867  0.0497046  0.045015      0.606873   0.0790143  0.0867932
     0.0687052  0.0432528  0.0291232     0.0790143  0.583542   0.0794274
     0.0623734  0.0608642  0.0351878     0.0867932  0.0794274  0.575616 




```julia
# GRM using every other SNP
grm(sub(hapmap, :, 1:2:snps))
```




    324x324 Array{Float64,2}:
     0.555485   0.0414584  0.0265599  …  0.0649969  0.0706832  0.0651222
     0.0414584  0.545477   0.035331      0.0558445  0.0440697  0.0535371
     0.0265599  0.035331   0.500768      0.0383132  0.0373413  0.0454816
     0.0431908  0.0442509  0.0250957     0.0492826  0.0589603  0.0540867
     0.0504474  0.0467705  0.0245719     0.0650241  0.0548574  0.0630899
     0.0506486  0.0391991  0.0386068  …  0.074283   0.0601437  0.0509112
     0.0379701  0.0264693  0.0159633     0.0453982  0.0331826  0.032985 
     0.0455662  0.0374818  0.0254581     0.0569612  0.053878   0.0662448
     0.0253964  0.0223143  0.0189219     0.0319043  0.0419019  0.0344454
     0.0303679  0.0390143  0.0231932     0.0575742  0.0397998  0.0488691
     0.0471279  0.0490411  0.0198069  …  0.0605351  0.0628944  0.0511443
     0.059658   0.0481027  0.039431      0.0636518  0.0620679  0.062151 
     0.0239542  0.0417324  0.0211919     0.0526874  0.0664469  0.0609897
     ⋮                                ⋱                                 
     0.0583416  0.0491196  0.0389251     0.0840045  0.0822214  0.0978664
     0.0636886  0.0582445  0.0290102     0.0863183  0.0732212  0.0837279
     0.0666562  0.0518503  0.0369456     0.0814162  0.0784215  0.0923037
     0.0629366  0.0560442  0.0389387  …  0.0903268  0.0748442  0.0794667
     0.0612991  0.059333   0.0395798     0.0704755  0.0668915  0.0842222
     0.0634261  0.0612304  0.0394953     0.0734993  0.0792671  0.086164 
     0.0611508  0.0621148  0.0306512     0.0831387  0.0885752  0.0803842
     0.0579779  0.0592217  0.0333789     0.0913713  0.0884812  0.0801725
     0.0735156  0.0639219  0.0384092  …  0.0981748  0.0720091  0.086251 
     0.0649969  0.0558445  0.0383132     0.5992     0.0671005  0.0924877
     0.0706832  0.0440697  0.0373413     0.0671005  0.561654   0.071662 
     0.0651222  0.0535371  0.0454816     0.0924877  0.071662   0.576829 




```julia
# MoM using all SNPs
grm(hapmap; method = :MoM)
```




    324x324 Array{Float64,2}:
     0.539214    0.0347339  0.00320397  …  0.0536283  0.0633117  0.0506761
     0.0347339   0.518076   0.0148949      0.0422917  0.0392214  0.0502037
     0.00320397  0.0148949  0.500008       0.033435   0.0212717  0.0206813
     0.0430002   0.0288295  0.0238697      0.0519751  0.0686257  0.049259 
     0.0450078   0.0330807  0.0167843      0.0655554  0.0566986  0.0565806
     0.0323721   0.0211536  0.0271762   …  0.0598871  0.0493771  0.0497313
     0.0249325   0.0109979  0.00332206     0.0316636  0.030837   0.0248144
     0.0261134   0.0287114  0.0104074      0.0386309  0.0460706  0.0467791
     0.0206813   0.025641   0.0143044      0.0297742  0.0484324  0.0304827
     0.0212717   0.0254048  0.0114703      0.0530379  0.0509122  0.0324902
     0.0360329   0.0328445  0.00662857  …  0.0497313  0.0557539  0.0502037
     0.0484324   0.0322541  0.0281209      0.0556358  0.0552816  0.0525655
     0.0314274   0.0418193  0.0255229      0.0587062  0.0705152  0.0578795
     ⋮                                  ⋱                                 
     0.0420555   0.0417012  0.0267038      0.0713418  0.0792538  0.0816156
     0.0620127   0.0481962  0.0276486      0.0918894  0.0788995  0.0833869
     0.0548092   0.0454801  0.0264677      0.0806709  0.0811432  0.0727589
     0.0447716   0.0421736  0.0246963   …  0.0722865  0.0621308  0.0698066
     0.0564625   0.0533921  0.0294199      0.0774825  0.0703971  0.074294 
     0.0524474   0.0542188  0.0314274      0.0788995  0.0858668  0.071578 
     0.0523293   0.050558   0.0290656      0.0778367  0.0826784  0.0734674
     0.0483143   0.0528017  0.0210355      0.0824422  0.083505   0.0719322
     0.0507942   0.0484324  0.0228069   …  0.0885829  0.0738217  0.0693343
     0.0536283   0.0422917  0.033435       0.561533   0.0805528  0.0760654
     0.0633117   0.0392214  0.0212717      0.0805528  0.566492   0.06898  
     0.0506761   0.0502037  0.0206813      0.0760654  0.06898    0.533309 



## Principal components 

Principal compoenent analysis is widely used in genome-wide association analysis (GWAS) for adjusting population substructure. `pca(A, pcs)` computes the top `pcs` principal components of a `SnpArray`. Each SNP is centered at $2\text{MAF}$ and scaled by $[2\text{MAF}(1-\text{MAF})]^{-1/2}$. The output is  

* `pcscore`: top `pcs` eigen-SNPs, or principal scores, in each column  
* `pcloading`: top `pcs` eigen-vectors, or principal loadings, in each column  
* `pcvariance`: top `pcs` eigen-values, or principal variances

Missing genotypes are imputed according the minor allele frequencies on the fly. This implies that, in the presence of missing genotypes, running the function on the same `SnpArray` twice may produce slightly different answers. For reproducibility, it is a good practice to set the random seed before each function that does imputation on the fly.


```julia
srand(123) # set seed
pcscore, pcloading, pcvariance = pca(hapmap, 3)
```




    (
    324x3 Array{Float64,2}:
     -38.7231  -1.2983     -7.00541  
     -32.6096  -1.21052    -3.3232   
     -23.0215  -0.505397   12.1751   
     -35.692   -2.76103    -2.40055  
     -37.1815  -0.132498   -3.66829  
     -34.9285  -1.11368     6.14167  
     -22.0323  -5.70536     2.02968  
     -30.9994  -2.28269    -0.0893283
     -22.8432  -3.76024     7.97486  
     -32.2024  -0.239253    2.91168  
     -36.344   -0.773184   -5.31525  
     -35.886   -0.807234    0.279053 
     -33.9423  -3.78982     7.35677  
       ⋮                             
     -49.1282   0.913683   10.4061   
     -46.9862  -0.9654     -0.435579 
     -48.5334  -1.05076    -0.15223  
     -49.0331   0.379279    5.65431  
     -47.8714  -0.406195   -7.14605  
     -48.2028  -1.41369    -0.564107 
     -46.7128  -3.36643    -4.44341  
     -48.9006  -1.69293     0.0467995
     -48.5574   1.34936    -1.89814  
     -50.2291   0.0865293  -1.94494  
     -48.9263  -2.06102     2.17374  
     -48.8627   0.274894    6.49518  ,
    
    13928x3 Array{Float64,2}:
      9.66817e-20   7.35949e-19   5.79015e-19
      0.00143962   -0.0042375    -0.00311816 
     -0.0183601    -0.00512036    0.00322409 
     -0.00956451   -0.004523     -0.00478078 
      0.0211999    -0.0226285     0.0110026  
     -1.82e-19     -1.35541e-18  -1.07856e-18
     -0.00230269   -0.000231224  -0.00339487 
     -0.0202126    -0.0025855     8.10915e-5 
      0.00631175   -0.0181213     0.00582407 
      0.000691273  -0.00158342   -0.0121338  
     -6.34042e-19  -3.71923e-18  -2.90818e-18
      0.0186933     7.92095e-5    0.00276918 
     -0.0127722     0.00765991    0.0134646  
      ⋮                                      
      0.000732677   0.000506129   0.00241864 
      0.000632772   0.000487763   0.00243887 
     -0.000604616  -0.000224069  -0.00294191 
      0.000769648   0.000534368   0.00250158 
      0.000410429   0.000371501   0.00266287 
     -0.00115497   -0.00172623    0.00106324 
      0.00051705    0.000728929   0.00249755 
      0.000652703   0.000748617   0.0023053  
      0.000643944  -0.000151043   0.00242307 
     -0.00149825   -0.000183435  -0.00454919 
      0.00313326   -0.00427486   -0.0152038  
     -9.09523e-5   -0.00287777    0.0037855  ,
    
    [1841.3950939952633,225.32365874997188,70.70835685208192])



To use eigen-SNPs for plotting or as covariates in GWAS, we typically scale them by their standard deviations so that they have mean zero and unit variance.


```julia
# standardize eigen-SNPs before plotting or GWAS
scale!(pcscore, 1.0 ./ √(pcvariance))
std(pcscore, 1)
```




    1x3 Array{Float64,2}:
     1.0  1.0  1.0



Internally `pca` converts `SnpArray` to the matrix of minor allele counts. The default format is `Matrix{Float64}`, which can easily exceed memory limit. Users have several options when the default `Matrix{Float64}` cannot fit into memory.  

* Use other intermediate matrix types.


```julia
# use single precision matrix and display the principal variances
# approximately same answer as double precision
srand(123)
pca(hapmap, 3, Matrix{Float32})[3]
```




    3-element Array{Float32,1}:
     1841.39  
      225.324 
       70.7085



* Use subset of SNPs


```julia
# principal components using every other SNP capture about half the variance
srand(123)
pca(sub(hapmap, :, 1:2:snps), 3)[3]
```




    3-element Array{Float64,1}:
     926.622 
     113.188 
      36.4866



* Use sparse matrix. For large data sets with majority of rare variants, `pca_sp` is more efficient by first converting `SnpArray` to a sparse matrix (default is `SparseMatrixCSC{Float64, Int64}`) and then computing principal components using iterative algorithms. 


```julia
# approximately same answer if we use Float16 sparse matrix
srand(123)
pca_sp(hapmap, 3, SparseMatrixCSC{Float16, UInt32})[3]
```




    3-element Array{Float64,1}:
     1841.4   
      225.31  
       70.7094




```julia
# approximately same answer if we use Int8 sparse matrix
srand(123)
pca_sp(hapmap, 3, SparseMatrixCSC{Int8, UInt32})[3]
```




    3-element Array{Float64,1}:
     1841.4   
      225.328 
       70.7119

