---
Title: Data1.html
---
Chapter 1. Population genetics in R
====

Introduction
----

This primer provides a concise introduction to conducting applied analyses of population genetic data in R, with a special emphasis on non-model populations including clonal or partially clonal organisms. It is not meant to be a textbook on population genetics. Quite to the contrary, we refer the reader to several textbooks on population genetics (<a href="http://books.google.com/books?id=Iy08kgEACAAJ">Nielsen and  Slatkin, 2013</a>; <a href="http://books.google.com/books?id=tJVreQjInt0C">Templeton, 2006</a>; <a href="http://books.google.com/books?id=SB1vQgAACAAJ">Hartl and  Clark, 2007</a>). Likewise, this book will not replace books on theory and statistics of population genetics (<a href="http://books.google.com/books?id=e9QPAQAAMAAJ">Weir, 1996</a>). The reader is thus expected to have a basic understanding of population genetic theory and applications. Finally, this primer is focused on traditional population genetics based on allele frequencies, rather than more sophisticated coalescent approaches (<a href="http://books.google.com/books?id=x30RAgAACAAJ">Wakeley, 2009</a>; <a href="http://books.google.com/books?id=Iy08kgEACAAJ">Nielsen and  Slatkin, 2013</a>; <a href="http://books.google.com/books?id=QBC\_SFOamksC">Hein et al. 2004</a>), although some of these aspects will be covered here too. In a nutshell, this primer provides a valuable resource for tackling the nitty-gritty analysis of populations that do not necessarily conform to textbook genetics and might or might not be in Hardy-Weinberg equilibrium and panmixia. 

This primer is geared towards biologists intersted in analyzing their populations. This primer does not reuqire extensive knowledge of programming in R, but the user is expected to install R and all its dependent packages. 

Why R?
-----

Until recently, one of the more tedious aspects of conducting a population genetic analysis was the need for repeated reformatting data to conduct different, complimentary analyses in different programs. Often, these programs only ran on one platform. Now, R provides a toolbox with its packages that allows analysis of most data conveniently without tedious reformatting on all major computing platforms including Microsoft Windows, Linux, and Apple's OS X. R is an open source statistical programming and graphing language that includes tools for statistical, population genetic, genomic, phylogenetic, and comparative genomic analyses. This primer is for those of us that want to conduct applied analyses of populations and make full use of the palette of tools available in R.

Note that the R user community is a very active and that both R and its packages are regularly updated, critically modified, and noted as deprecated (no longer updated). 

> Any R user needs to make sure all components are up-to-date and that versions are compatible. 

Population genetics
----

Traditional population genetics is based on analysis of observed allele frequencies compared to frequencies expected, assuming a population genetic model. For example, under a Wright-Fisher model you might expect to see populations of diploid individuals that reproduce sexually, with non-overlapping generations. This model ignores effects such as mutation, recombination, selection or changes in population size or structure. More complex models can incorporate different aspects of effects observed in real populations. However, most of these models assume that populations reproduce sexually. 

Some basic terminology before we get started
----

Although we briefly review some terminology, we expect that the reader has a basic understanding of theory and applications of population genetics. Now, let's review some useful terms.

A **locus** is a position in the genome where we can observe one or several alleles in different individuals. Although in phylogenetic analyses, loci are typically coding genes, in population genetics, a locus is not necessarily a coding gene. Loci used in population genetics are assumed to be selectively neutral and can be an anonymous or non-coding region such as a microsatellite locus (SSR), a single nucleotide polymorphism (SNP) or the presence/absence of a band on a gel. A **genotype** is the combination of alleles carried by a  given individual at a particular set of loci. Individuals carring the same set of alleles are considered to have the same **multilocus genotype** $MLG$. 

Basic metrics of interest in populations are polymorphisms, allele frequencies and genotype frequencies. Polymorphism can be estimated in several ways, such as the total number of loci observed that have more than one allele. The frequency of an allele is calculated as the number of allele copies observed in a population divided by the total number of individuals, times the ploidy ($N$ in haploids or $2N$ in diploids) in the population. For diploids we would observe frequency $f_{A}$ and $f_{a}$ for alleles $A$ and $a$, respectively:
 
$f_{A} = \frac{N_{A}}{2N}$ and $f_{a} = \frac{N_{a}}{2N}$

where $N_{A}$ are the numbers of allele $A$ segregating in the population genotyped. 

By definition, at a biallelic locus frequencies sum to 1: 

$f_{A} + f_{a} = 1$.

Genotype frequencies are the relative frequencies of each $MLG$ observed in a population. Thus, for diploids at a **biallelic locus** we can observe three genotypes: $AA$, $Aa$, and $aa$ and their respective frequencies:

$f_{AA} = \frac{N_{AA}}{2N}$ and $f_{Aa} = \frac{N_{Aa}}{2N}$ and $f_{aa} = \frac{N_{aa}}{2N}$ 

These frequencies again add up to 1. In diploid organisms we can also calculate the frequency of **homozygotes** ($AA$ or $aa$) or **heterozygotes** ($Aa$) as the proportion of individuals falling into each class.

Genetic differentiation of two populations occurs if we observe differences in polymorphism, allele frequencies, genotype frequencies, and heterozygosity. This is often measured by metrics such as $F_{st}$ or genetic distance.

> ADD MORE <<<<<<<<<<<<<<<<<<<<<<<<<<<

The special case of clonal organisms
----

