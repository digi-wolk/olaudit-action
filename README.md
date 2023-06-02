# About

Github Action for OSS license auditor (olAudit)

# Usage

```yaml
name: OSS License Auditor

on:
  pull_request:
    branches: [ master ]

jobs:
  oss-license-auditor:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: OSS License Auditor
      uses: digi-wolk/olaudit@v1
      with:
        path: .
```

# Inputs

| Name | Description | Required | Default |
| --- | --- | --- | --- |
| path | Path to the project root directory | true | . |


