#Patterns
### Code Injection
```javascript
eval(data)
(1, eval) (data)
```
These are two calls to eval, one direct and one indirect. In both cases the callee is eval.

```javascript
window["eval"].call(window, data);
obj['eval'].call(data);
obj['eval'].apply(data);
obj['eval'](data);
```
These patterns use the main, window, or a custom object to call on eval. Eval is still a function call, but through a method call.

```javascript
setTimeout(data, 1000);
setInterval(data, 1000);
Function(){return data;};
```
These patterns are additional ways to run abritary code other than a call to eval within the JavaScript langauge. Function is a call expression with no name

### Command Injection
```javascript
const cp = require('child_process');
cp.exec('ls;'+ data);
```
This pattern imports the `child_process` library and uses the exec function to run system commands.

```javascript
const shell = require('shelljs');
shell.exec(data);
```
This pattern imports the `shelljs` library and uses the exec function to run system commands.

### SQL Injection
```javascript
require('mysql').createConnection({
     host: 'localhost',
     user: 'root',
     password: 'password',
     database: 'mydb',
     multipleStatements: true
}).query(data);
```
This pattern imports the `mysql` library, uses the method `createconnection` to connect to the sql server, which retruns an object. That object has a function `query` that can be exploited.