# dbt Capstone Project

## Project Overview
This dbt project is a capstone project meant to provide an understanding of how to use Snowflake, dbt, Github, Jira, Hakkoda Recipe's and Accelerators in the context of a real project. You will be be taking a mock problem from the ingestion of raw data in a source all the way to its consumption by the customer Analysts. In doing so, you will...
- Participate in project ceremonies including a Kick-off, Sprint Planning, scrum and Sprint Review
- Manage your work in Jira
- Create a Snowflake-based Data Warehouse utilizing dbt Cloud including the creation of models, macros, implementing tests, unit testing your model, creating pull requests and more.
- Help you understand the roles on a project along with the responsibliites for that role (i.e. what does a Lead Engineer do? what does a Data Analyst do?) 

Lastly, we hope this helps you understand how the context of our teams work adds business value to the customers we serve. 

## Business Problem
Duffield Memorial Hospital needs the ability to analyze the encounters and identify trends in order to reduce length-of-stay. An encounter is defined as health care contact between the patient and the provider who is responsible for diagnosing and treating the patient. Currently this process is performed manually by Duffield Memorial analysts who extract data from the electronic medical record system and integrate the data in Excel to perform their trending. This process is time consuming and looking at historical trends is becoming more and more difficult for the team because of the size of the data and limitations of Excel. They need an automated solution that helps them look at trends. This solution should also include automated checks to improve reporting accuracy and provide the team some ability to incorporate new changes in logic without having to pass excel files back and forth. Some changes currently happen in master Excel spreadsheets while they're being shared with leadership which loses leadership's trust in the team.

## Our Solution
Hakkoda has been hired by the customer to implement a modern data tech stack including Snowflake, Azure Data Lake Storage, and dbt Cloud to solve the above business problem. As part of the solution, we should guide the customer's data team in implementing the tech stack utilizing industry best practices and ensuring the solution is scalable for solving subsequent use cases. 

This engagement will utilize Hakkoda's dbt implementation recipe. This recipe will inform the project roadmap and provide accelerators that the team can use to decrease time to deliver and time to value for the customer.

