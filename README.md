## Overview:
This project was developed for the UOB Hackathon to address the management of e-statement file size limits and the process of transitioning between vaults (or files) when specific thresholds are exceeded. The problem we are solving relates to a real-world scenario where e-statements must be split into separate files once they exceed 700,000 lines of text. Our system simulates this behavior using shell scripts, SQLite, and file management techniques.

## Problem Statement:
Focus Area: Improving Operational Efficiency / Driving Innovation

Our problem statement is to identify potential issues in the e-statement batch processing such as abnormal file sizes in the production database, incorrect subscriptions from BWC, or unexpected server usage etc.

Our primary focus is to improve operational efficiency, increase accuracy and drive innovation by identifyng anomalies in the batch processing. Anomalies refers to abnormal data counts or file size which differ from the normal daily recordings. In the case of the banking industry, we have found anomalies in e-statement batch processing . 

During batch processing, it is critical to ensure that no error occur before releasing e-statements for printing or mailing. However, anomalies have been observed when we monitor batch processing via the server. Hence, we aim to develop a script that can automatically detect anomalies in order to enhance working efficiency.   

Specifically, we are focusing on addressing the issue of file size abnormalities. The current e-statement processing system faces a problem where files exceeding the 700,000-line limit are not moved to the next file, as they should be, and instead continue to be saved in the same file. Our solution creates a flexible and automated system that ensures adherence to the size limit, triggers warning and moves files to the next one once the threshold is reached.

We will generate mock e-statements, simulating batch processing and setting thresholds to identify when file sizes become too large. The system will provide a warning when limits are exceeded and recommend appropriate action. Our target users are employees within the Group Banking Technology (GBT) department, and the solution will indirectly benefit all UOB stakeholders, including customers.

## Tools & Technologies:
## SQLite
A lightweight database system used to track the vaults, file batches, and e-statement allocation.
## MobaXterm 
A terminal emulator and SSH client used for running scripts locally on the development machine.
## PDF Generation
Scripts were developed to generate sample PDF e-statements for testing purposes.
## Features:
## File Allocation 
The system allocates e-statements to files, ensuring no file exceeds 700,000 lines of text.
## File Vault Switching
When a file exceeds the limit, the system automatically moves to the next file and continues the process.
Tracking via SQLite: Each e-statement’s location is tracked in a SQLite database, which stores details such as pdf file name, folder name, line count, and file size.

## The entire guide on the detection of abnormal size process:
1. Install mobaXterm (https://mobaxterm.mobatek.net/download-home-edition.html) (installer edition)

2. Since we do not have a server to connect to, we will be using our own local terminal. 
   - Settings->configuration->terminal->local terminal environment->Bash (64 bit) (this will auto download the CygUtils plugin)

3. You can test it out by creating a script, either by within mobaXterm, or connecting to windows. But for simplicity sake, we will create from within. 

4. To edit your script, nano file_name
   - After you are done editing, ctrl+x to exit-> Y -> press 'enter'
   - Alternatively, you can use vim file_name. To edit, enter 'i'. After you are done editing, press esc key-> and enter ":wq" 
     to save and exit

5. Created a script (pdf.py) that generates EST PDF
   - Ensure python is installed onto mobaXterm
   - pip install fpdf 
   - Created a directory that contains the generated EST PDF called 'EST_Folder'

6. For some reason, we am unable to view the PDF e-statements on mobaXterm, so we moved the PDF to windows drive
   - Created a folder named 'SCRIPTS' within windows. File path: /drives/c/Users/user/OneDrive/Desktop/SCRIPTS
   - type mv EST_Folder /drives/c/Users/user/OneDrive/Desktop/SCRIPTS/ into the terminal and it will move the pdf to windows drive
   - mv EST_Folder /drives/c/Users/ryan\ yeoh/Desktop/SCRIPTS (only difference is the user)
                                                                                                     

7. Next, we created the script (trigger_warning.py) that allocates the EST PDF into the folders, with each folder having a number of 
   lines of text limited to a <> lines
   - Running this script will then created a new directory named 'vault_manangement.db'

8. At the same time, it creates a database out of it
   - pip install sqlite3 if sqlite has not been installed
   - If unable to do so, download sqlite (https://www.sqlite.org/download.html) and set it as a new path in environment variable-> system 
     variable-> path -> edit -> new -> add in the path directory


## pdy.py_code:
The pdf.py script is responsible for generating mock e-statements as PDF files. It simulates four different types of statements that are common in banking operations: CASA, CPFIS, Credit Card, and SRS statements. The PDFs are generated with random account and transaction data and are saved into corresponding folders under the EST_Folder.

## trigger_warning.py_code
This code first prompts the user to decide which type of e-statements to perform allocation on as well as the number of files that will then allocated generated e-statements PDF into the files of the Vault with the threshold value implemented. It then extracts the lines of text and count them, making sure the file adhere to the maximum number of lines of text, with the last file as the exception. The code will continue to allocate the remaining generated e-statements PDF into the last file, ignoring the threshold. Lastly, it will then calculate the number of lines of text that exceeds the threshold in percentage. This serves as a warning to the user to allocate more files to prevent it from happening. At the same time, the SQLite database is updated with each processed batch, tracking the files and their corresponding sizes.
