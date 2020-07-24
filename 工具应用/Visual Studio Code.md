# Visual Studio Code

1. 调试 Webpack

   ```json
   {
       // Use IntelliSense to learn about possible attributes.
       // Hover to view descriptions of existing attributes.
       // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
       "version": "0.2.0",
       "configurations": [
           {
               "type": "node",
               "request": "launch",
               "name": "webpack debugger",
               "runtimeExecutable": "npm",
               "runtimeArgs": [
                   "run",
                   "build"
               ],
               "port": 5000
           }
       ]
   }
   // runtimeExecutable: 程序执行器，默认是 node，如果是用 npm 启动，就需要设置成 "npm"
   // runtimeArgs: 传递给程序执行器的参数。比如 npm run build，参数就是 run 和 build
   // port: node 调试端口号，与 package.json 的 scripts 中配的 --inspect-brk 保持一致
   ```

   

2. 2