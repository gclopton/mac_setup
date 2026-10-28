

| Action                             | Command                              |     |
| ---------------------------------- | ------------------------------------ | --- |
| **Jump (cd)**                      | `nav <alias>`                        |     |
| **Create alias**                   | `nav create <path> <alias>`          |     |
| create alias for current directory | `nav create $(pwd) alias`            |     |
| **Show** path + tags               | `nav <alias> show`                   |     |
| **Change** alias/path              | `nav <alias> change`                 |     |
| **Delete** alias                   | `nav <alias> del`                    |     |
| **Add tags**                       | `nav <alias> tag add <tag…>`         |     |
| **Remove tags**                    | `nav <alias> tag rm <tag…>`          |     |
| **List by tag**                    | `nav list <tag>`                     |     |
| **Search** substring               | `nav search <text>`                  |     |
| **Copy path to clipboard**         | `nav copy <alias>`                   |     |
| **Open in Finder/Nautilus**        | `nav open <alias>`                   |     |
| **Alphabetize file**               | `nav alphabetize`                    |     |
| **Help**                           | `nav -h` / `nav --help` / `nav help` |     |



### Special behavior on `create`

* Verifies directory exists; offers to `mkdir -p` if missing.
* Detects duplicate alias or duplicate path and lets you decide what to do.





## 3  PSV file format

```
alias|/absolute/path|tag1,tag2
```

Example:

```
sim|~/projects/semiconductor-sim|school,research
notes|~/Dropbox/Notes|
```

