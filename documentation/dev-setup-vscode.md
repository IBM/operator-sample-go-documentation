# Setup Visual Studio Code Environment

Opening the root folder 'operator-sample-go' in VSCode will display import errors in the code.

This can be resolved by working directly in each operator subfolder, e.g:

```shell
cd operator-sample-go
code .
```

or

```shell
cd operator-database
make install run
```

This method has the advantage of working only with the relevant code. 

Alternatively, if you prefer to access all folders in a single VSCode window, you can create a workspace and add each application subfloder to it.

- [ ] First, create a new workspace

![buildworkspace](./images/buildworkspace.png)

- [ ] Add folders to the workspace

![addfoldertoworkspace](./images/addfoldertoworkspace.png)