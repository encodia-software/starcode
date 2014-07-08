## Starcode: An exact algorithm for sequence clustering ##
---
## Contents: ##
    1. What is starcode?
    2. Source file list.
    3. Compilation and installation.
    4. Running starcode.
    5. File formats.
    6. License.
    7. References.

---
## I. What is starcode?   ##


Starcode is an exact and fast DNA sequence clustering software. Sequence
clustering is performed based on a Levenshtein distance metric [1].
Typically, a file containing a set of related DNA sequences is passed as
input, jointly with a parameter specifying the desired cluster distance.
Starcode aligns and computes the distance between all the sequence pairs
[2] and prints a line for each cluster containing: canonical DNA sequence,
sequence count and the list of sequences that belong to the cluster.

Starcode has many applications in the field of biology, such as DNA/RNA
motif recovery, barcode clustering, sequencing error recovery, etc.


II. Source file list
--------------------

* ** main-starcode.c **        Starcode main file (parameter parsing).
* ** starcode.c **             Main starcode algorithm.
* ** starcode.h **             Main starcode algorithm header file.
* ** trie.c **                 Trie search and construction functions.
* ** trie.h **                 Trie functions header file.
* ** Makefile **               Make instruction file.


III. Compilation and installation
---------------------------------

To install starcode you first need to clone or manually download the 
repository content from github:

 > git clone git://github.com/gui11aume/starcode.git

the files should be downloaded in a folder named 'starcode'. To compile
just change the directory to 'starcode' and run make (Mac users require
'xcode', available at the Mac Appstore):

 > cd starcode

 > make

a binary file 'starcode' will be created. You can optionally make a
symbolic link to execute starcode from any directory:

 > sudo ln -s ./starcode /usr/bin/starcode


IV. Running starcode
--------------------

Starcode runs on Linux and Mac. It has not been tested on Windows.

List of arguments:

  > starcode [-vs] [-d#] [-t#] [-i] INPUT_FILE [-o OUTPUT_FILE]
  
  ** -v or --verbose **

     Verbose. Prints verbose information to the standard error channel.

  ** -i or --input ** * file *

     Specifies input file.

  ** -o or --output ** * file *

     Specifies output file. When not set, standard output is used instead.

  ** -d or --distance ** * distance *

     Defines the maximum Levenshtein distance for clustering.
     Default is 3.

  ** -t or --threads ** * threads *

     Defines the maximum number of parallel threads.
     Default is 1.

  ** -s or --spheres **

     When specified, sphere clustering algorithm is performed in the
     clustering phase, instead of the default message passing algorithm.

  ** -h or --help **

     Prints usage information.
   

V. File formats
---------------

### V.I. Supported input file formats: ###

####  V.I.I. Plain text: ####

  Consists of a file containing one sequence per line. Only the standard
  DNA-base characters are supported ('A', 'C', 'G', 'T'). The sequences
  may not contain empty spaces at the beginning or the end of the string,
  as these will be counted as alignment characters. The file may not
  contain empty lines as these will be considered as zero-length sequences.
  The sequences do not need to be sorted and may be repeated.
  
  Example:

    TTACTATCGATCATCATCGACTGACTACG
    ACTGCATCGACTAGCTACGACTACGCTACCATCAG
    TTACTATCGATCATCATCGACTGACTAGC
    ACTACGACTACGACTCAGCTCACTATCAGC
    GCATCGACCGCTACTACGCATACTACGACATC


####  V.I.II. Plain text with sequence count: ####

  If the count of the sequences is known, it may be specified in the input
  file using the following format:

  > [SEQUENCE]\t[COUNT]\n

  Where '\t' denotes the TAB character and '\n' the NEWLINE character.
  The sequences do not need to be sorted and may be repeated as well. If
  a repeated sequence is found, their counts will be addded together. As
  before, the sequences may not contain any additional characters and the
  file may not contain empty lines.

  Example:

    TATCGACTCTATCTATCGCTGATGCGTAC       200
    CGAGCCGCCGGCACGTCACGACGCATCAA       1
    TAGCACCTACGCATCTCGACTATCACG         234
    CGAGCCGCCGGCACGTCACGACGCATCAA       17
    TGACTCTATCAGCTAC                    39


####  V.I.III. FASTA ####

  Starcode supports FASTA files as well. Note, however, that the only
  relevant information for starcode is the sequence itself and the FASTA
  labels will not be used to identify the sequences in the output file.
  The sequences do not need to be sorted and may be repeated.

  Example:

    >sequence 1 label
    ATGCATCGATCACTCATCAGCTACAG
    >sequence 2 label
    TATCGACTATCTACGACTACATCA
    >sequence 3 label
    ATCATCACTCTAGCAGCGTACTCGCA
    >sequence 4 label
    ATGCATCGATTACTCATCAGCTACAG


### V.II. Output format: ###

  Starcode prints a line for each detected cluster with the following
  format:

  > [CANONICAL SEQUENCE]\t[CLUSTER SIZE]\t[CLUSTER SEQUENCES]\n
  
  Where '\t' denotes the TAB character and '\n' the NEWLINE character.
  'CANONICAL SEQUENCE' is the sequence of the cluster that has more
  counts, 'CLUSTER SIZE' is the aggregated count of all the sequences
  that form the cluster, and 'CLUSTER SEQUENCES' is a list of all the
  cluster sequences separated by commas and in arbitrary order. The
  lines are printed sorted by 'CLUSTER SIZE' in descending order.

  For instance, an execution with the following input and clustering
  distance of 3 (-d3):

    TAGCTAGACGTA   250
    TAGCTAGCCGTA   10
    TAAGCTAGGGGT   16
    ACGCGAGCGGAA   155
    ACTTTAGCGGAA   1

  would produce the following output:

    TAGCTAGACGTA    276       TAGCTAGACGTA,TAGCTAGCCGTA,TAAGCTAGGGGT
    ACGCGAGCGGAA    156       ACGCGAGCGGAA,ACTTTAGCGGAA

  The same example executed with a more restrictive distance -d2 would
  produce the following output:

    TAGCTAGACGTA    260       TAGCTAGACGTA,TAGCTAGCCGTA
    ACGCGAGCGGAA    155       ACGCGAGCGGAA
    TAAGCTAGGGGT    16        TAAGCTAGGGGT
    ACTTTAGCGGAA    1         ACTTTAGCGGAA


VI. License
-----------

Starcode is licensed under the GNU General Public License, version 3
(GPLv3), for more information read the LICENSE file or refer to:

  http://www.gnu.org/licenses/


VII. References
---------------

[1] Levenshtein, V. (1966), 'Binary Codes Capable of Correcting Deletions,
    Insertions and Reversals', Soviet Physics Doklady 10, 707.

[2] Needleman, S.B. and Wunsch, C.D. (1970), 'A general method applicable
    to the search for similarities in the amino acid sequence of two
    proteins' J. Mol. Biol., 48 (3), 443-53.