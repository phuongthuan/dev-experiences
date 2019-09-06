### 1. Get list of globally installed packages

```
npm list -g --depth 0
```
- **npm**: the Node package manager command line tool
- **list -g**: display a tree of every package found in the user’s folders (without the -g option it only shows the current directory’s packages)
- **depth 0 / — depth=0**: avoid including every package’s dependencies in the tree view

