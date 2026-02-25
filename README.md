
# make_mycosnp_script.py
Python code to create scripts to run in a google vm to process sra sample data from nih.
Compares sample data to reference sequence and returns comparison edits in the form of .g.vcf.gz and .maple files.
See [pathogentotree suite](https://github.com/tleppertbio/pathogentotree/blob/main/README.md) invokes the docker container tleppertwood/pathogentotree:latest.

---

## What will you need

1) [collect metadata](https://github.com/tleppertbio/pathogentotree/blob/main/metadata.README.md), to determine if you have the correct samples and the size of the sample file.
2) [sra_now.list](https://github.com/tleppertbio/pathogentotree/blob/main/README.md#create-sra_nowlist-file), a file containing the size of the sample file and the sra number, tab separated.
3) [google buckets](https://github.com/tleppertbio/pathogentotree/blob/main/README.md#how-to-create-a-bucket-identify-your-google-region-and-viewing-pricing-tablessizes-for-vms), creating a bucket to house your output data until you can retrieve it to your local machine.
4) [reference data](https://github.com/tleppertbio/pathogentotree/blob/main/README.md#create-and-execute-ref-bucket-setupscript-file), reference files prepped for analysis using nucmer, bedtools maskfasta, samtools faidx, picard.jar and bwa, which reside in the google bucket and vms during analysis.
5) [directory structure](https://github.com/tleppertbio/pathogentotree/blob/main/README.md#directory-structure-on-your-local-machine), the directory structure that is created on your local machine, pathogentotree's expected structure.
6) [after this program](https://github.com/tleppertbio/pathogentotree/blob/main/README.md#running-invoke_mycosnp_scriptpy) script that is to be run after make_mycosnp_script.py
7) [complete pathogentotree package](https://github.com/tleppertbio/pathogentotree/blob/main/README.md) full documentation to the entire process, setting up google cloud vms to run pathogentotree docker container to analyze nih sra datasets to find reference compared sequence edits.

### Running make_mycosnp_script.py

  **What does this do?**
  
  make_mycosnp_script.py reads in the sra_now.list and creates a vm processing script for each sample.
  These processing scripts need to be pushed (invoked) to an appropriately configured vm size and location. 
  The processing scripts interact with the pathogentotree docker image via a google vm.
  This script executes a sequence of programs to compare sample sequences to a reference and finds edit differences.
  Edit differences are listed in .g.vcf.gz and .maple files.
  [Diagram of mycosnp based workflow](./mycosnp-based-workflow.png)

  **How to run it?**
  
  python3 make_mycosnp_script.py

  **Things to know**
  
  - When the size of the sample file is >= 9GB this script creates two vm process scripts SRR#-startup-vm1.script and SRR#-startup-vm2.script.
  - The two scripts are created as separate processes, because the second half of the protocol can take longer than the 24 hour preemptible window.
  - The second script *-vm2.script is invoked with non-preemptible parameters.
  - A non-preemptible run is more expensive than a preemptible run.
  - If the size of the sample file is < 9GB bases, the process can run with one vm instance.
  - The make_mycosnp_script.py determines when the file size is >= 9GB, this is read in the sra_now.list file.
  - These execute vm files are copied to ./vm-scripts in your working directory and are chmod 775.
  - If the ./vm-scripts folder does not exist, the make_mycosnp_script.py will create it.
  - If you wish to change parameters for any of the internal process, you can modify or specify those parameters in this python code.

Here is an example of the type of file that is created by this make_mycosnp_scripts.py python program
[example mycosnp processing file script](https://github.com/tleppertbio/pathogentotree/blob/main/mycosnp.sample.script)

![Diagram of mycosnp based workflow](./mycosnp-based-workflow.png)
