
# Go Modules

Go team introduces, go modules(a new dependency management system) with GO 1.11 version. Go module introduced as an alternative to GOPATH for package distribution.
Go module does not replace GOPATH but only replaces GOPATH for version control and package distribution.

Go use an environment variable GO111MODULE, to enable(_GO111MODULE=on/off_) Go Module feature. 


### Importing packages
Any third party package needs to download first before using it in programs.

- Without Go module  
Go runtime download packages in Go workspace i.e. _$GOPATH/src_
  _GO111MODULE=off_ uses legacy GOPATH and download packages in _$GOPATH/src_ 

- With Go module  
Go runtime still download packages in GOPATH, but structure is different for Go modules. Pakcages are downloaded in _$GOPATH/pkg/mod_
  

Any projects/programs can run even if project/program is outside of $GOPATH. GOPATH dependency comes due to subpackages. To identify subpackages, Go runtime will search in GOROOT, GOPATH.

### With GO Modules
With Go modules, projects need not be in $GOPATH. We just need to initialize go module and and use that path for subpackages.
```
go mod init github.com/gomodtest		# This will create go.mod file
						# Modules can be imported as _import github.com/gomodtest/module1_
```

*GO111MODULE values*
GO111MODULE vales can be _on/off/auto_
1. _on_   : Forces Go runtime to always use Go modules, whether project is in GOPATH or not
2. _off_  : Go runtime behaves in a legacy GOPATH way
3. _auto_ : Default mode. 
            When outside of GOPATH, behaves as if GO111MODULE is set
            When inside of GOPATH, behaves as if GO111MODULE is unset

If Go module is not initialzed i.e. no go.mod files is present in project, then legacy GOPATH will be used.

Go Modules can also be used to maintain multiple package versions i.e.
```
go get golang.org/x/crypto@latest
go get golang.org/x/crypto@v0.0.0-20201203163018-be400aefbc4c
```

#### Refer local package paths
Go module also supports another feature to replace import packages used in project with the given package.
Replace can be defined in go.mod file as:
```
module github.com/gomodtest

replace github.com/gomodtest/config => /home/user/oustide/gopath/config

```

Repace statement can also be specified via command line as:
```
$ go mod edit -replace github.com/gomodtest/config=/home/user/oustide/gopath/config
```

NOTE: Target location in replace, should have go.mod file present. If not present, then create one with go mod init.

If a version is present on the left side of the arrow (=>), only that specific version of the module is replaced; other versions will be accessed normally.  
If the left version is omitted, all versions of the module are replaced.

_replace_ directives only apply in the main module's go.mod file and are ignored in other modules.

```
replace golang.org/x/net v1.2.3 => example.com/fork/net v1.4.5

replace (
    golang.org/x/net v1.2.3 => example.com/fork/net v1.4.5
    golang.org/x/net => example.com/fork/net v1.4.5
    golang.org/x/net v1.2.3 => ./fork/net
    golang.org/x/net => ./fork/net
)
```
