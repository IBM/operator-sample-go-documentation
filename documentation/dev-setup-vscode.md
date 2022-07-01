# Setup Visual Studio Code Environment

In the documentation it is recommended to work directly in each operator subfolder, e.g:

```shell
cd operator-sample-go
code .
```

or

```shell
cd operator-database
make install run
```

This method has the advantage of working only with the relevant code. If you prefer to access all folders in a single VSCode window, you can create a workspace and add each application subfloder to the workspace.

- [ ] First, create a new workspace.

![buildworkspace](./images/buildworkspace.png)

- [ ] Add folders to the workspace.

![addfoldertoworkspace](./images/addfoldertoworkspace.png)

Note, opening the root folder 'operator-sample-go' in VSCode will result in import errors.