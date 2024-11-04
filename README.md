## Overview:
This project was developed for the UOB Hackathon to address the management of e-statement file size limits and the process of transitioning between vaults (or files) when specific thresholds are exceeded. The problem we are solving relates to a real-world scenario where e-statements must be split into separate files once they exceed 700,000 lines of text. Our system simulates this behavior using shell scripts, SQLite, and file management techniques.

## Problem Statement:
Focus Area: Improving Operational Efficiency / Driving Innovation

Our problem statement is to identify potential issues in the e-statement batch processing such as abnormal file sizes in the production database, incorrect subscriptions from BWC, or unexpected server usage etc.

Our primary focus is to improve operational efficiency, increase accuracy and drive innovation by identifyng anomalies in the batch processing. Anomalies refers to abnormal data counts or file size which differ from the normal daily recordings. In the case of the banking industry, we have found anomalies in e-statement batch processing . 

During batch processing, it is critical to ensure that no error occur before releasing e-statements for printing or mailing. However, anomalies have been observed when we monitor batch processing via the server. Hence, we aim to develop a script that can automatically detect anomalies in order to enhance working efficiency.   

Specifically, we are focusing on addressing the issue of file size abnormalities. The current e-statement processing system faces a problem where files exceeding the 700,000-line limit are not moved to the next file, as they should be, and instead continue to be saved in the same file. Our solution creates a flexible and automated system that ensures adherence to the size limit, triggers warning and moves files to the next one once the threshold is reached.

We will generate mock e-statements, simulating batch processing and setting thresholds to identify when file sizes become too large. The system will provide a warning when limits are exceeded and recommend appropriate action. Our target users are employees within the Group Banking Technology (GBT) department, and the solution will indirectly benefit all UOB stakeholders, including customers.

## Overview:
This project was developed for the UOB Hackathon to address the management of e-statement file size limits and the process of transitioning between vaults (or files) when specific thresholds are exceeded. The problem we are solving relates to a real-world scenario where e-statements must be split into separate files once they exceed 700,000 lines of text. Our system simulates this behavior using shell scripts, SQLite, and file management techniques.

## Problem Statement:
Focus Area: Improving Operational Efficiency / Driving Innovation

Our problem statement is to identify potential issues in the e-statement batch processing such as abnormal file sizes in the production database, incorrect subscriptions from BWC, or unexpected server usage etc.

Our primary focus is to improve operational efficiency, increase accuracy and drive innovation by identifyng anomalies in the batch processing. Anomalies refers to abnormal data counts or file size which differ from the normal daily recordings. In the case of the banking industry, we have found anomalies in e-statement batch processing . 

During batch processing, it is critical to ensure that no error occur before releasing e-statements for printing or mailing. However, anomalies have been observed when we monitor batch processing via the server. Hence, we aim to develop a script that can automatically detect anomalies in order to enhance working efficiency.   

Specifically, we are focusing on addressing the issue of file size abnormalities. The current e-statement processing system faces a problem where files exceeding the 700,000-line limit are not moved to the next file, as they should be, and instead continue to be saved in the same file. Our solution creates a flexible and automated system that ensures adherence to the size limit, triggers warning and moves files to the next one once the threshold is reached.

We will generate mock e-statements, simulating batch processing and setting thresholds to identify when file sizes become too large. The system will provide a warning when limits are exceeded and recommend appropriate action. Our target users are employees within the Group Banking Technology (GBT) department, and the solution will indirectly benefit all UOB stakeholders, including customers.
