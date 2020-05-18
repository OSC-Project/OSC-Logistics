# Queries Report

This report shows the results of the LGTM query set versus our LGTM++ query set. The SinkFinder's results are also included.

## Results
![](./Tools_over_Packages.png)

The tools being used are our additional queries (LGTM++), the built in queries from codeQL (LGTM), and the SinkFinder we've developed previously. The included packages are:

- the list of 50 from Comcast (except for 2 that wouldn't compile)
- 3 additional packages from CVEs we found to be relevant. 

Overall, LGTM++ had the highest true positive rate with the smallest false positive rate. The further from the line on the left side, the better the tool.

### Queries

#### What exactly is in our "Classic Sink Finder" (which ones are missing from LGTM++: Ahmad's question).

#### LGTM

List of queries included.

#### LGTM++

List of queries included. If these are _NOT_ a superset of LGTM. BE explicit about it.

## Scoring

Each tool was scored using our benchmark's scorecard generator process. This is process that mimic's OWASP's scoring schema. The automation script runs the queries over each package and outputs each result (empty file if nothing is found) in a csv file. The scorecard script reads each file and counts the true/false positives/negatives. The index.csv indicates the location of of all true positives in the packages to help with scoring.

**Recall formulas for the rates**

**How do we count, no duplicates**

## LGTM

The scorecard is in the repository labeled `LGTM over Packages.csv`. The false positive rate is 0.21, the true positive rate is 0.36, and the corresponding score is 0.14. The queries used are in the json `LGTM.json` and are `CodeInjection.ql` and `Eval.ql`.

| **CWE**               | **TP**     | **FN**     | **TN**      | **FP**      | **Total**            | **TPR**                         | **FPR**                         | **Score**                         |
| --------------------- | ---------- | ---------- | ----------- | ----------- | -------------------- | ------------------------------- | ------------------------------- | --------------------------------- |
| **94:Code injection** | 4          | 7          | 84          | 23          | 118                  | 0.36363636363636400             | 0.21495327102803700             | 0.14868309260832600               |
|                       | Total TP:4 | Total FN:7 | Total TN:84 | Total FP:23 | Total Test Cases:118 | Average TPR:0.36363636363636365 | Average FPR:0.21495327102803738 | Average Score:0.14868309260832627 |



## LGTM++

The scorecard is in the repository labeled `LGTM++ over Packages.csv`. The false positive rate is 0.12, the true positive rate is 0.66, and the corresponding score is 0.54. The query used are in the json `LGTM++.json` and is `customCodeInjection2.ql`.

| **CWE**               | **TP**     | **FN**     | **TN**      | **FP**     | **Total**           | **TPR**                        | **FPR**                         | **Score**                        |
| --------------------- | ---------- | ---------- | ----------- | ---------- | ------------------- | ------------------------------ | ------------------------------- | -------------------------------- |
| **94:Code injection** | 4          | 2          | 43          | 6          | 55                  | 0.6666666666666670             | 0.12244897959183700             | 0.5442176870748300               |
|                       | Total TP:4 | Total FN:2 | Total TN:43 | Total FP:6 | Total Test Cases:55 | Average TPR:0.6666666666666666 | Average FPR:0.12244897959183673 | Average Score:0.5442176870748299 |



## SinkFinder

The scorecard is in the repository labeled `SinkFinder over Packages.csv`. The false positive rate is 0.66, the true positive rate is 1, and the corresponding score is 0.34. This tool was run over the same package list and manually counted.

| **CWE**               | **TP**     | **FN**     | **TN**      | **FP**      | **Total**            | **TPR**       | **FPR**          | **Score**          |
| --------------------- | ---------- | ---------- | ----------- | ----------- | -------------------- | ------------- | ---------------- | ------------------ |
| **94:Code injection** | 6          | 0          | 34          | 66          | 106                  | 1             | 0.66             | 0.34               |
|                       | Total TP:6 | Total FN:0 | Total TN:34 | Total FP:66 | Total Test Cases:106 | Average TPR:1 | Average FPR:0.66 | Average Score:0.34 |

# Include the Raw DATA
