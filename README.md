# Introduction  
**IsoSplitter** is a de novo identification tool of alternative splicing sites using long-reads transcriptome without a reference genome. IsoSplitter consists of two steps of analyses that are executed by IsoSplittingAnchor.py and ShortReadsAligner.py. The latter analysis is optional, which takes short reads of transcriptome sequencing to evaluate the splicing sites predicted by IsoSplittingAnchor.py.  
 
**IsoSplittingAnchor.py** invokes the SIM4 alignment algorithms (Florea et al. 1998) to find splitting sites derived from the whole long-read transcriptome. The first output file-named as Breakingpoint_out-is a tabular text containing the sequencing ID and the location of splicing sites. The second output file-named as GeneCluster-is the results of sequence IDs that are transcript isoforms, and each cluster is a potential gene locus for alternative splicing isoforms. The third output file-Sim4_align_out.txt-is the alignments generated by SIM4.  

**ShortReadsAligner.py** is used to evaluate the splitting sites by counting the eligible breaking points. This step analysis a short-read RNA sequencing dataset to find the “junction reads”-reads partially mapped next to the breaking sites and exclusively split at the same location, and count the numbers of junction reads for each breaking sites. 

# Installation
## Dependencies of IsoSplitter:
* Python (version:>=3.5)
* setuptools (generally installed in python by default)
* sim4
* bowtie2  

All dependencies (except setuptools) must be executable and reachable in the user's PATH.

1. IsoSplitter's running platform is UNIX. Python3.5+ is required in your environment. You also need to install the PIP function corresponding to your Python version. 

2. Setuptools is generally installed in python by default. If you meet the error like *"ImportError: No module named 'setuptools'"* during the installation of IsoSplitter, you can reinstall the latest setuptools by typing in the console:  
`$ pip install setuptools`

