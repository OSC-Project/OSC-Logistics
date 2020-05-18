# Queries Report

This report shows the results of the LGTM query set versus our LGTM++ query set. The SinkFinder's results are also included.

## Results
![](./TOPackages.png)

The tools being used are our additional queries (LGTM++), the built in queries from codeQL (LGTM), and the SinkFinder we've developed previously. The included packages are:

- the list of 50 from Comcast (except for 2 that wouldn't compile)
- 3 additional packages from CVEs we found to be relevant.

Overall, LGTM++ had the highest true positive rate with the smallest false positive rate. The further from the line on the left side, the better the tool.

### Queries

#### Classic Sink Finder

AST pattern matching for eval and the obfuscated variants.


#### LGTM

List of queries included.
CodeInjection.ql - LGTM query designated for Code Injection.
Eval.ql - LGTM query designated for finding Eval.

#### LGTM++
This is not a superset of LGTM

list of queries included
customCodeInjection.ql - Our custom query for finding eval.
customCodeInjection2.ql - a simplified version of the previous query.

List of queries included. If these are _NOT_ a superset of LGTM. BE explicit about it.  

## Scoring

Each tool was scored using our benchmark's scorecard generator process. This is process that mimic's OWASP's scoring schema. The automation script runs the queries over each package and outputs each result (empty file if nothing is found) in a csv file. The scorecard script reads each file and counts the true/false positives/negatives. The index.csv indicates the location of of all true positives in the packages to help with scoring.

**Recall formulas for the rates**

true positive rate = TP / (TP+FN)
false positive rate = FP / (FP + TN)

**How do we count, no duplicates**

Each query result is counted and calculated. If multiple queries are run, results are found and each query will only count if it is different from the previous results.

## LGTM

The scorecard is in the repository labeled `LGTM over Packages.csv`. The false positive rate is 0.28, the true positive rate is 0.66, and the corresponding score is 0.28. The queries used are in the json `LGTM.json` and are `CodeInjection.ql` and `Eval.ql`.

| **CWE**               | **TP**     | **FN**     | **TN**      | **FP**      | **Total**           | **TPR**                        | **FPR**                        | **Score**                         |
| --------------------- | ---------- | ---------- | ----------- | ----------- | ------------------- | ------------------------------ | ------------------------------ | --------------------------------- |
| **94:Code injection** | 4          | 2          | 38          | 15          | 59                  | 0.6666666666666670             | 0.2830188679245280             | 0.38364779874213800               |
|                       | Total TP:4 | Total FN:2 | Total TN:38 | Total FP:15 | Total Test Cases:59 | Average TPR:0.6666666666666666 | Average FPR:0.2830188679245283 | Average Score:0.38364779874213834 |

## LGTM++

The scorecard is in the repository labeled `LGTM++ over Packages.csv`. The false positive rate is 0.10, the true positive rate is 0.66, and the corresponding score is 0.56. The query used are in the json `LGTM++.json` and is `customCodeInjection2.ql`.

| **CWE**               | **TP**     | **FN**     | **TN**      | **FP**     | **Total**           | **TPR**                        | **FPR**                         | **Score**            |
| --------------------- | ---------- | ---------- | ----------- | ---------- | ------------------- | ------------------------------ | ------------------------------- | -------------------- |
| **94:Code injection** | 4          | 2          | 43          | 5          | 54                  | 0.6666666666666670             | 0.10416666666666700             | 0.5625               |
|                       | Total TP:4 | Total FN:2 | Total TN:43 | Total FP:5 | Total Test Cases:54 | Average TPR:0.6666666666666666 | Average FPR:0.10416666666666667 | Average Score:0.5625 |

## SinkFinder

The scorecard is in the repository labeled `SinkFinder over Packages.csv`. The false positive rate is 0.66, the true positive rate is 1, and the corresponding score is 0.34. This tool was run over the same package list and manually counted.

| **CWE**               | **TP**     | **FN**     | **TN**      | **FP**      | **Total**            | **TPR**       | **FPR**          | **Score**          |
| --------------------- | ---------- | ---------- | ----------- | ----------- | -------------------- | ------------- | ---------------- | ------------------ |
| **94:Code injection** | 6          | 0          | 34          | 66          | 106                  | 1             | 0.66             | 0.34               |
|                       | Total TP:6 | Total FN:0 | Total TN:34 | Total FP:66 | Total Test Cases:106 | Average TPR:1 | Average FPR:0.66 | Average Score:0.34 |

# Include the Raw DATA
