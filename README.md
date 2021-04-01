# db\* CODECOP Command Line

## Introduction

Trivadis db\* CODECOP is a command line utility to check Oracle SQL*Plus files for compliance violations of the [Trivadis PL/SQL & SQL Coding Guidelines Version 4.0](https://trivadis.github.io/plsql-and-sql-coding-guidelines/v4.0/).

Furthermore McCabe’s cyclomatic complexity, Halstead’s volume, the maintainability index and some other software metrics are calculated for each PL/SQL unit and aggregated on file level.

The code checking results are stored in XML, HTML and Excel files for further processing.

To get the most out of this command line utility you should make it part of your Continuous Integration environment by using the [db\* CODECOP for SonarQube](https://github.com/Trivadis/plsql-cop-sonar) plugin. This way you may control the quality of your code base over time.

Have also a look at [db\* CODECOP for SQL Developer](https://github.com/Trivadis/plsql-cop-sqldev) if you are interested to check the code quality of PL/SQL code within SQL Developer. It’s a free extension.

db\* CODECOP supports custom validators. A validator must implement the `PLSQLCopValidator` Java interface and has to be a direct or indirect descendant of the `PLSQLJavaValidator` class.

You may use these validators as is or amend/extend them to suit your needs.

## Examples

Here are some screen shot taken from an of an HTML report based on the samples provided with db\* CODECOP.

![Processing & Content](images/plsqlcop_processing.png)
![Issue Overview](images/plsqlcop_issues_overview.png)
![Complex PL/SQL Units](images/plsqlcop_complex_plsql_units.png)
![File Overview](images/plsqlcop_file_overview.png)
![File Issues](images/plsqlcop_file_issues.png)

These [HTML](https://trivadis.github.io/plsql-cop-cli/tvdcc_report.html) and [Excel](https://trivadis.github.io/plsql-cop-cli/tvdcc_report.xlsx) reports have been created by db\* CODECOP and are based on a simple set of good and bad example files distributed with db\* CODECOP.

## Custom Guidelines as db\* CODECOP Plugins

Since version 2.0 db\* CODECOP supports custom validators. Such a validator must implement the PLSQLCopValidator Java interface and has to be a direct or indirect descendant of the PLSQLJavaValidator class. To simplify the development of a validator an example is provided as Maven project in a dedicated [GitHub repository](https://github.com/Trivadis/cop-validators). This example validator implements 15 guidelines to cover the [chapter 2.2 Naming Conventions of the Trivadis PL/SQL & SQL Coding Guidelines](https://trivadis.github.io/plsql-and-sql-coding-guidelines/2-naming-conventions/naming-conventions/). The following screenshot shows how checks are implemented.

![File Issues](images/plsqlcop_custom_validator.png)

## PL/SQL Editor for Eclipse

The following screenshot shows the test case for the checkConstantName method. The outline of the editor reveals the class name to be used for the check.

![PL/SQL Editor](images/plsqlcop_editor.png)

The editor is mainly provided to understand the PL/SQL model better. The full model is available as PLSQL.ecore file, which may be explored best within the Eclipse IDE.

## Releases

You find all releases and release information [here](https://github.com/Trivadis/plsql-cop-cli/releases).

## Installation

1. Uncompress the distributed db\* CODECOP archive file into a folder of your choice. 

    The archive file contains the following files in the root directory: `ReadMe.html`, `tvdcc.jar`, `tvdcc.sh`, `tvdcc.cmd`, `PLSQL-and-SQL-Coding-Guidelines.html` and the subdirectories `lib`, `eclipse`, `plugin` and `sample`. SQL scripts with good and bad examples for every guideline are provided in the sample directory.

2. Amend the settings for `JAVA_HOME` in tvdcc.cmd to meet your environment settings.

    This is necessary on Windows only, and only if the `java.exe` cannot be found in the path. Use at least a Java 8 runtime environment (JRE) or development kit (JDK) for JAVA_HOME.

3. Include `TVDCC_HOME` in your PATH environment variable for handy interactive usage.

4. Optionally copy your commercial license file into the `TVDCC_HOME` directory. For simplicity name the file `tvdcc.lic`.

5. Ignoring JDK warnings

    When using JDK 11 the following warnings are thrown:

    ```
    WARNING: An illegal reflective access operation has occurred
    WARNING: Illegal reflective access by com.google.inject.internal.cglib.core.$ReflectUtils$2 (file:/usr/local/bin/tvdcc/lib/guice-3.0.jar) to method java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain)
    WARNING: Please consider reporting this to the maintainers of com.google.inject.internal.cglib.core.$ReflectUtils$2
    WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
    WARNING: All illegal access operations will be denied in a future release
    ```

    This message is caused by https://github.com/google/guice/issues/1133 . It should be fixed in Guice 5.0.1. In future releases this warning should disappear, when our third party libraries have updated their dependencies. For the time being you have to ignore this warning.

    When using JDK 16 the following error is thrown:

    ```
    Exception in thread "main" java.lang.ExceptionInInitializerError
      at com.trivadis.tvdcc.CodeChecker.validateFiles(CodeChecker.java:225)
      at com.trivadis.tvdcc.CodeChecker.doCodeCheck(CodeChecker.java:150)
      at com.trivadis.tvdcc.CodeChecker.mainFunction(CodeChecker.java:64)
      at com.trivadis.tvdcc.CodeChecker.main(CodeChecker.java:43)
    Caused by: com.google.inject.internal.util.$ComputationException: com.google.inject.internal.util.$ComputationException: java.lang.ExceptionInInitializerError
    ```

    To fix the issue you have to add the JVM argument `--illegal-access=warn` in the `tvdcc.sh` and `tvdcc.cmd`. This will show a warning as in earlier JDK versions.

## Usage

If db\* CODECOP is invoked without arguments, the following help screen is shown, after the copyright information:

```
usage: tvdcc path=<path> [options]

mandatory arguments: 
  path=<path>            path to directory containing files to be checked, e.g ./mysourceDir

options: 
  -help, -h, -?          print this message
  filter=<filter>        regular expression to filter files, e.g. "myfile.sql" or "(pks|pkb|pkg)$" or "^((?!guideline_7210_65).)*$"
  timeout=<seconds>      maximum seconds allowed to load and parse a file, e.g. 10
  complexity=<value>     list PL/SQL units with cyclomatic complexity greater than this value, e.g. 10
  output=<name>          file name incl. path of output file, e.g ./myreport.html or ./myreport.xlsx
  template=<name>        file name incl. path of XSL stylesheet to create HTML output, e.g ./myhtml.xsl
  excel={true|false}     create Excel output file, default is true
  html={true|false}      create HTML output file, default is true
  transonly={true|false} transform temporary XML file only, default is false
  cleanup={true|false}   remove temporary XML file, default is true
  check=<list>           comma separated list of guidelines, severities, characteristics to be checked,
                         e.g. "blocker, critical, major, efficiency, 1040", default all guidelines
                         guidelines: 1010, 1020, 1030, 1040, ..., 8410, 8510
                         severities: blocker, critical, major, minor, info
                         characteristics: changeability, efficiency, maintainability, portability,
                                          reliability, reusability, security, testability
                         invalid values are ignored
  skip=<list>            comma separated list of guidelines, serverities, characteristics not to be checked,
                         e.g. "7460, info, portability, testability", invalid values are ignored
  nosonar=false          ignore "-- NOSONAR" marker comments (do not suppress warnings)
  license=<name>         name incl. path to license file, default tvdcc.lic in the root folder
  propertyfile=<name>    load properties from file, e.g. ./tvdcc.properties
  validator=<name>       decendent of PLSQLJavaValidator and implements PLSQLCopValidator interface, 
                         default is com.trivadis.tvdcc.validators.TrivadisGuidelines3
  genmodel={true|false}  generate SonarQube XML model files, default is false.
```

Please note that db\* CODECOP applies by default a filter to support the following file extensions: `.sql`, `.prc`, `.fnc`, `.pks`, `.pkb`, `.trg`, `.vw`, `.tps`, `.tbp`, `.plb`, `.pls`, `.rcv`, `.spc`, `.typ`, `.aqt`, `.aqp`, `.ctx`, `.dbl`, `.tab`, `.dim`, `.snp`, `.con`, `.collt`, `.seq`, `.syn`, `.grt`, `.sp`, `.spb`, `.sps`, `.pck`. 

The value of all options will be included in the output files and on the console output. 

## Issues
Please file your bug reports, enhancement requests, questions and other support requests within [Github's issue tracker](https://help.github.com/articles/about-issues/).

* [Questions](https://github.com/trivadis/plsql-cop-cli/issues?q=is%3Aissue+label%3Aquestion)
* [Open enhancements](https://github.com/trivadis/plsql-cop-cli/issues?q=is%3Aopen+is%3Aissue+label%3Aenhancement)
* [Open bugs](https://github.com/trivadis/plsql-cop-cli/issues?q=is%3Aopen+is%3Aissue+label%3Abug)
* [Submit new issue](https://github.com/trivadis/plsql-cop-cli/issues/new)

## Frequently Asked Questions

see [Frequently Ased Questions](FAQ.md).

## Further Information

Please find further information about db\* CODECOP on the [Trivadis](https://www.trivadis.com/en/plsql-cop) website.

## License

The preview/trial version of db\* CODECOP is licensed under the Creative Commons Attribution-NonCommercial-NoDerivs 3.0 Unported License. You may obtain a copy of the License at https://creativecommons.org/licenses/by-nc-nd/3.0/.

![CC-BY_NC-ND](images/CC-BY-NC-ND.png)

The trial/preview version provides full functionality but is limited in time and volume.

For production use a separate software license agreement is required.
