# Phylogenetic Biology - Final Project
___

# Abstract

The primary goal of these projects is to apply the knowledge and tools acquired in phylogenetic biology to problems in ecology and evolution. This project has 2 parts, both centered in the use of three topology for inferring evolutionary relationship among and between taxa, these parts differ in the scale of evolutionary relationship of interest and the type of information used for the inference. 

Part 1, titled "phylogenetic reconstruction of demographic structure in admixed populations", uses phylogenetic methods to unravel demographic signals in a complete chromosome compared to individual genes within the chromosome. This part looks at short evolutionary scales and its primarily intersted in the topology leading to the population members as the tips of the phylogeny. DNA sequences were used for this part of the project, and at its core, it is comparison of "chromosomal/concatenated locus trees" vs. "gene trees".     

**"Phylogenetic reconstruction of demographic structure in introgressed populations"**

## Introduction and Goals

Your favorite marine organism inhabits the eastern seaboard of some contient. Ecological conditions across the eastern seaboard of some continent are variable thus your favorite organism is exposed to a drastic range of ecological conditions. The demographic history of this organism is characterized by historical phylogenetic break occurring at the height of Middlecity (Figure 1). This phylogenetic break is likely due to a vicariance event occurring  recently in geologic time. 

![Figure 1](https://rawgit.com/Jcbnunez/phylobio_final_project/master/model_system.png "Figure 1 Ecology of your favorite Marine Organism")

*Figure 1: Left - A "map" of the sampled populations correlated with the dominant haplogroup. Right - a putative topology of a phylogeny including populations of the 2 major haplogroups and a sister taxa of your favorite marine organism.*    

This historical event has a very distinct demographic signal occurring in the chromosome K such that organism south of the break have a particular set of haplogroups and viceversa for the north. However, populations of your favorite marine organism living close to Middelcity town show a conspicuous introgression pattern such that about 50% of organisms living in this area show either a discrete northern or discrete southern haplogroup (Figure 2). In other words, demographic signals from chromosome K tend to split the middlecity populaiton in 2 groups that cluster with organisms living miles appart rather than reconstructing a single population even thought gene flow  among middlecity seems to be unrestricted.

This project will utilize phylogenetic reconstruction methods to unravel the demographic signal of chromosome K in a gene-by-gene fashion and investigate what gene(s) recapitulate the demographic signal.  

![Figure 2](https://rawgit.com/Jcbnunez/phylobio_final_project/master/cline_network_phylo.png "Figure 2 Maximun Parsimony Network with full chromosome haplotypes of  your favorite Marine Organism")

*Figure 2: a maximun parsimony haplotype network with individuals from all populations and the outgroup.*

## Methods
Data for this project corresponds to real anonymized sequencing data from various populations of *your favorite marine organism.* Sequencing data was assembles to high coverage and mapping quality standards to a reference sequence of chromosome K available in NCBI. Data was anonymized using scripts 1, 2, and 3 (see supplement). Monomorphic and singleton (parsimony non-informative) locus were filtered using TASSEL 5 (http://www.maizegenetics.net/#!tassel/c17q9). Nexus files were built in SEAVIEW (http://www.molecularevolution.org/software/alignment/seaview). As opposed to MESQUITE, SEAVIEW outputs revbayes ready NEXUS files.

Phylogenetic reconstructions were conducted in revbayes (rb and rb-mpi) version 1.00 (March 2016) using 2 independent parameter searches of 40,000 generations and a burn-in of 10,000 generations. For both types of runs, searches were conducted in independent nodes of the OSCAR computer cluster using the MPI version of revbayes (rb-mpi). A major advantage of RevBayes is its modular nature allowing phylogenetic reconstruction to be informed by different assumptions and models: rate matrix(figure 3), among site variation(figure 4), tree topology (Figure 5), and the phylogenetic MCMC model (Figure 6).

The original vision of this project was to compare the chromosomal tree with each individual gene tree in the molecule. However, given time constrains, this project will present a 'Lite' version of the original vision focusing on the comparison of the main phylogeny constructed using the complete chromosome K sequences with 3 genes (gene a6, Gene CI, gene BigGene) and 1 neutral marker. 

Phylogenetic visualizations were produced in R using ggtree(https://www.bioconductor.org/packages/3.3/bioc/vignettes/ggtree/inst/doc/ggtree.html) and ape(https://cran.r-project.org/web/packages/ape/index.html). Visualization of posterior probabilities were produced using ggplot2. For some clustering tests, ape phylogenetic objects were converted to dendrograms and analyzed using the R package dendextend (https://cran.r-project.org/web/packages/dendextend/index.html). 

-- Many clustering analyses were conducting under the assumptions that ultrametric phylogenetic trees, like dendrograms, can be seen as sets of nested lists possessing particular attributes. These clustering analyses mainly investigated the similarity between trees inferred using the entirety of the data set vs. trees inferred using only subsets of the data (chromosome trees vs. locus trees). These methods consist of: a) Tanglegrams, a comparison method that investigates the similarities in hierarchical clustering between the locus and chromosomal trees.  Tanglegrams allows for the estimation of "entanglement", a parameter which indicates the degree in which the branches and tips retain order between trees (lower entanglement suggests highest conservation of structure). Since tree topology can be represented in multiple ways, the relationship between two trees may not be entirely intuitive at face value. Built in algorithms in dendextend were used to "disentangle" tree visualization such that both topologies are visualized to the highest degree of similarity. Tree similarity was compared quantitively using two metrics, Baker's Gamma Index (Baker, 1974) measures the degree of association between two sets of hierarchical clusters (trees in this case), and The Fowlkes-Mallows Index (Fowlkes 1983). Statistical confidence for the Baker's Gamma Index was estimated using a permutation test (here done with 1000 repetitions) as follows:

```{r}

set.seed(123456) #setting a random seed
K_cor <- cor_bakers_gamma(k_dend, k_dend) #estimating the baker's index  between two indetical trees from the chromosome K. This set a null expectation.
K_BG_cor <- cor_bakers_gamma(k_dend,BG_dend) #estimating the baker's index between a gene tree and a chromosome tree. Here trees have been transformed to dendrograms (input trees were ultrametic). 

rep = 1000 #Number of iterations

cor_bakers_gamma_results <- numeric(rep) #Generating vector to store results
dend_mixed <- k_dend

#sampling loop
for(i in 1:rep) {
   dend_mixed <- sample.dendrogram(dend_mixed, replace = FALSE)
   cor_bakers_gamma_results[i] <- cor_bakers_gamma(k_dend, dend_mixed)
}

#P-value was estimated from the sampling distribution

round(sum(K_BG_cor < cor_bakers_gamma_results)/ rep, 4)


```




#####Figure 3: Rate matrix
![Figure 3](https://rawgit.com/Jcbnunez/phylobio_final_project/master/rate_matrix.png "Figure 3 Rate Matrix")
___

#####Figure 4: Site Variation
![Figure 4](https://rawgit.com/Jcbnunez/phylobio_final_project/master/among_sites_rate.png "Figure 4 Site Variation")
___

#####Figure 5: Topology
![Figure 5](https://rawgit.com/Jcbnunez/phylobio_final_project/master/Phylogeny_topology.png "Figure 4 Site Variation")
___

#####Figure 6: phyloMCMC
![Figure 5](https://rawgit.com/Jcbnunez/phylobio_final_project/master/phyloMCMC.png "Figure 4 Site Variation")
   

## Results

##**Phylogenetic Inference Quality Control**

**Chromosomal Tree**
Two runs of phylogenetic inferences were conducted with the entire chromosomal sequence in RevBayes. Only one of the two runs showed parameter convergence (Mean of Posterior Distribution = -11907.167; ESS = 922). Other quality measures of the inference are also explored: tree space uncertainty of the best tree in the tree space is shown in figure 7 (Due to time and computational constrains, only 10% -4000 randomly sampled trees- of the treespace is shown). Posterior probability search and probability distribution are shown in figure 8. 

![Figure 7](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_tree_space.png "Figure 7: Tree Space with branch length for chromosome K")
*Figure 7: Light blue, 10% of the tree space constructed for chromosome K. Red, Best tree.

![Figure 8](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_posterior_QC1.png "Figure 8: Chromosome K Quality Control of Parameter Convergence")
*Figure 8: Parameter searches conducted by revBayes during phylogenetic inference of chromosome K. Left: PDF of sampled posteriors. The blue vertical line represent the mean of the sampling distribution. Right: Posterior sampling per state (i.e. *per* generations)*


**Gene Tree A6:** Two independent runs were conducted with the sequence of gene A. Tree space uncertainty and and best tree are shown in figure 9. For both runs, parameter searches converged to similar posterior distributions (Distribution Mean 1 = -1695.242, ESS = 992, Distribution Mean 2 = -1695.473, ESS = 739). Distribution and parameter search *per* state are shown in figure 10. 

![Figure 9](https://rawgit.com/Jcbnunez/phylobio_final_project/master/a_treespace.png "Figure 9: Tree Space with branch lenght ")
*Figure 9: Light blue, 10% of the tree space constructed for chromosome K. Red, Best tree. 


![Figure 10](https://rawgit.com/Jcbnunez/phylobio_final_project/master/A_posterior_QC2.png "Figure 10: Gene A6 Quality Control of Parameter Convergence")
*Figure 10: Parameter searches conducted by revBayes during phylogenetic inference for gene a6. Left: PDF of sampled posteriors. The vertical lines represent the mean of the sampling distribution. The color scheme is blue and red for first and second independent run respectively. Right: Posterior sampling per state (i.e. *per* generations.) Color scheme is the same as the sampling distributions*

**Gene Tree CI:** Two independent runs were conducted with the sequence of gene CI. Tree space uncertainty and and best tree are shown in figure 11. For both runs, parameter searches converged to similar posterior distributions, the effective sample size of these searches was relatively small (Distribution Mean 1 = -3742.323, ESS = 115, Distribution Mean 2 = -3741.266, ESS = 182). Distribution and parameter search *per* state are shown in figure 12. 

![Figure 11](https://rawgit.com/Jcbnunez/phylobio_final_project/master/CI_treespace2.png "Figure 11: Tree Space with branch lenght ")
*Figure 11: Light blue, 10% of the tree space constructed for Gene CI. Red, Best tree. 


![Figure 12](https://rawgit.com/Jcbnunez/phylobio_final_project/master/CI_posterior_QC.png "Figure 12: Gene CI Quality Control of Parameter Convergence")
 *Figure 12: Parameter searches conducted by revBayes during phylogenetic inference for gene CI. Left: PDF of sampled posteriors. The vertical lines represent the mean of the sampling distribution. The color scheme is blue and red for first and second independent run respectively. Right: Posterior sampling per state (i.e. *per* generations.) Color scheme is the same as the sampling distributions*


**Gene Tree BigGene**

![Figure 13](https://rawgit.com/Jcbnunez/phylobio_final_project/master/BG_treespace2.png "Figure 13: Tree Space with branch lenght ")
*Figure 13: Light blue, 10% of the tree space constructed for the neutral marker. Red, Best tree. 

![Figure 14](https://rawgit.com/Jcbnunez/phylobio_final_project/master/BG_posterior_QC.png "Figure 14: Big Gene Quality Control of Parameter Convergence")
*Figure 14: Parameter searches conducted by revBayes during phylogenetic inference for the Big Gene. Left: PDF of sampled posteriors. The vertical lines represent the mean of the sampling distribution. The color scheme is blue and red for first and second independent run respectively. Right: Posterior sampling per state (i.e. *per* generations.) Color scheme is the same as the sampling distributions*


**Loci Tree Neutral Marker**

![Figure 15](https://rawgit.com/Jcbnunez/phylobio_final_project/master/NM_treespace.png "Figure 15: Tree Space with branch lenght ")
*Figure 15: Light blue, 10% of the tree space constructed for the neutral marker. Red, Best tree. 

![Figure 16](https://rawgit.com/Jcbnunez/phylobio_final_project/master/NM_posterior_QC.png "Figure 16: Loci NM Quality Control of Parameter Convergence")
*Figure 16: Parameter searches conducted by revBayes during phylogenetic inference for loci CI. Left: PDF of sampled posteriors. The vertical lines represent the mean of the sampling distribution. The color scheme is blue and red for first and second independent run respectively. Right: Posterior sampling per state (i.e. *per* generations.) Color scheme is the same as the sampling distributions*

##**Phylogenetic Signal**

and thus was used as the phylogeny for the chromosome.  The tree shows the split of the northern and Southern clades (Posterior probability = 0.92). As expected from the network analysis, individuals from the introgression zone clustered within either of the clades.      

![Figure 17](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_phylogeny.png "Figure 17: phylogeny for chromosome K")
**Figure 17:** Left: Phylogenetic reconstruction of of populations of your favorite organism. Branch leading to the split of the norther and souther clades is indicated in gold, posterior probability of the branch is also shown. Right: Mulutiple sequence aligment (MSA) of chromosome K used in the phylogenetic reconstruction

###Phylogenetic Signal Confounded in low sequencing coverage and/or bad loci calls

**Sequencing Coverage Across the tips**
![Figure 17B](https://rawgit.com/Jcbnunez/phylobio_final_project/master/N_mapped_BR.png "Figure 17B: phylogeny for gene BG")
Blomberg's K = 0.01374608

**Sequencing Coverage Across the tips - with flat (br = 1) branch lengths**
![Figure 17B](https://rawgit.com/Jcbnunez/phylobio_final_project/master/N_mapped_noBR.png "Figure 17B: phylogeny for gene BG")
Blomberg's K = 0.2848437


####Gene Big Gene
![Figure 20](https://rawgit.com/Jcbnunez/phylobio_final_project/master/BG_phylogeny.png "Figure 20: phylogeny for gene BG")

![Figure 20A](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_BG_tangled.png "Figure 20A: tangled K vs BG")

**Pre-entanglement**
Untangled Tanglegram of Big Gene vs Chromosome K. In this state the entanglement coefficient is =  0.8978911.

![Figure 20B](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_BG_untangled.png "Figure 20B: untangled K vs BG")

**Post-entanglement**
Post-entanglement coefficient is = 0.0322881

![Figure 20B](https://rawgit.com/Jcbnunez/phylobio_final_project/master/Bakers_gamma_kvsBG.png "Figure 20B: untangled K vs BG")
**Baker's Gamma**


![Figure 20C](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_BG_BK.png "Figure 20C: untangled K vs BG")
**Fowlkes–Mallows index of "dissimilarity"**

####Gene A
![Figure 18](https://rawgit.com/Jcbnunez/phylobio_final_project/master/a6_phylo.png "Figure 18: phylogeny for gene A")

![Figure 18B](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_A_tangled.png "Figure 18B: phylogeny for gene A")
**Pre-entanglement**
Entanglement = 0.3317921

![Figure 18C](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_A6_untangled.png "Figure 18C: phylogeny for gene A")
**Post-entanglement**
Post-entanglement coefficient is = 0.1615409

![Figure 18D](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_A6_BK.png "Figure 18D: phylogeny for gene A")

####Gene CI
![Figure 19](https://rawgit.com/Jcbnunez/phylobio_final_project/master/CI_phylogeny.png "Figure 19: phylogeny for gene CI")

![Figure 20A](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_CI_entangled.png "Figure 20A: tangled K vs BG")
**Pre-entanglement**
Entanglement = 0.4619899

![Figure 20B](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_CI_untangled.png "Figure 20B: untangled K vs BG")
**Post-entanglement**
Post-entanglement coefficient is = 0.35123

![Figure 20C](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_CI_BK.png "Figure 20C: untangled K vs CI")
**Fowlkes–Mallows index of "dissimilarity"**


####Neutral Loci
![Figure 21](https://rawgit.com/Jcbnunez/phylobio_final_project/master/NM_phylo2.png "Figure 21: phylogeny for gene BG")

![Figure 21B](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_NM_tangled.png "Figure 21B: phylogeny for gene BG")
Entanglement = 0.3514943

![Figure 21C](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_NM_untangled.png "Figure 21C: phylogeny for gene BG")
Entanglement = 0.2572355

![Figure 21D](https://rawgit.com/Jcbnunez/phylobio_final_project/master/K_vs_NM_BK.png "Figure 21D: phylogeny for gene BG")
**Fowlkes–Mallows index of "dissimilarity"**

####Potential sequence length Bias of phylogenetic signal 





## Discussion

These results indicate...

The biggest difficulty in implementing these analyses was...

If I did these analyses again, I would...

## References

Baker, Frank B. "Stability of two hierarchical grouping techniques Case I: Sensitivity to data errors." Journal of the American Statistical Association 69.346 (1974): 440-445.

Fowlkes, Edward B., and Colin L. Mallows. "A method for comparing two hierarchical clusterings." Journal of the American statistical association 78.383 (1983): 553-569.

## Supplement 

###Unix Code

```{sh}
#Script 1 - Data wrangling in Unix

#Deposit all Fasta files obtained from MEROPS/NCBI/Sequencing in a single folder
# cd to folder containing Fasta sequences
#make sequences one line
mkdir ./1_continious_FASTAs
files=(*.fasta)
#echo ${files[@]}
for i in "${files[@]}"
do awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < $i > ./1_continious_FASTAs/$i.continuouslines.fasta
done


mkdir ./2_Linearized_Fastas
#Generarate array with original files
cd ./1_continious_FASTAs
files=(*.fasta)
echo ${files[@]}

#Linearizing Fasta
for i in "${files[@]}"
do paste -d "\t" <(awk 'NR%2==1' $i) <(awk 'NR%2==0' $i) > ../2_Linearized_Fastas/$i.linerized.txt
done
```

```{sh, eval - F}
#Script 3 - Reconstructing Fasta files from Anonymized files

files=(*.out.txt) #change to the output of files from Script 2 
for i in "${files[@]}"
# depending on input the ">" may not be needed
awk '{print ">"$1"\n"$2 }' $i > $i.fasta
done

```

###R Code

```{r, eval=FALSE}
#Script 2 - Annomizing Data in R

#!/usr/bin/env Rscript
#Script name = TabFastaIndexer.r
#This scrip aims at changeing the names of a tabulated/linerized fasta file for index names
#takes two arguments "args", [1] = infile, [2] = outfile

args = commandArgs(trailingOnly=TRUE)

# test if there is at least one argument: if not, return an error
if (length(args)==0) {
  stop("At least one argument must be supplied (input file).n", call.=FALSE)
} else if (length(args)==1) {
  # default output file
  args[2] = "out.txt"
}

##Script

DF = read.delim2(args[1], header = F)

DF$V1 = paste(args[1],1:nrow(DF), sep = "_")

write.table(DF, file = args[2], col.names = F, row.names = F, quote = F )

```

## RvBayes code

#### Heavy Run

```{r, eval = F}
#script 4
####################################################################################
#                                                                                  #
# RevBayes Script for CTMC phylogetic recpnstruction of DNA data from Chromosome K #
#		This is a "heavy" run - with 4 independent runs and 100k generations	   #
#                                                                                  #
####################################################################################

#######################
#  Imput NEXUS file   #
#######################

data <- readDiscreteCharacterData("annon_master1line.nxs")

# gather data from nexus format
n_species <- data.ntaxa()
taxa <- data.taxa()
n_branches <- 2 * n_species - 3

# move index
mi = 0

######################
# Substitution Model #
######################

#### GTR+G substitution model applied uniformly to all sites ###
er_prior <- v(1,1,1,1,1,1)
er ~ dnDirichlet(er_prior)
moves[++mi] = mvSimplexElementScale(er,weight=3)

pi_prior <- v(1,1,1,1) 
pi ~ dnDirichlet(pi_prior)
moves[++mi] = mvSimplexElementScale(pi,weight=2)

#### create a deterministic variable for the rate matrix ####
# General time reversible# 
Q := fnGTR(er,pi) 

#### monitor difference between empirical and estimated base frequencies ####
pi_empirical <- data.getEmpiricalBaseFrequencies()
# pi_diff := pi_empirical - pi

#############################
# Among Site Rate Variation #
#############################

alpha_prior <- 0.05
alpha ~ dnExponential( alpha_prior )
gamma_rates := fnDiscretizeGamma( alpha, alpha, 4, false )

# add moves the shape parameter
moves[++mi] = mvScale(alpha,weight=2)


##############
# Tree model #
##############

# Uniform topology from a birth death process
topology ~ dnUniformTopology(taxa=taxa)

# add topology Metropolis-Hastings moves
moves[++mi] = mvNNI(topology, weight=1.0)
moves[++mi] = mvSPR(topology, weight=1.0)

# create branch length vector and add moves
for (i in 1:n_branches) {
   br_lens[i] ~ dnExponential(10.0)
   moves[++mi] = mvScale(br_lens[i])
}

# add deterministic node to monitor tree length
TL := sum(br_lens)

# unite topology and branch length vector into phylogeny object
phylogeny := treeAssembly(topology, br_lens)


###################
# PhyloCTMC Model #
###################

# the sequence evolution model
seq ~ dnPhyloCTMC(tree=phylogeny, Q=Q, siteRates=gamma_rates, type="DNA")

# attach the data
seq.clamp(data)


##############
# CTMC Model #
##############

#Define model as a handle:
mymodel = model(Q)

#Create monitors:
monitors[1] = mnModel(filename="output/chrm4x_GTR_Gamma.log",printgen=10, separator = TAB)
monitors[2] = mnFile(filename="output/chrm4x_GTR_Gamma.trees",printgen=10, separator = TAB, phylogeny)
monitors[3] = mnScreen(printgen=1000, TL)

# the mcmc algorithm will run with 4 chains for 100,000 generations
mymcmc = mcmc(mymodel, monitors, moves, nruns=4)

# For the purpose of the project I will include the burnin in the output.
# mymcmc.burnin(generations=10000,tuningInterval=1000)
mymcmc.run(generations=100000)


# Analyze the tree output.
treetrace1 = readTreeTrace("output/chrm4x_run_1.trees", treetype="non-clock")
treetrace2 = readTreeTrace("output/chrm4x_run_2.trees", treetype="non-clock")
treetrace3 = readTreeTrace("output/chrm4x_run_3.trees", treetype="non-clock")
treetrace4 = readTreeTrace("output/chrm4x_run_4.trees", treetype="non-clock")
# and get the summary of the tree trace
#treetrace1.summarize()

#Map to a consensus tree
map_tree1 = mapTree(treetrace1,"output/chrm4x_run_1.tree", burnin=1000)
map_tree2 = mapTree(treetrace2,"output/chrm4x_run_2.tree", burnin=1000)
map_tree3 = mapTree(treetrace3,”output/chrm4x_run_3.tree", burnin=1000)
map_tree4 = mapTree(treetrace4,”output/chrm4x_run_4.tree", burnin=1000)

# terminate rb or rb-mpi
q()
```


#### Light Run

```{r, eval = F}
#Script 5
####################################################################################
#                                                                                  #
# RevBayes Script for CTMC phylogetic recpnstruction of DNA data from Chromosome K #
#		This is a "Light" run - with 2 independent runs and 40k generations	   	   #
#                                                                                  #
####################################################################################

#######################
#  Imput NEXUS file   #
#######################

data <- readDiscreteCharacterData("annon_master1line.nxs")

# gather data from nexus format
n_species <- data.ntaxa()
taxa <- data.taxa()
n_branches <- 2 * n_species - 3

# move index
mi = 0

######################
# Substitution Model #
######################

#### GTR+G substitution model applied uniformly to all sites ###
er_prior <- v(1,1,1,1,1,1)
er ~ dnDirichlet(er_prior)
moves[++mi] = mvSimplexElementScale(er,weight=3)

pi_prior <- v(1,1,1,1) 
pi ~ dnDirichlet(pi_prior)
moves[++mi] = mvSimplexElementScale(pi,weight=2)

#### create a deterministic variable for the rate matrix ####
# General time reversible# 
Q := fnGTR(er,pi) 

#### monitor difference between empirical and estimated base frequencies ####
pi_empirical <- data.getEmpiricalBaseFrequencies()
# pi_diff := pi_empirical - pi

#############################
# Among Site Rate Variation #
#############################

alpha_prior <- 0.05
alpha ~ dnExponential( alpha_prior )
gamma_rates := fnDiscretizeGamma( alpha, alpha, 4, false )

# add moves the shape parameter
moves[++mi] = mvScale(alpha,weight=2)


##############
# Tree model #
##############

# Uniform topology from a birth death process
topology ~ dnUniformTopology(taxa=taxa)

# add topology Metropolis-Hastings moves
moves[++mi] = mvNNI(topology, weight=1.0)
moves[++mi] = mvSPR(topology, weight=1.0)

# create branch length vector and add moves
for (i in 1:n_branches) {
   br_lens[i] ~ dnExponential(10.0)
   moves[++mi] = mvScale(br_lens[i])
}

# add deterministic node to monitor tree length
TL := sum(br_lens)

# unite topology and branch length vector into phylogeny object
phylogeny := treeAssembly(topology, br_lens)


###################
# PhyloCTMC Model #
###################

# the sequence evolution model
seq ~ dnPhyloCTMC(tree=phylogeny, Q=Q, siteRates=gamma_rates, type="DNA")

# attach the data
seq.clamp(data)


##############
# CTMC Model #
##############

#Define model as a handle:
mymodel = model(Q)

#Create monitors:
monitors[1] = mnModel(filename="output/chrm2x_GTR_Gamma.log",printgen=10, separator = TAB)
monitors[2] = mnFile(filename="output/chrm2x_GTR_Gamma.trees",printgen=10, separator = TAB, phylogeny)
monitors[3] = mnScreen(printgen=1000, TL)

# the mcmc algorithm will run with 4 chains for 100,000 generations
mymcmc = mcmc(mymodel, monitors, moves, nruns=2)

# burning on!
mymcmc.burnin(generations=10000,tuningInterval=1000)

mymcmc.run(generations=40000)


# Analyze the tree output.
treetrace1 = readTreeTrace("output/chrm2x_run_1.trees", treetype="non-clock")
treetrace2 = readTreeTrace("output/chrm2x_run_2.trees", treetype="non-clock")

# and get the summary of the tree trace
#treetrace1.summarize()

#Map to a consensus tree
map_tree1 = mapTree(treetrace1,"output/chrm2x_run_1.tree", burnin=1000)
map_tree2 = mapTree(treetrace2,"output/chrm2x_run_2.tree", burnin=1000)

# terminate rb or rb-mpi
q()
```
####Amino Acid Run

```{r, eval = F}
################################################################################
#																			   #
# 		RevBayes Script for CTMC phylogetic recpnstruction of AA data 		   #
#																			   #
################################################################################

#######################
# Reading in the Data #
#######################

###### This just defines a single model for all sites #######

### Read in sequence data for both genes

data <- readDiscreteCharacterData("AA_data.nex")

# Get some useful variables from the data. We need these later on.
n_species <- data.ntaxa()
taxa <- data.names()
n_branches <- 2 * n_species - 3

# set my move index
mi = 0



######################
# Substitution Model #
######################

#### specify the Jukes-Cantor substitution model applied uniformly to all sites ###
Q <- fnWAG() 



##############
# Tree model #
##############

# construct a variable for the tree drawn from a birth death process
topology ~ dnUniformTopology(taxa)

# add topology Metropolis-Hastings moves
moves[++mi] = mvNNI(topology, weight=1.0)
moves[++mi] = mvSPR(topology, weight=1.0)

# create branch length vector and add moves
for (i in 1:n_branches) {
   br_lens[i] ~ dnExponential(10.0)
   moves[++mi] = mvScale(br_lens[i])
}

# add deterministic node to monitor tree length
TL := sum(br_lens)

# unite topology and branch length vector into phylogeny object
phylogeny := treeAssembly(topology, br_lens)



###################
# PhyloCTMC Model #
###################

# the sequence evolution model
seq ~ dnPhyloCTMC(tree=phylogeny, Q=Q, type="AA")

# attach the data
seq.clamp(data)



#############
# The Model #
#############

# We define our model.
# We can use any node of our model as a handle, here we chose to use the rate matrix.
mymodel = model(Q)


monitors[1] = mnModel(filename="AA.log",printgen=10, separator = TAB)
monitors[2] = mnFile(filename="AA.trees",printgen=10, separator = TAB, phylogeny)
monitors[3] = mnScreen(printgen=1000, TL)

mymcmc = mcmc(mymodel, monitors, moves, nruns=2)

# burning on!
mymcmc.burnin(generations=10000,tuningInterval=1000)
mymcmc.run(generations=40000)



# Now, we will analyze the tree output.
# Let us start by reading in the tree trace
treetrace1 = readTreeTrace("AA_run_1.trees", treetype="non-clock")
treetrace2 = readTreeTrace("AA_run_2.trees", treetype="non-clock")
# and get the summary of the tree trace
#treetrace.summarize()

map_tree1 = mapTree(treetrace1,"AA_run_1.tree", burnin=1000)
map_tree2 = mapTree(treetrace2,"AA_run_2.tree", burnin=1000)

# quit RevBayes
q()
```