3. Sim4 is required for IsoSplittingAnchor.py. It is freely accessible from <http://globin.cse.psu.edu/html/docs/sim4.html> (Florea et al. 1998). The following provides a succinct way of installing SIM4. 
  * Unpack the downloaded files.  
    `$ tar -zxvf sim4.tar.gz`
  * The tar file will unpack into a directory named sim4.[some-date]. (You'll see what the precise name is while tar is unpacking.)  
    `$ cd sim4.*`
  * Compile and you may get some warnings from the compiler.  
    `$ make`
  * The executable will be named *"sim4"*, then copy it to your bin directory like this:  `$ cp sim4 /usr/local/bin/sim4`.  
    You can check the installation by typing: `$ sim4 –h`

4. *"bowtie2-build"* and *"bowtie2"* are used in ShortReadsAligner.py. Bowtie2-build is a part of Bowtie2 and free from <http://bowtie-bio.sourceforge.net/bowtie2/index.shtml>. The installation detail can be found at <http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml>. You also need to copy *"bowtie2-build"* and *"bowtie2"* to your bin directory.

## Installation of IsoSplitter
Download the IsoSplitter release file (IsoSplitter-1.0.tar.gz) from <https://github.com/Hengfu-Yin/IsoSplitter>. We provide two ways of installation.

1. Option 1 (recommended):  
  * Unpack the downloaded files.  
     `$ tar -zxvf IsoSplitter-1.0.tar.gz`
  * The tar file will unpack into a directory named like IsoSplitter-1.0  
    `$ cd IsoSplitter-1.0`
  * Install by Python3.5+. The executable *IsoSplittingAnchor* and *ShortReadsAligner* will be installed to */usr/local/bin*.   
    `$ python3.5 setup.py install`

2. Option 2:
    You can run IsoSplitter without installation directly by running the script "IsoSplittingAnchor.py" or "ShortReadsAligner.py" that are in the directory ("scripts") of the source code by:  
    `$ python3.5 IsoSplittingAnchor.py ...` or `$ python3.5 ShortReadsAligner.py ...`

# Usage of first step IsoSplittingAnchor
## Quick Start  
   `$ IsoSplittingAnchor [options] longReadsFile`  
   by default:   
  `$ IsoSplittingAnchor -i 95 -L 30bp –t <all available threads> longReadsFile`

   You can also run the script "IsoSplittingAnchor.py" directly, for example:  
   `$ python3.5 IsoSplittingAnchor.py [options] longReadsFile`

## Command Line Options

|   Main  arguments  : |    |  
| :- | :- |   
| longReadsFile | The transcript sequences file in fasta format. This argument is required.|
| **Options :**|  |
|  -h, --help | show this help message and exit |
|  -v, --version | show program's version number and exit |
|  -i \<integer> | the minimum identity (0-100) (default: 95)|
|  -L \<integer+bp> | the minimum length of each matched fragment(>14bp) (default: 30bp)|
|  -t \<integer> | the number of threads (default is to use all available threads)|

## Output Files
The output directory will be created in the working directory and named 'Fout[time]', where [time] is the current UNIX time according to the system. If the directory already exists, IsoSplittingAnchor exits with an error message.  
There are three output files: *Sim4\_align\_out.txt*, *Breakpoint\_out.txt*, *GeneCluster.txt*.  

- **Sim4\_align\_out.txt** :  
> sequence alignment results from the sim4 analysis

- **Breakpoint_out.txt** :  
> Format of each record :  
> >  seq = the transcripts sequence name  
> >  total number of isoforms   {breakpoint : location of split-site, ... }

- **GeneCluster.txt** :  
> sequence IDs that are transcript isoforms, and each cluster is a potential gene locus for alternative splicing isoforms

## Notes

1. If the sequence contains lowercase characters, the script will automatically convert to uppercase when compared, and output a warning to the console. They don't affect the results, so they can be ignored.
2. The sequence name can't contain the character '/', it will lead the script to error. If the sequence name has whitespace characters, the name will be trimmed at the first whitespace character.

# Usage of second step ShortReadsAligner
## Quick Start
    
  `$ ShortReadsAligner [options] longReadsFile shortReadsFile BreakPointFile `  
  by default:  
  `$ ShortReadsAligner -t 4 -n 3 longReadsFile shortReadsFile BreakPointFile `  
  
  If you run the script "ShortReadsAligner.py" directly:  
  `$ python3.5 ShortReadsAligner.py [options] longReadsFile shortReadsFile BreakPointFile `

## Command Line Options
| Main arguments: |  |
| :- | :-|
| longReadsFile |  the file is your input of long-read transcriptome sequences in the first step |
| shortReadsFile | second generation transcriptome sequence, must be in fasta, fastq,or their gzip-compressed versions |
| BreakPointFile | the file is one of output of first step |
| **Options :**
| -h, --help  |  show this help message and exit |
| -v, --version | show program's version number and exit |
| -t \<integer> | the number of threads (default: 4) (t*n<= total number of CPUs) |
| -n \<integer> | the number of split files (default: 3) (t*n<= total number of CPUs) for a multithreading purpose |
| -q, --fastq | If this option is present, the shortReadsFile must be in fastq format (default is in fasta format) |

## Output Files
The output directory will be created in the directory where the program run and it defaults to 'Sout[time]', where [time] is the current UNIX time according to the system. If the directory already exists, ShortReadsAligner exits with an error message.  
There are two output files:  *ShortReadsMapped.sam*, *JunctionReadsCount.txt*.

- **ShortReadsMapped.sam** :
> Sequence alignment result through bowtie2

- **JunctionReadsCount.txt** :
> Format of each record:
>> reference sequence name {breakpoint :  junction-read count, ... }

## Notes
1. The options of "-t<integer>" and "-n<integer>" are limited, t*n<= total number of CPUs. If you don't know the total number of CPUs, you can use "ShortReadsAligner -h" to query. It will appear in the description of ShortReadsAligner.
2. The number of threads (option of -t) is optimized around 4. You can adjust the number of files (option of -n) to reduce ShortReadsAligner's running time.
3. At least 50G of hard disk space is recommended, due to the large size of temporary and final alignment files.

# Example Datasets
We use current gene transcripts data from Arabidopsis thaliana to test the identification of alternative splicing sites. Transcripts are available from the TAIR website ([Araport11_genes.201606.cdna.fasta.gz](https://www.arabidopsis.org/download_files/Genes/Araport11_genome_release/Araport11_blastsets/Araport11_genes.201606.cdna.fasta.gz)). We further align the short reads from a previous study (Li et al. 2016 Dev. Cell) to validate the prediction of split-sites from previous step. The short-reads file is available from the NCBI SRA database (accession SRR3664433). For testing by ShortReadsAligner, you need to convert SRA data into fastq format, and filter adaptor and low-quality sequences.  

The followings are some scripts for quick reproduction of the test results.     
**To identify the split-sites:**  
	 `$ IsoSplittingAnchor Araport11_genes.201606.cdna.fasta`  
**To validate the split-sites:**  
Breakpoint_out.txt is one of the output of first step, Araport11_genes.201606.cdna.fasta is the same file of first step, SRR3664433.fasta / SRR3664433.fastq is sequencing data.  
	 `$ ShortReadsAligner Araport11_genes.201606.cdna.fasta SRR3664433.fasta Breakpoint_out.txt`  
   or  
	 `$ ShortReadsAligner -q Araport11_genes.201606.cdna.fasta SRR3664433.fastq Breakpoint_out.txt`  
