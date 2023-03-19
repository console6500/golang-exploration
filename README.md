# # 02_05 CI for Go

## Steps for Golang CI
- Create repo
- add files from exercise files
- click actions tab
- no choices under continuous integration that pop out; Go project is suggested.
- select go under suggested actions
- add `workflow_dispatch` to the triggers
- Give the workflow permission to write to the Actions summary.  Add the following under "runs_on"...
```
    permissions:
      checks: write
```

- Use `go.mod` to determine the version of go instead of setting it in the workflow; Cache the libraries to speed up workflow runs. Change "Set up Go" to:
```
    - name: Set up Go
      uses: actions/setup-go@v3
      with:
        go-version-file: 'go.mod'
        cache: true
```

- Instead of building the package; install `gotest.tools/gotestsum` for testing.  Change "Build" step to:
```
    - name: Build
      run: |
        go install gotest.tools/gotestsum@latest
```

- Use `gotest.tools/gotestsum`.  Change "Test..." call to:
```
gotestsum --format=standard-verbose --junitfile=junit.xml
```

-- Publish test reports.  Add the following job at the end:
```
    - name: Publish Test Report
      uses: mikepenz/action-junit-report@v3
      if: success() || failure() # always run even if the previous step fails
      with:
        report_paths: '**/junit.xml'
        detailed_summary: true
        include_passed: true
```
- commit new file directly to main branch
- go to actions tab
