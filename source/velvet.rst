==========================================
Assembling reads with Velvet
==========================================
In this exercise we will learn how to perform an assembly with Velvet. Velvet
takes your reads as input and turns them into contigs. It consists of two
steps. In the first step, ``velveth``, the de Bruijn graph is created.
Afterwards the graph is traversed and contigs are created with ``velvetg``.
When constructing the de Bruijn graph, a *kmer* has to be specified. Reads are
cut up into pieces of length *k*, each representing a node in the graph, edges
represent an overlap (some de Bruijn graph assemblers do this differently, but
the idea is the same). The advantage of using kmer overlap instead of read
overlap is that the computational requirements grow with the number of unique
kmers instead of unique reads. A more detailled explanation can be found at
http://www.nature.com/nbt/journal/v29/n11/full/nbt.2023.html.


Pick a kmer
===========
Please work in pairs for this assignment. Every group can select a kmer of
their likings - pick a random one if you haven't developed a preference yet.
Write you and your partner's name down at a kmer on the
`Google doc`__ for this workshop.

.. _doc: https://docs.google.com/spreadsheet/ccc?key=0AvduvUOYAB-_dDJwcG1jbk1kTTY3eGZNazJGMUhfM3c#gid=3.
__ doc_ 

velveth
=======
Create the graph data structure with ``velveth``. Again like we did with
``sickle``, first create a directory with symbolic links to the pairs that you
want to use::

    mkdir -p ~/metagenomics-workshop-gbg/velvet
    cd ~/metagenomics-workshop-gbg/velvet
    ln -s ../sickle/qtrim1.fastq pair1.fastq
    ln -s ../sickle/qtrim2.fastq pair2.fastq

The reads need to be interleaved for ``velveth``::

    shuffleSequences_fastq.pl pair1.fastq pair2.fastq pair.fastq

Run velveth over the kmer you picked (21 in this example)::

    velveth out_21 21 -fastq -shortPaired pair.fastq

Check what directories have been created::

    ls

velvetg
=======
To get the actual contigs you will have to run ``velvetg`` on the created
graph. You can vary options such expected coverage and the coverage cut-off if
you want, but we do not do that in this tutorial. We only choose not to do
scaffolding::

    velvetg out_21 -scaffolding no


assemstats
==========
After the assembly one wants to look at the length distributions of the
resulting assemblies. You can use the ``assemstats`` script for that::

    assemstats 100 out_*/contigs.fa

Try to find-out what each of the stats represent by varying the cut-off. One of
the most often used statistics in assembly length distribution comparisons is
the *N50 length*, a weighted median, where you weight each contig by it's
length. This way you assign more weight to larger contigs. Fifty procent of all
the bases in the assembly are contained in contigs shorter or equal to N50
length. Once you have gotten an idea of what it all the stats mean, it is time
to compare your results with the other attendees of this workshop. Use ``tail``
and ``xclip`` to copy the results to the doc_::

    assemstats 100 out_*/contigs.fa | tail -n +2 | xclip -selection clipboard

Do the same for the cut-off at 1000 and add it to the doc_. Compare your kmer
against the others. If there are very little available yet, this would be an
ideal time to help out some other attendees or do the same exercise for a kmer
that has not been picked by somebody else yet. Please write down you and your
partners name again at the doc_ in that case.


What are the important length statistics? Do we prefer sum over length? Should
it be a combination? Think of a formula that could indicate the best preferred
length distribution where you express the optimization function in terms of the
column names from the doc_. For instance only ``n50_len`` or ``sum *
n50_len``.


(Optional exercise) VelvetOptimiser
===================================
VelvetOptimiser_ is a script that runs Velvet multiple times and follows the
optimization function you give it. Use VelvetOptimiser to find the assembly
that gets the best score for the optimization function you designed in `assemstats`_.

.. _VelvetOptimiser: https://github.com/Victorian-Bioinformatics-Consortium/VelvetOptimiser
