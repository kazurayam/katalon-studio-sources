# katalon-studio-sources

## What this project does

This project creates a folder tree as follows

Please note the following 2 directories:

- `versions`  which contains the source codes of Katalon Studio of various versions
- `build/compareVersions` which contains the "diff" information of Katalon Studio versions generated out of files under the `versions` directory

```
$ tree . -L 8
.
├── README.md
├── build
│   ├── compareVersions
│   │   └── 9.7.6_10.3.1
│   │       ├── diff
│   │       │   └── 9.7.6_10.3.1
│   │       │       └── resources
│   │       │           ├── extentions
│   │       │           │   └── scripts
│   │       │           └── source
│   │       │               ├── com.kms.katalon.core
│   │       │               └── com.kms.katalon.core.webui
│   │       └── differences.json
│   ├── nameStatusList.txt
│   ├── reports
│   │   └── problems
│   │       └── problems-report.html
│   └── tmp
...
└── versions
    ├── 10.3.1
    │   └── resources
    │       ├── extentions
    │       │   └── scripts
    │       │       └── smart-wait.js
    │       └── source
    │           ├── com.kms.katalon.core
    │           │   ├── META-INF
    │           │   │   ├── MANIFEST.MF
    │           │   │   └── maven
    │           │   │       └── com.kms
    │           │   └── com
    │           │       └── kms
    │           │           └── katalon
    │           └── com.kms.katalon.core.webui
    │               ├── META-INF
    │               │   ├── MANIFEST.MF
    │               │   └── maven
    │               │       └── com.kms
    │               └── com
    │                   └── kms
    │                       └── katalon
    └── 9.7.6
        └── resources
            ├── extentions
            │   └── scripts
            │       └── smart-wait.js
            └── source
                ├── com.kms.katalon.core
                │   ├── META-INF
                │   │   ├── MANIFEST.MF
                │   │   └── maven
                │   │       └── com.kms
                │   └── com
                │       └── kms
                │           └── katalon
                └── com.kms.katalon.core.webui
                    ├── META-INF
                    │   ├── MANIFEST.MF
                    │   └── maven
                    │       └── com.kms
                    └── com
                        └── kms
                            └── katalon

56 directories, 18 files
```

## custom Gradle tasks

This is a small Gradle project of which `build.gradle` provides 2 custom tasks:

1. `extractVersion`
2. `compareVersions`

### extractVersion task

- This task assumes that a version of Katalon Studio Free is installed on the machine.
- The task assumes that I explicitly declare the version number as `ext.KATALONSTUDIO_INSTALLATION_VERSION` in the `ext { ... }` section in the `build.gradle`
- run the task in the Terminal app as follows:
```
$ cd $katalon-studio-sources
$ gradle extractVersion
```
- The task will copy the source files of my interest out of the installation directory into the directory named `<projectDir>/versions/${KATALONSTUDIO_INSTALLTION_VERSION}`.
- I am interested in the Groovy source codes of the package `com.kms.katalon.core.webui` mainly, and supplementally `com.kms.katalon.core`. Also I am interested in the JavaScript codes that implements so called "Smart Wait" which sometimes causes performance issues.

### compareVersions task

- This task assumes that I specify 2 properties `ext.COMPARE_VERSION_A` and `ext.compare_VERSION_B`. For example,
```
ext {
    KATALONSTUDIO_INSTALLATION_VERSION = '10.3.1'
    COMPARE_VERSION_A = '9.7.6'
    COMPARE_VERSION_B = '10.3.1'
    ...
```

- This task assumes that I have extracted the source codes of 2 versions and created directory/file tree under the `<projectDir>/versions/${COMPARE_VERSION_A}` and `<projectDir>/versions/${COMPARE_VERSION_B}`. This must be done before I run the `compareVersions` task
- The task will compare 2 directory/file trees. It will output the result under the `<projectDir>/build/compareVersions/${COMPARE_VERSION_A}_${COMPARE_VERSION_B}` directory.
- The output includes 2 types of diff reports. The `compareVersions/${COMPARE_VERSION_A}_${COMPARE_VERSION_B}/diff` directory contains a file tree where each files are actuall the "unified-diff" formatted texts. The unified-diff tells you every detail information of source codes. Additionally, the task generates a JSON file named `compareVersions/${COMPARE_VERSION_A}_${COMPARE_VERSION_B}/differences.json` which summerizes the differences of 2 trees of versions.
- The compareVersions task emitts messages in the console as this:
```
> Task :compareVersions
dirA: /Users/kazuakiurayama/katalon-workspace/katalon-studio-sources/versions/9.7.6
dirB: /Users/kazuakiurayama/katalon-workspace/katalon-studio-sources/versions/10.3.1
---------------------------------
filesOnlyInA: 35 files
filesOnlyInB: 82 files
intersection: 531 files
modifiedFiles: 165 files
output at /Users/kazuakiurayama/katalon-workspace/katalon-studio-sources/build/compareVersions/9.7.6_10.3.1/differences.json
```
The number of 'modifiedFiles' will be usefull to know how much the differences the 2 versions to compare have.
