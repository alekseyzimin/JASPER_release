# JASPER software for polishing genome assemblies and creating personalized reference genomes

JASPER (Jellyfish based Assembly Sequence Polisher for Error Reduction) is an efficient polishing tool for draft genomes.  It uses accurate reads (PacBio HiFi or Illumina) to evaluate consensus quality and correct consensus errors in genome assemblies.  JASPER is substantially faster than polishing methods based on sequence alignment, and more accurate than currently available k-mer based methods.  The efficiency and scalability of JASPER allows one to use it to create personalized reference genomes for specific populations very efficiently, even for large sequenced populations, by polishing the reference genome, such as GRCh38 or chm13v2.0 for human, with Illumina reads sequenced from many individuals from the population. 

## Dependencies
* Python 3
* Biopython (https://biopython.org/)
  
# Installation insructions

To install, first download the latest distribution tarball JASPER-X.X.X.tar.gz (not one of the Source files) from the github release page https://github.com/alekseyzimin/EviAnn_release/releases. Replace X's below with the version number. Then run:
```
$ tar xvzf JASPER-X.X.X.tar.gz
$ cd JASPER-X.X.X
$ ./install.sh
```
The installation script will configure and make all necessary packages.  The EviAnn executables will appear under bin/.  You can run JASPER from anywhere by executing /path_to/JASPER-X.X.X/bin/jasper.sh.  JASPER installs jellyfish k-mer counter by default, and adds the path to the jellyfish python binding to jasper.sh.

## Only for developers

You can clone the development tree, but then there are dependencies such as swig and yaggo (http://www.swig.org/ and https://github.com/gmarcais/yaggo) that must be available on the PATH:

```
$ git clone https://github.com/alekseyzimin/JASPER_release
$ cd JASPER_release
$ git submodule init
$ git submodule update
$ cd JASPER && git checkout develop
$ cd ..
$ make
```
To create a distribution, run 'make install'. Run 'make' to compile the package. The binaries will appear under build/inst/bin.  
Note that on some systems you may encounter a build error due to lack of xlocale.h file, because it was removed in glibc 2.26.  xlocale.h is used in Perl extension modules used by EviAnn.  To fix/work around this error, you can upgrade the Perl extensions, or create a symlink for xlocale.h to /etc/local.h or /usr/include/locale.h, e.g.:
```
ln -s /usr/include/locale.h /usr/include/xlocale.h
```

## Running JASPER
To run JASPER, execute <PATH>/bin/jasper.sh with the following options:\
(default value in (), *required):\
```
-b, --batch=uint64               Desired batch size for the query (default value based on number of threads and assembly size). For the efficiency of jellyfish database loading, the max number of batches is limited to 8.
-t, --threads=uint32             Number of threads (1)
-a --assembly                    *Path to the assembly file
-j --jf                          Path to the jellyfish database file. Required if --reads is not provided
-r --reads                       Path to the file(s) containing the reads to construct a jellyfish database. If two or more files are provided, please enclose the list with single-quotes, e.g. -r '/path_to/file1.fastq /path_to/file2.fastq'. Both fasta and fastq formats are acceptable. Required if --jf is not provided.
-k, --kmer=uint64                k-mer size (37)
-p, --num_passes=utint16         The number of iterations of running jasper for fixing (2). A number smaller than 4 is usually more than sufficient
-h, --help                       This message
-v, --verbose                    Output information (False)
-d. --debug                      Debug mode. If supplied, all the _iter*batch*csv and _iter*batch*fa.temp files will be kept for debugging 
```
When JASPER runs with --reads, it creates mer_counts.jf Jellyfish k-mer count database from the reads. On subsequent runs in the same folder, if mer_counts.jf exists, it re-uses the mer_counts.jf.  Here is an example of how to run JASPER:

```shell
/PATH/bin/jasper.sh --reads '/path/read1.fastq /path/read2.fastq' -a assembly.fasta -k 25 -t 16 -p 4 1>jasper.out 2>&1
```
This command will polish assembly.fasta using reads /path/read1.fastq and /path/read2.fastq with 16 threads and with 4 passes of polishing. jasper.out will contain the diagnostic output including the QV value before and after polishing, and the polished assembly will be output as assembly.fasta.fixed.fasta.  
