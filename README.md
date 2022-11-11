
# GitHub Repository Search Utility

This repo includes primitive shell scripts that wrap the GitHub CLI to support
[code search](https://docs.github.com/en/rest/search#search-code) use cases such as finding references to a range of database tables
and columns across a code base.


## Usage

### Install Pre-requisites
The [github CLI utility](https://cli.github.com/) must be installed and configured to authenticate against your target repositories. See https://cli.github.com/ for more details.

### Prepare Inputs
1. Add a line-return-delimited list of repositories to the [repositories input file](input/repositories.txt)
```
acme-organisation/repository1
acme-organisation/repository2
```
2. Add a line-return-delimited list of table names to the [tables input file](input/tables.txt)
```
customer
product
```
3. For each entry in [tables input file](input/tables.txt) create a new file in the [tables directory](input/tables/) of the same name containing a line-return-delimited list of column names.
For example, [customer table file](input/tables/customer.txt) contains:
```
customer_identifier
email_address
first_name

```
### Execute Script
```
./find_db_usage_in_repos
```

### Use Outputs
One output CSV file for each repository is created in the [repositories output directory](output/).  

Each file is named after the repository without the organisation reference.

Each output file contains the following fields:
1. Name of the database table
2. Name of the database table column
3. Name of the GitHub repository
4. Name of the file containing the table and column name
5. Path of the file in the target repository

For example:
```
customer,id_customer,acme-organisation/repository1,my_big_query.sql,materialized_views/subfolder1/subfolder2/my_big_query.sql
```
The output files can then be optionally combined and imported into Excel for further analyses.
The composite primary key in each file comprises: (table name + column name + repository name).

### Limitations
* The script performs a primitive search for the name of the table AND the name of each column in a single search operation. This primitive approach is likely to produce false positives and as such any list of files should be reviewed manually.
* The GitHub API has [specific rate limits for search](https://docs.github.com/en/rest/rate-limit) operations and you should override the `RATE_LIMIT_SLEEP_INTERVAL_SEC` variable as necessary.

### Future Extensions
* To reduce the false positive rate of search results, the [exact GitHub code search](https://docs.github.com/en/search-github/github-code-search/understanding-github-code-search-syntax#query-for-an-exact-match) functionality should be considered when it becomes GA.