Plants and microorganisms such as fungi, oomycetes, and bacteria often reproduce clonally or follow a mixed reproductive system, including various degrees of clonality and sexuality (<a href="">Halkett et al. 2005</a>). Traditional population genetic theory does not apply and these populations violate basic assumptions in the corresponding analyses. Thus, a different set of tools are required for analyses of clonal populations (<a href="">Anderson and  Kohn, 1995</a>; <a href="">Balloux et al. 2003</a>; <a href="">Mee{\^u}s et al. 2006</a>; <a href="">Gr{\"u}nwald and Goss, 2011</a>; <a href="">Halkett et al. 2005</a>; <a href="">Milgroom, 1996</a>). Typically these focus on metrics that are model free and do not assume random mating or random sampling of alleles. Model free metrics suitable for analyzing these populations include measures of genotypic diversity and evenness, and using genetic distance to cluster populations. Additional useful measures include indices of linkage disequiblibrium among markers that are used to infer if populations are reproducing sexually or clonally. We will explore these analyses bit by bit throughout this primer. The *poppr* R package was specifically developped to facilitate analyses of clonal populations.

Marker systems, ploidy and other kinds of data
----

Population genetic data come in many shapes and forms and a good analysis needs to be tailored to the marker system, ploidy used and corresponding genetic models assumed (<a href="">Gr{\"u}nwald and Goss, 2011</a>). Organisms can be haploid, diploid with or without known phase, or polyploid and in the case of diploid species, marker systems can be dominant (AFLP, RFLP or RAPD data) or codominant (SSR, SNPs, allozymes) (<a href="">Gr{\"u}nwald et al. 2003</a>). A locus can have two alleles (0/1 for AFLP; C/T for SNPs) or many alleles per locus (A/B/C...N for SSRs or allozymes). In each case the data has to be coded and analyzed differently. Mutation rates can also differ for different markers systems (SSR: high; mitochondrial SNPs: moderate; nuclear SNPs: moderate) (<a href="">Gr{\"u}nwald and Goss, 2011</a>). 

Hierarchical analysis
----

To determine if populations show evidence of genetic structure they need to be sampled hierarchically to assess if allele frequencies in subpopulations differ from frequencies in the overall population and amongst each other. 

Applications of population genetic analyses
----

Population genetic analyses are tremendously valuable for answering questions ranging from determining best management practices to basic evolutionary questions. For example, a typical concern when finding a new, invasive organism is whether it is introduced to the area or emerged from a resident population. Other questions of interest might include: Where is the center of origin? Does this organism reproduce sexually? Is there evidence of recombination? Has the population gone through a genetic bottleneck? Are populations structured by region, geography, micro-environment? What is the source population of a newly introduced population? What are source and sink populations and what are the rates of migration? This primer will provide some of the basic tools needed to answer many of these questions for populations that are clonal or partially clonal. We hope you enjoy following along. 

References
----------

- James Anderson, Linda Kohn,   (1995) Clonality in soilborne, plant-pathogenic fungi.  *Annual Review of Phytopathology*  **33**  (1)   369-391
- Fran{\c{c}}ois Balloux, Laurent Lehmann, Thierry Mee{\^u}s,   (2003) The population genetics of clonal and partially clonal diploids.  *Genetics*  **164**  (4)   1635-1644
- Thierry Mee{\^u}s, Laurent Lehmann, Fran{\c{c}}ois Balloux,   (2006) Molecular epidemiology of clonal diploids: a quick overview and a short DIY (do it yourself) notice.  *Infection, Genetics and Evolution*  **6**  (2)   163-170
- N. Gr{\"u}nwald, S. Goodwin, M. Milgroom, W. Fry,   (2003) Analysis of genotypic diversity data for populations of microorganisms.  *Phytopathology*  **93**  (6)   738-746-NA
- Niklaus Gr{\"u}nwald, Erica Goss,   (2011) Evolution and population genetics of exotic and re-emerging pathogens: Novel tools and approaches.  *Annual Review of Phytopathology*  **49**  249-267
- Fabien Halkett, Jean-Christophe Simon, Fran{\c{c}}ois Balloux,   (2005) Tackling the population genetics of clonal and partially clonal organisms.  *Trends in Ecology \and  Evolution*  **20**  (4)   194-201
- D.L. Hartl, A.G. Clark,   (2007) Principles of Population Genetics.  [http://books.google.com/books?id=SB1vQgAACAAJ](http://books.google.com/books?id=SB1vQgAACAAJ)
- J. Hein, M. Schierup, C. Wiuf,   (2004) Gene Genealogies, Variation and Evolution: A primer in coalescent theory.  [http://books.google.com/books?id=QBC\_SFOamksC](http://books.google.com/books?id=QBC\_SFOamksC)
- Michael Milgroom,   (1996) Recombination and the multilocus structure of fungal populations.  *Annual review of phytopathology*  **34**  (1)   457-477
- R. Nielsen, M. Slatkin,   (2013) An Introduction to Population Genetics: Theory and Applications.  [http://books.google.com/books?id=Iy08kgEACAAJ](http://books.google.com/books?id=Iy08kgEACAAJ)
- A.R. Templeton,   (2006) Population Genetics and Microevolutionary Theory.  [http://books.google.com/books?id=tJVreQjInt0C](http://books.google.com/books?id=tJVreQjInt0C)
- J. Wakeley,   (2009) Coalescent Theory: An Introduction.  [http://books.google.com/books?id=x30RAgAACAAJ](http://books.google.com/books?id=x30RAgAACAAJ)
- B.S. Weir,   (1996) Genetic data analysis 2.  [http://books.google.com/books?id=e9QPAQAAMAAJ](http://books.google.com/books?id=e9QPAQAAMAAJ)
