# Queries Report

This report shows the results of the LGTM query set versus our LGTM++ query set. The SinkFinder's results are also included.

## Results Summary
![](./TOPackages.png)

The tools being used are our additional queries (LGTM++), the built in queries from codeQL (LGTM), and the SinkFinder we've developed previously. The included packages are:

- the list of "50" from Comcast  (49 really) (except for 2 that wouldn't compile)
- 3 additional packages from CVEs we found to be relevant.

In total, there are therefore 50 benchmarks. 

**Bottom line**: LGTM-Basic and LGTM++ have about the same expressive power now. Slight advantage to LGTM++ with a lower false positive rate. Not all queries that were in SinkFinder have been ported over (Roughly: only 3 in 9 were ported). 

### Queries

#### Classic Sink Finder

AST pattern matching for eval and the obfuscated variants. Roughly 9 patterns. 


#### LGTM

List of queries included.
- `CodeInjection.ql` - LGTM query designed for Code Injection. (`ql/security` folder of LGTM distribution). However it does not find anything from the benchmark list. Since it was designed for specific vulnerabilities, additional benchmarks must be added to demonstrate its value. 
- `Eval.ql` - LGTM query designed for finding `Eval`.

#### LGTM++
This is not a superset of LGTM. It is a replacement.

list of queries included
- `customCodeInjection.ql` - Our custom query for finding eval.
- `customCodeInjection2.ql` - a variant of the previous query.

LGTM++ finds the save vulnerabilities as the default LGTM

## Scoring

Each tool was scored using our benchmark's scorecard generator process. This is process that mimic's OWASP's scoring schema. The automation script runs the queries over each package and outputs each result (empty file if nothing is found) in a csv file. The scorecard script reads each file and counts the true/false positives/negatives. The index.csv indicates the location of of all true positives in the packages to help with scoring.

**Recall formulas for the rates**

- true positive rate = TP / (TP+FN)
- false positive rate = FP / (FP + TN)

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

### LGTM

| **Use of eval** | **The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used.** | **recommendation** | **Do not use eval or the Function constructor.** | **/backbone-localstorage-0.3.2/package/test/vendor/jquery.js** | **612** | **5** | **612** | **41** |
| --------------- | ------------------------------------------------------------ | ------------------ | ------------------------------------------------ | ------------------------------------------------------------ | ------- | ----- | ------- | ------ |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /flot-3.2.5/package/source/jquery.js                         | 565     | 5     | 565     | 41     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /twitter-bootstrap-2.1.1/package/docs/assets/js/jquery.js    | 2       | 11362 | 2       | 11377  |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /twitter-bootstrap-2.1.1/package/js/tests/vendor/jquery.js   | 2       | 11362 | 2       | 11377  |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /angular-1.7.8/package/angular.js                            | 1289    | 7     | 1289    | 22     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /angular-1.7.8/package/angular.js                            | 16418   | 15    | 16422   | 17     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /backbone-localstorage-0.3.2/package/test/vendor/jquery.js   | 569     | 15    | 569     | 46     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /backbone-localstorage-0.3.2/package/test/vendor/underscore.js | 1206    | 16    | 1206    | 68     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /depot-0.1.6/package/specs/vendor/chai.js                    | 2333    | 34    | 2333    | 60     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /flot-3.2.5/package/source/jquery.js                         | 524     | 13    | 524     | 44     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /jade-1.11.0/package/bin/jade.js                             | 78      | 12    | 78      | 40     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /jade-1.11.0/package/jade.js                                 | 220     | 8     | 220     | 39     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /jade-1.11.0/package/jade.js                                 | 4510    | 5     | 4510    | 67     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /jade-1.11.0/package/jade.js                                 | 6252    | 10    | 6252    | 31     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /jade-1.11.0/package/lib/index.js                            | 218     | 8     | 218     | 39     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /jquery-migrate-3.1.0/package/test/data/jquery-2.2.3.js      | 343     | 5     | 343     | 20     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /jsdiff-1.1.1/package/readme.js                              | 9       | 36    | 9       | 77     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /prototype-0.0.5/package/lib/String.js                       | 100     | 64    | 100     | 75     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /prototype-0.0.5/package/lib/String.js                       | 201     | 46    | 201     | 67     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /twitter-bootstrap-2.1.1/package/docs/assets/js/jquery.js    | 2       | 10894 | 2       | 10918  |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /twitter-bootstrap-2.1.1/package/js/tests/phantom.js         | 11      | 54    | 11      | 65     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /twitter-bootstrap-2.1.1/package/js/tests/phantom.js         | 19      | 44    | 19      | 56     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /twitter-bootstrap-2.1.1/package/js/tests/vendor/jquery.js   | 2       | 10894 | 2       | 10918  |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /vis-4.21.0-EOL/package/dist/vis.js                          | 34455   | 40    | 34455   | 55     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /vis-4.21.0-EOL/package/docs/js/ie-emulation-modes-warning.js | 28      | 26    | 28      | 79     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /vis-4.21.0-EOL/package/examples/graph3d/playground/playground.js | 386     | 3     | 386     | 10     |
| **Use of eval** | The 'eval' function and the 'Function' constructor execute strings as code. This is dangerous and impedes program analysis and understanding. Consequently, these two functions should not be used. | recommendation     | Do not use eval or the Function constructor.     | /vis-4.21.0-EOL/package/examples/timeline/other/requirejs/scripts/require.js | 35      | 39    | 35      | 45     |

### LGTM++

| **Code injection custom 2** | **Interpreting unsanitized user input as code allows a malicious user arbitrary code execution.** | **warning** | **Here** | **/depot-0.1.6/package/specs/vendor/chai.js**                | **2333** | **34** | **2333** | **60** |
| --------------------------- | ------------------------------------------------------------ | ----------- | -------- | ------------------------------------------------------------ | -------- | ------ | -------- | ------ |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /jade-1.11.0/package/bin/jade.js                             | 78       | 12     | 78       | 40     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /jade-1.11.0/package/jade.js                                 | 4510     | 5      | 4510     | 67     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /prototype-0.0.5/package/lib/String.js                       | 100      | 64     | 100      | 75     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /prototype-0.0.5/package/lib/String.js                       | 201      | 46     | 201      | 67     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /twitter-bootstrap-2.1.1/package/js/tests/phantom.js         | 11       | 54     | 11       | 65     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /twitter-bootstrap-2.1.1/package/js/tests/phantom.js         | 19       | 44     | 19       | 56     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /vis-4.21.0-EOL/package/dist/vis.js                          | 34455    | 40     | 34455    | 55     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /vis-4.21.0-EOL/package/examples/graph3d/playground/playground.js | 386      | 3      | 386      | 10     |
| **Code injection custom 2** | Interpreting unsanitized user input as code allows a malicious user arbitrary code execution. | warning     | Here     | /vis-4.21.0-EOL/package/examples/timeline/other/requirejs/scripts/require.js | 35       | 39     | 35       | 45     |