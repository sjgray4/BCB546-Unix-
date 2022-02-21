**Data Inspection of snp_position.txt**

***$ head -n 3 snp_position.txt***
* From this inspection I learned 
    * The layout of the snp_position.txt table
    * The first column is the header column followed by SNP table
    * The first SNP ID is abph 1.20 on chromosome 2
    
    
**$ tail -n 3 snp_position.txt**
* From this inspection I learned
    * Chromosome 9 is the last listed in the file. with the SNP ID zmm3.4
    
 **$ wc -l snp_position.txt**
 * From this inspection I learned
 
    * There are 984 lines in the file
    
**$ du -h snp_position.txt**

* From this inspection I learned 
     * The file size of this file is 39K 
    
    
    
**Data Inspection of fang_et_al_genotypes**

**$ head -n1 fang_et_al_genotypes.txt**
    
   *  From this inspection I learned that there are many column headers that appear to be SNP IDs, and that the lines in this file are large
    
 **$ less fang_et_al_genotypes.txt**
    
 * From this inspection I was able to scroll across the file to become familiar with its structure. From this inspection I learned that there are genotypes at the head of a section of SNP data. 
    
**$ wc fang_et_al_genotypes.txt**
    
    
* From this inspection I learned that the fang_et_al_genotypes.txt file has 2783 lines,  2744038 words, and 11051939 characters. 


**$du -h fang_et_al_genotypes.txt**

* From this insepection I learned that the fang_et_al_genotypes.txt file size is 6.6M

**$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt**

   * From this inspection I learned that there are 986 columns in this file.
   
**cut -f 1-4 fang_et_al_genotypes.txt|column -t**

   * From this inspection I was able to view the first four columns from the file and the column -t command gave a more organized view of the way the data was aligned in the file.  









**Data Processing** 


**Processing SNP File** 

**$ cut -f 1,3,4 snp_position.txt|sort -k1,1 >snp_newfile**
  * Here is the code I used to reorganize the columns in the snp folder to go from SNP ID, chromosome, and position. I then used the sort command to order the columns based on SNP ID. 
  
  
  
**Processing Maize Genotypes** 

 **$ grep -E "(ZMMIL|ZMMLR|ZMMMR|Group)" fang_et_al_genotypes.txt|cut -f 1,4-986 > maize_genotypes**
   * From this code I was able to find my maize genotypes, and then used cut command to keep the Sample_ID and genotype data moving forward 
   
**$ awk -f transpose.awk maize_genotypes > transposed_maize_genotypes.txt**
   * From this code I transposed my  maize_genotypes file
   
**$ sed 's/Sample_ID/SNP_ID/' transposed_maize_genotypes.txt|sort -k1,1> maize_genotype_sort_file**
   * From this code I replaced the Sample_ID header with the SNP_ID header to be consistent with the SNP file. I also sorted the transposed data by the first column. 
   
   
**$ join -1 1 -2 1 -t $'\t' snp_newfile maize_genotype_sort_file > maize_join_file*** 
   * From this code I joined snp_newfile and maize_genotype_sort_file based on the first column of both files. using the -t command enabled me make the file tab delimited 
   
**$ for i in {1..10} ; do awk '$1 ~ /SNP/' maize_join_file && awk '$2 == '$i' && $3 != "multiple"' maize_join_file > maize_chrm$i.txt ; done**
  * From this code I ran a loop to separate the the maize SNPs into seperate files based on chromosome.  i being the number of chromosome the file will print out maize_chrm$i.txt  
  
**$ (awk '$1 ~ /SNP/' maize_join_file && awk '$3 == "multiple"' maize_join_file)> maize_multiple_file**
  * From this code I separated the SNPs that have multiple places in the genome by finding the "mulitple" pattern in the third column of the maize_join_file.

**$ (awk '$1 ~ /SNP/' maize_join_file && awk '$3 == "unknown"' maize_join_file)> maize_unknown_file**
  * From this code I separated the SNPs that have unknown places in the genome by finding the "unknown" pattern in the third column of the maize_join_file

**$ for i in {1..10}; do (sort -k3,3n maize_chrm$i.txt) > SNPS_increase_chrm$i.txt ; done**
  * From this code I ran a loop to sort the separate chromosome files based on the third column SNP position. 

**$ for i in {1..10}; do (sort -k3r,3n maize_chrm$i.txt)| sed 's/?/-/g' > SNPS_decrease_chrm$i.txt ; done**
  * From this code I ran a loop to sort the separate chromosome files based on the third column SNP position in reverse order. I also used the sed command to find and replace the ? with -
  
  
**$ mkdir maize_processed_data**
  * From this code I created a new directory to store processed maize files. 

**$ mv SNPS_increase_chrm1.txt  maize_processed_data/**
  * From this code I moved all the new processed files into the maize_processed_data. 
  










   









**Processing Teosinte Genotypes** 

**$ grep -E "(ZMPBA|ZMPIL|ZMPJA|Group)" fang_et_al_genotypes.txt|cut -f1,4-986 >teosinte_genotypes**
  * From this code I was able to find my teosinte genotypes and then use the cut command to keep the Sample_ID and genotype data moving forward. 
  
**$ awk -f transpose.awk teosinte_genotypes> transposed_teosinte_genotypes.txt**
  * From this code I transposed the teosinte_genotypes file
  
**$ sed 's/Sample_ID/SNP_ID/' transposed_teosinte_genotypes.txt|sort -k1,1>teosinte_genotype_sort_file**
  * From this code I replaced the Sample_ID header with SNP_ID to be consistent with the SNP file. I also sorted the file by the first column. 
  
**join -1 1 -2 1 -t $'\t' snp_newfile teosinte_genotype_sort_file > teosinte_join_file**
  * From this code I joined the snp_newfile and the teosinte_genotype_sort_file. I sorted based on the first column of each file in order to join them. I used -t to make the file tab delimited. 
  
  
**$ for i in {1..10} ; do awk '$1 ~ /SNP/' teosinte_join_file && awk '$2 == '$i' && $3 != "multiple"' teosinte_join_file > teosinte_chrm$i.txt ; done**


**$ (awk '$1 ~ /SNP/' teosinte_join_file && awk '$3 == "multiple"' teosinte_join_file)> maize_multiple_file**

**$ (awk '$1 ~ /SNP/' teosinte_join_file && awk '$3 == "unknown"' teosinte_join_file)> maize_unknown_file**

**$ for i in {1..10}; do (sort -k3,3n teosinte_chrm$i.txt) > SNPS_increase_chrm$i.txt ; done**

**$ for i in {1..10}; do (sort -k3r,3n teosinte_chrm$i.txt)| sed 's/?/-/g' > SNPS_decrease_chrm$i.txt ; done**

















   

   


 



 
 
 






    
 





















        



    