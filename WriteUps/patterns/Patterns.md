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

# AST Patterns

### Identifier or SequenceExpression
This code looks for a variable decloration or sequence of variables/functions in a set with a specific name
```
if (node.type === 'VariableDeclaration'){
          //console.log(node.declarations[3].id);
          if(node.declarations[0].id.name === name) {
            if (node.declarations[0].init) {
              if (node.declarations[0].init.type === 'CallExpression'){
                switch (node.declarations[0].init.callee.type) {
                  case 'Identifier':
                  if(node.declarations[0].init.callee.name === method) {
                    result.push(packageData(node.declarations[0].init.callee, node.declarations[0].init.arguments, p));
                  }
                  break;
                  case 'MemberExpression':
                  if(node.declarations[0].init.callee.property.name === method) {
                    result.push(packageData(node.declarations[0].init.callee.object, node.declarations[0].init.arguments, p));
                  }
                  break;
                }
              }
            }
          }else if(node.declarations[0].init){//used for function calls with a name
              if (node.declarations[0].init.type === 'FunctionExpression') {
                for (var i = 0; i < node.declarations[0].init.params.length; i++) {
                  if (node.declarations[0].init.params[i].name === name) {
                    let argVal = methodFinder(tree, 'f', method, node.declarations[0].id.name, i);
                    let argRes = methodFinder(tree, type, method, argVal[0]);
                    result = argRes;
                  }
                }
              }
            }
          }else if (node.type === 'ExpressionStatement'){
            if (node.expression.type === 'CallExpression') {
              if (node.expression.callee.type === 'FunctionExpression') {//used for lambda function calls
                for (var i = 0; i < node.expression.callee.params.length; i++) {
                  if (node.expression.callee.params[i].name === name){
                    result = methodFinder(tree, type, method, node.expression.arguments[i].name);
                  }
                }
              }
            }
          }
```
### CallExpression
This code looks for a call expression with a specific name
```
if (node.type === 'CallExpression'){
          switch (node.callee.type) {
            case 'Identifier':
            if(node.callee.name === method) {
              if (!node.callee.object) {
                result.push(packageData(node.callee, node.arguments, node.callee));
              }else if (node.callee.object.type === 'Identifier') {
                result.push(packageData(node.callee.object, node.arguments, tree));
              }else if (node.callee.object.type === 'CallExpression') {
                result.push(packageData(node.callee.object, node.arguments, node.callee));
              }
            }
            break;
            case 'MemberExpression':
            if(node.callee.property.name === method || node.callee.property.value === method) {
              if (!node.callee.object) {
                result.push(packageData(node.callee, node.arguments, node.callee));
              }else if (node.callee.object.type === 'Identifier') {
                result.push(packageData(node.callee.object, node.arguments, tree));
              }else if (node.callee.object.type === 'CallExpression') {
                result.push(packageData(node.callee.object, node.arguments, node.callee));
              }else if (node.callee.object.type === 'MemberExpression') {
                result.push(packageData(node.callee.object, node.arguments, tree));
              }
            }
            break;
            case 'SequenceExpression':
              for (var k= 0; k < node.callee.expressions.length; k++) {
                if (node.callee.expressions[k].name === method) {
                  result.push(packageData(node.callee, node.arguments, tree));
                }
              }
              break;

          }
        } else if (node.type === 'Identifier' && node.name === method && p.type ) {
          if (p.type === 'MemberExpression') {
              result.push(packageData(p.object, [{type: 'Nothing'}], p));
          }
        }
```
### MemberExpression
This code looks for a variable defined in another file
```
if (node.type === 'VariableDeclaration'){
          if(node.declarations[0].id.name === name[0]) {
            if (node.declarations[0].init.callee) {
              //console.log(node.declarations[0].init.callee);
              if(node.declarations[0].init.callee.name === 'require') {
                let str = node.declarations[0].init.arguments[0].value;
                if (str.includes("/")) {
                  if (str[0] === '.' && str[1] !== '.') {
                    str = path.resolve(dir, str.substring(2));
                  }else if(str[0] === '.' && str[1] === '.'){
                    let holder = path.parse(dir).dir;
                    str = path.resolve(holder, str.substring(3))
                  }
                  let newAST = buildLinkedASTFromFile(str);
                  let findings = methodFinder(newAST, 'e', method, name[1]);
                  if (typeof findings[0] === 'string') {
                    result = methodFinder(newAST, 'm', method, findings[0]);
                  } else{
                    result = findings;
                  }
                }
              }
            }
          }
        }
```
The following code is used to help with the code right above for type "e":
```
          if (node.type === 'ExpressionStatement') {
            if (node.expression.type === 'AssignmentExpression') {
              if (node.expression.right.type === 'ObjectExpression') {
                for (var i = 0; i < node.expression.right.properties.length; i++) {
                  if (node.expression.right.properties[i].key.name === name) {
                    switch (node.expression.right.properties[i].value.type) {
                      case 'CallExpression':
                      if (node.expression.right.properties[i].callee.property.name === method) {
                        result.push(packageData(node.expression.right.properties[i].value.callee.object, node.expression.right.properties[i].value.arguments, tree));
                      }
                      break;
                      case 'Identifier':
                      result.push(node.expression.right.properties[i].value.name);
                      break;
                    }
                  }
                }
              }else if (node.expression.left.property.name === name) {
                switch (node.expression.right.type) {
                  case 'CallExpression':
                  if (node.expression.right.callee.property.name === method) {
                    result.push(packageData(node.expression.right.callee.object, node.expression.right.arguments, tree));
                  }
                  break;
                  case 'Identifier':
                  result.push(node.expression.right.name);
                  break;
                }
              }
            }
          }
```