### Pre-project Checklist
Each team member should have the following access:
- [ ] Access to the Capstone Jira Project
- [ ] Snowflake Sandbox account (https://ht48847.canada-central.azure.snowflakecomputing.com/)
- [ ] Member of dbt Cloud Capstone Design Project
- [ ] Write Access to GitHub Capstone Design Project repo
- [ ] Access to Snowflake CAPSTONE database(s) including DEV, QA, and PROD environments
- [ ] Snowflake Role CAPSTONE_PROJECT_TEAM_ROLE
- [ ] (Optional) Hex or Sigma Sandbox access to create visuals

### Solution Architecture
The following high-level diagram describes how data will be ingested from the electronic medical record system and flat file sources, refined and curated for downstream analytics. The diagram is available in Miro [here](https://miro.com/app/board/uXjVP8NwfUY=/?share_link_id=497882965360) and show below:

![image](https://drive.google.com/uc?export=view&id=1HTpkoUYPm_hFyBnmwniO3chsTrnZU7Fn)

### Project Lifecycle
The following steps are document the lifecycle of a data engineering pipeline on our project from environment setup, ingesting data into Snowflake, transforming it and finally serving it up for analytics and other downstream uses. 

#### 1. Environment Setup & Configuration
Setup the Snowflake, dbt and Azure environments for the project. This will include configuring Azure & Snowflake objects to load FHIR bundles via Snowpipe to Snowflake, configuring dbt Cloud for the development team and deployment, and GitHub to create Data Warehouse releases. 

#### 2. Design Target Data Model
The following target data model is to be built via dbt Cloud. Use the naming conventions in the appendix for the Curated [Model Layers](#model-layers). Prototype views should be written for each target table for use in unit testing to check row counts and column values (See [Unit Testing](#unit-testing) in the appendix). For additional information on the projects use of keys, hashing and metadata columns see [Model Standards](#model-standards) in the appendix:

![image](https://drive.google.com/uc?export=view&id=17BpnvjbKltSnqMRaVSChKhbz94vo-jUc)

#### 3. Design Data Pipelines
The design of the dbt-based Data Pipelines are documented in the appendix below. This includes relevant packages, macros, models and testing. See [Model Organization](#model-organization) for more information. 

#### 4. Build Data Pipelines
The following steps should be followed for each step of the development process for building a dbt-based data pipeline and following the [Branching Strategy](#branching-strategy) outlined in the appendix:

##### a. Ingest data to Snowflake 
Ingest data into Snowflake via macros in the [dbt-external-tables](https://github.com/dbt-labs/dbt-external-tables/tree/0.8.2/) package. This will create a pipe using Snowflake objects Storage Integration, Notification Integration, File Format and Stage. Perform unit testing and include screen shots in your pull request to show how file(s) were initially loaded and then subsequent files were loaded during a second load. For additional details on ingesting data into Snowflake see [Ingestion Standards](#ingestion-standards) in the appendix.

##### b. Build Raw dbt model(s)
Raw data models will be created for each source table/file. Use the naming convention below in the [Model Layers](#model-layers) section of the appendix. Raw models should include testing for source freshness and descriptions for the purpose of each table. 

##### c. Build Refined dbt model(s)
Refined data models will be created to cleanse, standardize and integrate data from Raw data models. Use the naming convention below in the [Model Layers](#model-layers) section of the appendix. Refined models should include testing for uniqueness, not null fields and descriptions for the purpose of each model and calculated field. 

##### d. Build Curated dbt model(s)
Curated data models will be created to prepare data for consumption by analysts and other downstream uses. Use the naming convention below in the [Model Layers](#model-layers) section of the appendix. Curated models should include testing for uniqueness, referential integrity (where applicable), not null fields and descriptions for the purpose of each model and column. 

##### e. Build dbt job(s)
dbt Jobs will be created within the dbt Cloud UI to orchestrate Data Warehouse loads on a regular interval. The job(s) will be composed of a series of commands (i.e. `dbt run`, `dbt test`, and `dbt run-operation`) to load, test, and perform maintenance operations. See [Job Standards](#job-standards) for additional information on the types of jobs, environments and examples.

#### 5. Deploy Data Pipelines
The development lifecycle of the Data Warehouse will include 3 environments; Development (CAPSTONE_DEV), Quality Assurance (CAPSTONE_QA) and Production (CAPSTONE). Once development has been completed on a data pipeline (including unit testing & applying tests/documentation) the developer will create a [pull request](#pull-request-template) using the standards defined in the appendix and then the new feature or fix will follow the steps listed [here](#deployment-workflow) before making it into Production where it will be consumed with the analysts.

## Appendix

### dbt Packages  
Our dbt project will make use of the following dbt packages to expedite development and enforce consistency:

| Category | Description                                             |
|----------|---------------------------------------------------------|
| [dbt-external-tables](https://github.com/dbt-labs/dbt-external-tables)      | Contains macros to ingest data from cloud storage into Snowflake via external tables and pipes.        |
| [dbt-audit-helper](https://github.com/dbt-labs/dbt-audit-helper)  | Contains macros for audting your dbt models for unit testing purposes.        |
| [dbt-expectations](https://github.com/dbt-labs/dbt-audit-helper)  | Contains macros with additional tests for data quality purposes.        |
| [dbt-constraints](https://github.com/Snowflake-Labs/dbt_constraints) | Contains macros to create and test database constrainsts for informational & data quality purposes.        |


#### How to add a package to your dbt project?
1. Add a `packages.yml` file to your dbt project. This should be at the same level as your `dbt_project.yml` file. Specify the package(s) you wish to add using one of the supported syntaxes, for example:
  ```yml
  packages.yml
  packages:
    - package: dbt-labs/snowplow
      version: 0.7.0

    - git: "https://github.com/dbt-labs/dbt-utils.git"
      revision: 0.9.2

    - local: /opt/dbt/redshift
  ```  

2. Run `dbt deps` to install the package(s). Packages get installed in the dbt_packages directory – by default this directory is ignored by git, to avoid duplicating the source code for the package.

### Ingestion Standards

#### Preliminary steps before using dbt-external-tables
1. Review documentation for [automating Snowpipe for Azure Blob Storage](https://docs.snowflake.com/en/user-guide/data-load-snowpipe-auto-azure.html).

2. Create Storage and Notification integration objects. In the capstone project these objects have already been created and they can be referenced using the names below.

   Example:
    ```
    CREATE STORAGE INTEGRATION IF NOT EXISTS capstone_fhir_storage_int
    TYPE = EXTERNAL_STAGE
    STORAGE_PROVIDER = AZURE
    ENABLED = TRUE
    AZURE_TENANT_ID = '1acc27f8-8478-4411-a70d-8a7eca63c288'
    STORAGE_ALLOWED_LOCATIONS = ('https://sahakkodadatalake1.blob.core.windows.net/capstone/fhir_input/')
    ;

    CREATE OR REPLACE NOTIFICATION INTEGRATION capstone_fhir_notification_int
    ENABLED = true
    TYPE = QUEUE
    NOTIFICATION_PROVIDER = AZURE_STORAGE_QUEUE
    AZURE_STORAGE_QUEUE_PRIMARY_URI = 'notificatin queue path'
    AZURE_TENANT_ID = '1acc27f8-8478-4411-a70d-8a7eca63c288';
    ```

3. Create file format and external stage objects in Snowflake. 

*In a real engagement these scripts could be collected in a macro and/or stored procedure and run using `dbt run-operation` commands to execute prior to running dbt-external tables.


#### Utilize dbt-external-tables

This package builds a target table and snowpipe in Snowflake by configuring a yml file in dbt. Follow the dbt-external-tables [snowpipe template](https://github.com/dbt-labs/dbt-external-tables). Watch [this video](https://www.loom.com/share/2a2799e51fab4e28b71b348ce23beadb) for a tutorial on how to utilize this pacakge To create the objects and perform an initial load, run the following command:

```
dbt run-operation stage_external_sources --vars "ext_full_refresh: true"
```


### Model Organization  
Our models (typically) fit into three main categories:

| Category | Description                                             |
|----------|---------------------------------------------------------|
| Stage      | Contains models which source data into Snowflake from Azure Data Lake Storage        |
| Raw      | Contains models which source data from Snowflake staging area for use downstream in dbt        |
| Refined  | Contains models which clean and standardize data        |
| Curated  | Contains models which combine, transform and prepare data for downstream analytics |
  
Things to note:
- There are different types of models
that typically exist in each of the above categories.  
See [Model Layers](#model-layers) for more information. 

- Read [How we structure our dbt projects](https://docs.getdbt.com/guides/best-practices/how-we-structure/1-guide-overview) for an example and more details around organization.

### Model Layers
- Only models in `raw` should select from [sources](https://docs.getdbt.com/docs/building-a-dbt-project/using-sources)
- Models not within the `raw` folder should select from [refs](https://docs.getdbt.com/reference/dbt-jinja-functions/ref).
- The following are the DAG stages that we tend to utilize:
  <details>

  <summary>Common</summary>

    | dag_stage | Typically found in | description                                                        |
    |-----------|--------------------|--------------------------------------------------------------------|
    | stg_     | /models/stg             | <li> Indicates a data set created from dbt-external-tables macros. |
    | raw_      | /models/raw    | <li> Indicates a data set that is being sourced into dbt. </li><li> This layer is typically virtual and it represents a 1:1 relationship between the source from the Snowflake staging database and first layer of models. </li> |                                                                                                           |
    | int_      | /models/refined      | <li> Indicates a logical step towards creating a final data set. </li><li>Typically used for:</li><ul><li>Breaking up models that will feed fct_ or dim_ models into smaller pieces to reduce complexity</li><li>Creating a reusable data set to reference in multiple downstream fct_ and dim_ models</li></ul> |
    | base_     | /models/refined    | <li> Indicates cleaning, flattening and standardization on a data set before joining to other data sets in `int_`  models. </li> |  
    | dim_      | /models/curated      | <li> Flags data which is used to describe an entity. </li><li> Indicates a final data which is robust, versatile, and ready for consumption. </li> |
    | fct_      | /models/curated      | <li> Flags data which is in the form of numeric facts observed during measurement events. </li><li> Indicates a final data which is robust, versatile, and ready for consumption. </li> |
  
  </details>

  <details>

  <summary>Uncommon</summary>

    | dag_stage | Typically found in | description                                                        |
    |-----------|--------------------|--------------------------------------------------------------------|
    | report_   | /models/curated/reports    | Indicates that a final data sets are being modeled to pre-aggregate reports for use in outside tooling.                                                                                                                    |

  </details>

### Model Standards
The following standards were defined by the customer for the Curated Layer for key columns and additional metadata fields.
- All key columns in the Curated layer should use the [dbt-utils generate surrogate key macro](https://github.com/dbt-labs/dbt-utils#generate_surrogate_key-source)
- All fct and dim tables should have a primary key which represents a hash of the natural key.
- All fct and dim tables should have a `record_source` field which represents the source table or file from which the row originated.
- All fct and dim tables should have a `load_datetime` or `effective_datetime` field which represents the datetime the row was inserted into the database
- All dim tables should be [Type 1](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-1/) unless otherwise noted.
- [Type 2 Dimension](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-2/) tables should have a `effective_start_datetime` and `effective_end_datetime` field representing the datetime range when the row was the current row and an `is_active_yn` or `is_current_row` indicator that is a `boolean` to show the latest row.

*For additional information on the definition of database keys see [Database Keys](#database-keys) in the appendix.

### Model File Naming and Coding

- All objects should be plural.  
  Example: `raw_stripe__invoices.sql` vs. `raw_stripe__invoice.sql`

- All objects should have a prefix to indicate their DAG stage in the flow.  
  See [Model Layers](#model-layers) for more information.

- All models should use the naming convention `<type/dag_stage>_<source/topic>__<additional_context>`. See [this article](https://docs.getdbt.com/blog/stakeholder-friendly-model-names) for more information.
  - For models in the **curated** folder `__<additional_context>` is optional. 
  - Models in the **raw** folder should use the source's name as the `<source/topic>` and the entity name as the `additional_context`.

    Examples:
    - seed_snowflake_spend.csv
    - base_stripe__invoices.sql
    - raw_stripe__customers.sql
    - raw_salesforce__customers.sql
    - int_customers__unioned.sql
    - fct_orders.sql

- Schema, table and column names should be in `snake_case` (i.e. an underscore separating words).

- Limit use of abbreviations that are related to domain knowledge. An analyst will understand `medical_record_number` better than `mrn`.

- Use names based on the _business_ terminology, rather than the source terminology.

- Each model should have a primary key that can identify the unique row, and should be named `<object>_id`, e.g. `account_id` – this makes it easier to know what `id` is being referenced in downstream joined models.

- If a surrogate (hash) key is created, it should be named `<object>_skey`.

- For `refined` or `raw` models, columns should be ordered in categories, where identifiers are first and date/time fields are at the end.  
  Example:
  ```sql
  transformed as (
      select
          -- ids
          order_id,
          customer_id,

          -- dimensions
          order_status,
          is_shipped,

          -- measures
          order_total,

          -- date/times
          created_at,
          updated_at,

          -- metadata
          _sdc_batched_at
      from source
  )
  ```

- Date/time columns should be named according to these conventions:
  - Timestamps: `<event>_at`  
    Format: UTC  
    Example: `created_at`
  
  - Dates: `<event>_date`  
    Format: Date  
    Example: `created_date`

- Booleans should be prefixed with `is_` or `has_`.  
  Example: `is_active_customer` and `has_admin_access`

- Price/revenue fields should be in decimal currency (e.g. `19.99` for $19.99; many app databases store prices as integers in cents). If non-decimal currency is used, indicate this with suffix, e.g. `price_in_cents`.

- Avoid using reserved words (such as [these](https://docs.snowflake.com/en/sql-reference/reserved-keywords.html) for Snowflake) as column names.

- Consistency is key! Use the same field names across models where possible.  
Example: a key to the `customers` table should be named `customer_id` rather than `user_id`.

### Model Configurations

- Model configurations at the [folder level](https://docs.getdbt.com/reference/model-configs#configuring-directories-of-models-in-dbt_projectyml) should be considered (and if applicable, applied) first.
- More specific configurations should be applied at the model level [using one of these methods](https://docs.getdbt.com/reference/model-configs#apply-configurations-to-one-model-only).
- Models within the `curated` folder should be materialized as `table` or `incremental`.
  - By default, `curated` should be materialized as `table` within `dbt_project.yml`.
  - If switching to `incremental`, this should be specified in the model's configuration.
 

### Unit Testing
At a minimum, developers need to perform the following unit tests before creating their Pull Request. Screenshots of the successful unit tests should be included as part of your pull request in the [Validation of models](https://github.com/Hakkoda1/dbt_capstone_solution/blob/main/pull_request_template.md#validation-of-models) section.

  1. [Compare Row Counts](https://github.com/dbt-labs/dbt-audit-helper/tree/0.6.0/#compare_relations-source) 
  This test will ensure that all rows have arrived in the target when compared to the source. Add a screenshot of the unit test preview from the dbt Cloud UI in your pull request similar to the table below:

      | in_a | in_b | count | percent_of_total |
      |----|----|----|----|
      | True   | True | 9500 | 100.00 |

  2. [Compare Column data types](https://github.com/dbt-labs/dbt-audit-helper#compare_relation_columns-source)
  This test will ensure that all data types & ordinal positions align between the source and target.  Add a screenshot of the unit test preview from the dbt Cloud UI in your pull request similar to the table below:

      | column_name	| a_ordinal_position	| b_ordinal_position	| a_data_type |	b_data_type |
      |-----------|-----------|-----------|-----------|-----------|
      |patient_skey	| 1	| 1	| binary	| binary |
      |patient_id	| 2	| 2	| varchar	| varchar |
      |birth_date	| 3	| 3	| timestamp	| date | 

  3. [Compare Column Values](https://github.com/dbt-labs/dbt-audit-helper/tree/0.6.0/#compare_all_columns-source) 
  This test will compare column values between the source and target.  Add a screenshot of the unit test preview from the dbt Cloud UI in your pull request.

### Testing
- At a minimum, `unique` and `not_null` tests should be applied to the primary key and natural key of each model.
  - If a key is represented by a combination of fields, use the [dbt_constraints](https://github.com/Snowflake-Labs/dbt_constraints) package created by Snowflake Labs to define a combination key and apply test as shown in this example:
  ```yaml
  version: 2

  models:

    - name: hsat_customer_name_jaffle_shop
      description: Descriptive information for Customers from Jaffle Shop.              
      tests:
        - dbt_constraints.primary_key:
            column_names:
              - hub_customer_hkey
              - load_datetime
  ```
- For facts and dimensions, referential integrity tests should be applied to the primary key of each relating model. The referential integrity tests can be created using the out-of-box `relationships` test or the `dbt-constraints.foreign_key` test.
  #### Example referential integrity test using `relationships`:
  ```yaml
  version: 2

  models:
    - name: orders
      columns:
        - name: customer_id
          tests:
            - relationships:
                to: ref('customers')
                field: id
  ```
  
  #### Example referential integrity test using `dbt-constraints.foreign_key`:
  ```yaml
  version: 2

  models:
  
    - name: lnk_order_customer
      description: A distinct list of the relationship between Orders and Customers from Jaffle Shop.
      tags: ['order','jaffle_shop']
      columns:
        - name: hub_order_hkey
          tests:
            - dbt_constraints.foreign_key:
                pk_table_name: ref('hub_order')
                pk_column_name: hub_order_hkey
        - name: hub_customer_hkey
          tests:
            - dbt_constraints.foreign_key:
                pk_table_name: ref('hub_customer')
                pk_column_name: hub_customer_hkey  
  ```
- Use the out-of-box dbt tests and/or packages such as [dbt-utils]() and [dbt-expectations](). In the event these packages do not include a test that meets your requirements, create a generic test.
- Additional tests should be applied based on business need for tests like ranges of values, thresholds for metrics, etc.

#### Creating generic tests
[Generic tests](https://docs.getdbt.com/guides/best-practices/writing-custom-generic-tests) are useful when there is custom logic needed to be validated which may not be present in the default tests or packages. 

1. Pick a directory (either tests/generic/ or macros/ ) to write out tests.
2. Write out a test block with the sql script inside utilizing the neccessary function arguements (model,column_name). A test will pass if no rows are returned.
```
{% test validate_ssn(model, column_name) %}

select 
    {{ column_name }} regexp '[0-9]{3}-[0-9]{2}-[0-9]{4}' as valid_ssn
from {{ model }}
where valid_ssn = false

{% endtest %}
```
3. Call the test to be called within the given model.yml and specify additional arguments as needed.

```
- name: view__patient_prototype
    description: This table contains patient data 
    columns:
      - name: patient_id
        description: This column denotes the unique identifier for every patient
        tests:
          - unique
          - not_null
      - name: social_security_number
        tests:
          - validate_ssn
```

#### Testing & Snowflake Zero Copy Cloning

### Branching Strategy
Our pull requests (typically) fit into four main categories:

| Category | Description                                             |
|----------|---------------------------------------------------------|
| Feature      | Branch for adding a new object or functionality in dbt (i.e. a new model, macro, seed, etc)       |
| Fix  | Branch for modifying an existing object in dbt.       |
| Test  | Branch for adding a dbt test or documentation |
| Update  | Branch for updating dbt packages or dbt_project.yml |

The following diagram highlights the branching strategy defined for the project:

![image](https://drive.google.com/uc?export=view&id=1F5iwwkFwbJ1fO1LwOnlUn69_C9FS2VRk)

### Deployment Workflow 
1. Developer creates a branch from QA and names it feature, fix or test and makes their changes or additions.
2. Developer unit tests their changes or additions against a clone of PROD (or QA)
3. A PR is created by the developer from their feature/fix/test branch to QA branch
4. PR is automatically labeled feature, fix or test based on the naming convention of “feature/logical-feature-name” by GitHub PR Labeler action.
5. PR is reviewed and merged into QA branch by an admin
6. QA job is run and QA/UAT is performed on the change/addition 
7. Once ready for production, an admin uses Github  action ‘Draft a new release’ to trigger creation of a release branch PR to the PROD branch.
8. After the release branch PR is merged a second Github action triggers creation of  a git Release and generates release notes.

### Job Standards 
dbt Jobs (typically) fit into four main categories:

| Category | Description                                             |
|----------|---------------------------------------------------------|
| Incremental      | Job for incrementally loading data to be executed on a schedule (i.e. daily, weekly, etc).   |
| History  | Job for loading or re-loading historical data to be executed on an ad-hoc basis. This job typically includes a `full-refresh` command.   |
| One-time Load  | Job for loading or re-loading portions of a model in QA or Production environments for bug fixes or feature additions. This job is typically run on an ad-hoc basis and is customized depending on the change that is being promoted to QA or Production. The steps in the job will make use of node selection commands such as `select`, `exclude` and `+`. |
| Maintenance or Project Initialization  | Job for maintenance or project initialization tasks such as cloning databases, creating Snowflake objects, and more. This type of job is run on an ad-hoc basis. |

#### Environments & Variables
Add note on Deployment vs Development

#### Job Triggers
Add note on dbt Core, dbt Cloud, Fivetran, etc

#### Job Properties and Command Node Selection
Add note on selection, tags, analysis, seeds, freshness and docs.

See dbt documentation on [node selection](https://docs.getdbt.com/reference/node-selection/syntax) for additional details.

### Pull Request Template
[This pull request template]([pull_request_template.md](https://github.com/Hakkoda1/dbt_capstone_solution/blob/main/pull_request_template.md#validation-of-models)), located in the root of our repository, has been created to standardize pull requests across the project team. For reference, review [this article from dbt-Labs](https://docs.getdbt.com/blog/analytics-pull-request-template) describes the importance of having a standardized template for pull requests and provides screenshots of a completed pull request as a guide.

### CTEs

For more information about why we use so many CTEs, check out [this glossary entry](https://docs.getdbt.com/terms/cte).

- Where performance permits, CTEs should perform a single, logical unit of work.

- CTE names should be as verbose as needed to convey what they do.

- CTEs with confusing or noteable logic should be commented with SQL comments as you would with any complex functions, and should be located above the CTE.

- CTEs that are duplicated across models should be pulled out and created as their own models.

- CTEs fall in to two main categories:
  | Term    | Definition                                                                                                                                                             |
  |---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
  | Import  | Used to bring data into a model. These are kept relatively simple and refrain from complex operations such as joins and column transformations.                        |
  | Logical | Used to perform a logical step with the data that is brought into the model toward the end result. |

- All `{{ ref() }}` or `{{ source() }}` statements should be placed within import CTEs so that dependent model references are easily seen and located.

- Where applicable, opt for filtering within import CTEs over filtering within logical CTEs. This allows a developer to easily see which data contributes to the end result.

- SQL should end with a simple select statement. All other logic should be contained within CTEs to make stepping through logic easier while troubleshooting.
  Example: `select * from final`

- SQL and CTEs within a model should follow this structure:
  - `with` statement
  - Import CTEs
  - Logical CTEs
  - Simple select statement

#### Example SQL with CTEs

  ``` sql
   -- Jaffle shop went international!
  with

  -- Import CTEs
  regions as (
      select * from {{ ref('stg_jaffle_shop__regions') }}
  ),

  nations as (
      select * from {{ ref('stg_jaffle_shop__nations') }}
  ),
  
  suppliers as (
      select * from {{ ref('stg_jaffle_shop__suppliers') }}
  ),
  
  -- Logical CTEs
  locations as (
      select
          {{ dbt_utils.surrogate_key([
              'regions.region_id',            
              'nations.nation_id'
          ]) }} as location_sk,
          regions.region_id,
          regions.region,
          regions.region_comment,
          nations.nation_id,
          nations.nation,
          nations.nation_comment
      from regions
      left join nations
          on regions.region_id = nations.region_id
  ),
  
  final as (
      select
          suppliers.supplier_id,
          suppliers.location_id,
          locations.region_id,
          locations.nation_id,
          suppliers.supplier_name,
          suppliers.supplier_address,
          suppliers.phone_number,
          locations.region,
          locations.region_comment,
          locations.nation,
          locations.nation_comment,
          suppliers.account_balance
      from suppliers
      inner join locations
          on suppliers.location_id = locations.location_sk
  )
  
  -- Simple select statement
  select * from final
  ```

### SQL style guide
- **DO NOT OPTIMIZE FOR FEWER LINES OF CODE.**  

  New lines are cheap, brain time is expensive; new lines should be used within reason to produce code that is easily read.

- Use trailing commas

- Indents should use four spaces. 

- When dealing with long `when` or `where` clauses, predicates should be on a new
  line and indented.  
  Example:
  ```sql
  where 
      user_id is not null
      and status = 'pending'
      and location = 'hq'
  ```

- Lines of SQL should be no longer than 80 characters and new lines should be used to ensure this.  
  Example:
  ```sql
  sum(
      case
          when order_status = 'complete'
              then order_total 
      end
  ) as monthly_total,



  {{ get_windowed_values(
        strategy='sum',
        partition='order_id',
        order_by='created_at',
        column_list=[
            'final_cost'
        ]
  ) }} as total_final_cost
  ```

- Use all lowercase unless a specific scenario needs you to do otherwise. This means that keywords, field names, function names, and file names
  should all be lowercased.

- The `as` keyword should be used when aliasing a field or table

- Fields should be stated before aggregates / window functions

- Aggregations should be executed as early as possible before joining to another table.

- Ordering and grouping by a number (eg. group by 1, 2) is preferred over listing the column names (see [this rant](https://blog.getdbt.com/write-better-sql-a-defense-of-group-by-1/) for why). Note that if you are grouping by more than a few columns, it may be worth revisiting your model design. If you really need to, the [dbt_utils.group_by](https://github.com/dbt-labs/dbt-utils/tree/0.8.6/macros/sql/groupby.sql) function may come in handy.

- Prefer `union all` to `union` [*](http://docs.aws.amazon.com/redshift/latest/dg/c_example_unionall_query.html)

- Avoid table aliases in join conditions (especially initialisms) – it's harder to understand what the table called "c" is compared to "customers".

- If joining two or more tables, _always_ prefix your column names with the table alias. If only selecting from one table, prefixes are not needed.

- Be explicit about your join (i.e. write `inner join` instead of `join`). `left joins` are the most common, `right joins` often indicate that you should change which table you select `from` and which one you `join` to.

- Joins should list the left table first (i.e., the table you're joining data to)  
  Example:
  ```sql
  select
      trips.*,
      drivers.rating as driver_rating,
      riders.rating as rider_rating
  from trips
  left join users as drivers
     on trips.driver_id = drivers.user_id
  left join users as riders
      on trips.rider_id = riders.user_id

  ```

#### Example SQL
  ```sql
  with

  my_data as (
      select * from {{ ref('my_data') }}
      where not is_deleted
  ),

  some_cte as (
      select * from {{ ref('some_cte') }}
  ),

  some_cte_agg as (
      select
          id,
          sum(field_4) as total_field_4,
          max(field_5) as max_field_5
      from some_cte
      group by 1
  ),

  final as (
      select [distinct]
          my_data.field_1,
          my_data.field_2,
          my_data.field_3,

          -- use line breaks to visually separate calculations into blocks
          case
              when my_data.cancellation_date is null
                  and my_data.expiration_date is not null
                  then expiration_data
              when my_data.cancellation_date is null
                  then my_data.start_date + 7
              else my_data.cancellation_date
          end as cancellation_date,

          some_cte_agg.total_field_4,
          some_cte_agg.max_field_5
      from my_data
      left join some_cte_agg  
          on my_data.id = some_cte_agg.id
      where 
          my_data.field_1 = 'abc'
          and (
              my_data.field_2 = 'def'
              or my_data.field_2 = 'ghi'
          )
      qualify row_number() over(
          partition by my_data.field_1
          order by my_data.start_date desc
      ) = 1
  )

  select * from final
  ```

### YAML and Markdown style guide

- Every subdirectory contains their own `.yml` file(s) which contain configurations for the models within the subdirectory.

- YAML and markdown files should be prefixed with an underscore ( `_` ) to keep it at the top of the subdirectory.

- YAML and markdown files should be named with the convention `_<description>__<config>`.  

  Examples: `_jaffle_shop__sources.yml`, `_jaffle_shop__docs.md`  

  - `description` is typically the folder of models you're setting configurations for.  
    Examples: `core`, `staging`, `intermediate`
  - `config` is the top-level resource you are configuring.  
    Examples: `docs`, `models`, `sources`
- Indents should use two spaces.

- List items should be indented.

- Use a new line to separate list items that are dictionaries, where appropriate.

- Lines of YAML should be no longer than 80 characters.

- Items listed in a single .yml or .md file should be sorted alphabetically for ease of finding in larger files.

- Each top-level configuration should use a separate `.yml` file (i.e, sources, models)
  Example:
  ```bash
  models
  ├── marts
  └── staging
      └── jaffle_shop
          ├── _jaffle_shop__docs.md

          ├── _jaffle_shop__models.yml
          ├── _jaffle_shop__sources.yml
          ├── stg_jaffle_shop__customers.sql
          ├── stg_jaffle_shop__orders.sql
          └── stg_jaffle_shop__payments.sql
  ```

#### Example YAML
  `_jaffle_shop__models.yml`:

  ```yaml
  version: 2

  models:
  
    - name: base_jaffle_shop__nations

      description: This model cleans the raw nations data
      columns:
        - name: nation_id
          tests:
            - unique
            - not_null   

    - name: base_jaffle_shop__regions
      description: >
        This model cleans the raw regions data before being joined with nations
        data to create one cleaned locations table for use in marts.
      columns:
        - name: region_id
          tests:
            - unique
            - not_null

    - name: stg_jaffle_shop__locations

      description: "{{ doc('jaffle_shop_location_details') }}"

      columns:
        - name: location_sk
          tests:
            - unique
            - not_null
  ```

  #### Example Markdown
  `_jaffle_shop__docs.md`:

  ```markdown
    {% docs enumerated_statuses %}
      
      Although most of our data sets have statuses attached, you may find some
      that are enumerated. The following table can help you identify these statuses.
      | Status | Description                                                                 |
      |--------|---------------|
      | 1      | ordered       |
      | 2      | shipped       |
      | 3      | pending       |
      | 4      | order_pending | 

      
  {% enddocs %}

  {% docs statuses %} 

      Statuses can be found in many of our raw data sets. The following lists
      statuses and their descriptions:
      | Status        | Description                                                                 |
      |---------------|-----------------------------------------------------------------------------|
      | ordered       | A customer has paid at checkout.                                            |
      | shipped       | An order has a tracking number attached.                                    |
      | pending       | An order has been paid, but doesn't have a tracking number.                 |
      | order_pending | A customer has not yet paid at checkout, but has items in their cart. | 

  {% enddocs %}
```

### Jinja style guide

- Jinja delimiters should have spaces inside of the delimiter between the brackets and your code.  
  Example: `{{ this }}` instead of `{{this}}`

- Use [whitespace control](https://jinja.palletsprojects.com/en/3.1.x/templates/#whitespace-control) to make compiled SQL more readable.

- An effort should be made for a good balance in readability for both templated 
and compiled code. However, opt for code readability over compiled SQL readability
when needed.

- A macro file should be named after the _main_ macro it contains.

- A file with more than one macro should follow these conventions:
  - There is one macro which is the main focal point
  - The file is named for the main macro or idea
  - All other macros within the file are only used for the purposes of the main 
    idea and not used by other macros outside of the file.

- Use new lines to visually indicate logical blocks of Jinja or to enhance readability.  
  Example:  
  ```jinja 
  {%- set orig_cols = adapter.get_columns_in_relation(ref('fct_orders')) %}

  {%- set new_cols = dbt_utils.star(
        from=ref('fct_order_items'),
        except=orig_cols
  ) %}

  -- original columns. {{ col }} is indented here, but choose what will satisfy
  -- your own balance for Jinja vs. SQL readability. 
  {%- for col in orig_cols %}
        {{ col }}
  {% endfor %}

  -- column difference
  {{ new_cols }}
  ```

- Use new lines within Jinja delimiters and arrays if there are multiple arguments.  
  Example:
  ```jinja
  {%- dbt_utils.star(
        from=ref('stg_jaffle_shop__orders'),
        except=[
            'order_id',
            'ordered_at',
            'status'
        ],
        prefix='order_'
  ) %}
  ```

### Metrics style guide

#### Organizing Metrics

* Metrics are categorized by entity (object grain that the metrics occurs), and filenames directly correspond to metrics.  
  Filenames are prefixed with `base__` only if they are pre-calculated inputs to derived metrics in other files.
  ```
  ├── dbt_project.yml
  └── models
      ├── marts
      ├── staging
      └── metrics
          ├── projects
          |   ├── active_projects.yml
          ├── accounts
          |   ├── active_cloud_accounts.yml
          └── users
              ├── base__net_promoter_score.yml
              └── net_promoter_score.yml

  ```

#### Metrics Conventions

dbt Metrics fall into four broad categories:
1. Company metrics
2. Team KPIs
3. OKRs
4. Specific metrics related to a product area, business unit, or business function that is not necessarily a team KPI, but important to track nonetheless.

Because of the wide socialization of these docs and downstream usage in the BI layer, consistency and clarity are _very_ important. Below are the general standards and examples of how we format and implement metrics at dbt Labs:
* Metrics names must begin with a letter, cannot contain whitespace, and should be all lowercase.
* The [minimum required properties](https://docs.getdbt.com/docs/building-a-dbt-project/metrics#available-properties) must be present in the metric definition.
* Tags and/or Meta properties should match the categories above and be used to organize metrics at the category or business function level.
* Meta properties should be used to track metric definition ownership.
* For up-to-date information on metrics, please see the [metrics docs on defining a metric](https://docs.getdbt.com/docs/building-a-dbt-project/metrics#defining-a-metric) or the [dbt-labs/metrics README](https://github.com/dbt-labs/dbt_metrics#readme)

#### Example Metrics YAML
```yaml
version: 2

metrics:
  - name: base__total_nps_respondents_cloud
    label: (Base) Total of NPS Respondents (Cloud)
    model: ref('fct_customer_nps')
    description: >
      'The count of users responding to NPS surveys in dbt Cloud.'
    tags: ['Company Metric']

    calculation_method: count
    expression: unique_id

    timestamp: created_at
    time_grains: [day, month, quarter, year]

    dimensions:
      - feedback_source

    filters:
      - field: feedback_source
        operator: '='
        value: "'dbt_cloud_nps'"

    meta:
      metric_level: 'Company'
      owner(s): 'Jane Doe'


  - name: base__count_nps_promoters_cloud
    label: (Base) Count of NPS Promoters (Cloud)
    model: ref('fct_customer_nps')
    description: >
      'The count of dbt Cloud respondents that fall into the promoters segment.'
    tags: ['Company Metric']

    calculation_method: count
    expression: unique_id

    timestamp: created_at
    time_grains: [day, month, quarter, year]

    filters:
      - field: feedback_source
        operator: '='
        value: "'dbt_cloud_nps'"
      - field: nps_category
        operator: '='
        value: "'promoter'"

    meta:
      metric_level: 'Company'
      owner(s): 'Jane Doe'

  - name: promoters_pct
    label: Percent Promoters (Cloud)
    description: 'The percent of dbt Cloud users in the promoters segment.'
    tags: ['Company Metric']

    calculation_method: derived
    expression: "{{metric('base__count_nps_promoters_cloud')}} / {{metric('base__total_nps_respondents_cloud')}}" 

    timestamp: created_at
    time_grains: [day, month, quarter, year]

    meta:
      metric_level: 'Company'
      owner(s): 'Jane Doe'
```

### Database Keys
The following are typical key fields that are encountered in a Data Warehouse. For additional information on the purpose of database keys & their implementation in dbt review [this article](https://docs.getdbt.com/blog/sql-surrogate-keys).

Primary Key: a field that is used to uniquely identifies records within a table.

Natural Key: meaningful values that uniquely identify a record.

* Example: a Social Security Number for an employee or a VIN number for a vehicle.

Foreign Key: a field that relates to the primary key in another table.

Surrogate Key: a key which is artifically generated and is not pulled from the table data.
 
* Example: a sequence which increments everytime a new patient is added to the database or a hash key which is generated using an MD5 hash function using the natural key.

Smart Key: a primary (or surrogate) key which in of itself has an underlying business meaning. This is pulled directly or transformed from the table data. 

* Example: 20221220 as the date key in a Date dimension table. The key is not a sequence or hash value. It is an integer representing the date in the format YYYYMMDD.


### Data Masking
#### What is Data Masking and why is it important?

Data masking is used to protect sensitive data from being viewed by users who shouldn’t have access. This is done by creating a masking policy on the given data column and table so that these users can’t view original data value. This is relevant especially in regards to [personal identifiable data](https://www.techtarget.com/searchsecurity/definition/personally-identifiable-information-PII). PID can be used to identify individuals, and as such if leaked can be harmful to the individuals in question. Furthermore, there are often legal requirements on what can be disclosed. Data masking is very useful in these situations as it mitigates these data risks associated with sensitive data by controlling level of access for users. 

#### [Dynamic Masking in Snowflake](https://docs.snowflake.com/en/user-guide/security-column-ddm-use)

1. Use role with required permissions to create and apply masking policies
2. Create masking policy
    1. Takes in a given value (column) input and requires specifying datatypes for input and output values
    2. Body of policy is a case statement which specifies for given roles what should they see in place of the value
```
create or replace masking policy email_mask as (val string) returns string ->
  case
    when current_role() in ('ANALYST') then val
    else '*********'
  end;
```
3. Place masking policy on table column
```
alter table if exists user_info modify column email set masking policy email_mask;
```
4. Validate data masking
Switch between specified roles to validate that masking works correctly given the specified role.

#### Data Masking Techniques
* Truncate/Substring
  * Rounds the input expression down and can be used to hide unnecessary pieces of  information by returning only a portion of the data value which is needed
  * Example
    Truncating birth dates to just the year
* Regex
  * Powerful tool to dynamically create a desired output based on pattern matching.
    * Example:
      Returning only the email domain in a email address column while substituting the actual address with *’s
* Hashes
  * Transforms an input to an encrypted randomized value. This is mapped to the original data value and is useful in masking data while allowing lookups and joins to still occur.
  
#### Capstone Data Masking Guideline
Follow the given [instructions](https://docs.google.com/document/d/1-7hVbOqhuIqZcZkPt1pDOUoZbuXvrtF5pvDLyjOJE-A/edit?usp=sharing) to mask approriate PID fields (social_security_number, medical_record_number, birth_date) for the project_team_viewer_role.

This is what the masked columns should look like:
<img width="630" alt="Screen Shot 2023-02-14 at 11 37 49 AM" src="https://user-images.githubusercontent.com/116028110/218827499-8abd1c7a-636a-40df-97f6-41d0248e9223.png">



### Sigma

#### Access Sigma Sandbox
1. Open a web browser and go to the Sigma Sandbox website.
2. Log in using your credentials.
3. Once logged in, you should be redirected to the Sigma Sandbox dashboard.


#### Connect Sigma to production database
1. Click on the "Settings" icon in the top-right corner of the Sigma Sandbox dashboard.
2. Select "Data Sources" from the drop-down menu.
3. Click on the "Add a Data Source" button.
4. Enter the necessary credentials and connection details for your production database.
5. Click on the "Connect" button to connect Sigma to your production database.

#### Create a visualization for the Length of Stay metric
1. Click on the "Visualizations" tab in the Sigma Sandbox dashboard.
2. Click on the "Create Visualization" button.
Select the "Length of Stay" metric from the list of available metrics.
3. Choose a visualization type (e.g., line chart, bar chart, etc.).
4. Customize the visualization as needed (e.g., adjust colors, add labels, etc.).
5. Save the visualization by clicking on the "Save" button.

#### Create two visualizations using statistics for the Length of Stay metric
1. Follow steps 1-3 from the previous section to select the "Length of Stay" metric.
2. Choose a visualization type that supports statistics (e.g., box plot, histogram, etc.).
3. Add the desired statistical calculations (e.g., mean, median, standard deviation, etc.) to the visualization.
4. Repeat steps 2-3 to create a second visualization with different statistical calculations.
5. Save the visualizations by clicking on the "Save" button.

#### Create a visualization for the Encounters table
1. Click on the "Visualizations" tab in the Sigma Sandbox dashboard.
2. Click on the "Create Visualization" button.
3. Select the "Encounters" table from the list of available tables.
4. Choose a visualization type that makes sense for the data (e.g., scatter plot, bar chart, etc.).
5. Customize the visualization as needed.
6. Save the visualization by clicking on the "Save" button.

#### Create a visualization for the Patients table
1. Follow steps 1-3 from the previous section to select the "Patients" table.
2. Choose a visualization type that makes sense for the data.
3. Customize the visualization as needed.
4. Save the visualization by clicking on the "Save" button.

#### Create a visualization for the Dates table
1. Follow steps 1-3 from the previous section to select the "Dates" table.
2. Choose a visualization type that makes sense for the data.
3. Customize the visualization as needed.
4. Save the visualization by clicking on the "Save" button.

#### Review final dashboard
1. Click on the "Dashboards" tab in the Sigma Sandbox dashboard.
2. Select the dashboard that you have been working on.
3. Review each visualization to ensure that it is displaying the correct data and is formatted correctly.
4. Make any necessary adjustments to the visualizations.
5. Save the final dashboard by clicking on the "Save" button.




