There are many factors to consider when designing data pipelines, which include disparate data sources, dependency management, interprocess monitoring, quality control, maintainability, and timeliness. Toolset choices for each step are incredibly important, and early decisions have tremendous implications on future successes. I tried to put in some of the key parameters while building/designing data management platform.

### Performance

In almost all cases, the prime concern of any ETL implementation is to migrate the data from source to target as quickly as possible. There is usually a load “window” specified as part of the non-functional requirements; a duration of time that is available to complete the process. The constraints are based on either the availability of the source system data, the need for the business to have access to the information, or a combination of both. At times the decision is being made during the POC and estimated that the flow would suffice the enterprises. Volume of data,Data: Origin, Type, and Timeliness are playing important roles in defining the performance, dependency management etc.

### Simplicity

As with all programming, a premium is placed on simplicity of design. This is in the interests of productivity of development time, consideration of ongoing maintenance, and a likely improvement in performance. The fewer steps involved, the less chance of mistakes being made, or places for things to go wrong. When changes need to be made, or fixes applied, the fewer touch points, the better. During the life of the processes, ownership will likely change hands. The system’s simplicity will aid clarity for those who need to take it on. To gain the advantage of customer and not to lose the opportunity we don’t spend enough time on planning and architecting the design holistically & ended up compromising scalability, elasticity & in turn cost associated with it

### Repeatability

One needs to be able to re-run jobs to achieve consistent and predictable results each time. This means it needs to be applicable to all relevant incoming sources, and in no way dependent on specific time parameters. If sources change, the process needs to handle those changes gracefully and consistently. UI based orchestration would be more beneficial post deployment, managing data engineering pipelines

### Modularity

Units of work should be designed with an eye to repeatable patterns, interdependencies and discreet operations that function in isolation. The goal is to have as few modules as possible to be applied as templates to all future development work. This principle assists with clarity and efficiency of design, as well as reusability. 

### Reusability & Extensibility

As a principle, the goal should be not only to repeat modular patterns, but where possible re-use existing jobs and apply parameterization. This optimizes the efficiency of development and reduces the cycles required for testing.

Rather than “bring everything” from a given source when a data migration process is first built, it should be possible to include only that which is identified as valuable to the business in the context of a given project or release cycle. Over time, additional data elements from the sources can be added to the ETL jobs, with potentially, new targets. The ETL job should take this iterative approach into account.

### Revocability

This refers to the ability to reset the database after a run and to return to the state it was in prior to running the process. This will be important for testing cycles during the development process, but also in production, in the event the database becomes corrupted or requires rolling back to a previous day.

### Subject-orientation & Auditable

Workloads are to be divided into units based on business subject areas rather than source-system groupings or strictly target table structures. This recognizes that a given source table may contain information about more than one subject area (e.g., Customers and Accounts) In addition, a given subject area may be composed of multiple source tables, which may populate multiple targets. Simply limiting the ETL jobs to a single source and a single target may compromise the other principles, particularly performance and simplicity. Similarly, orienting the ETL jobs to either the source or target layouts may degrade the efficiency of the design. 

It is essential to be able to trace the path that data takes from source to target and be able to identify any transformations that are applied on values along the way. Now a days, with evolution of APIs custom fields which increases the Data drifts which required auto-schema correction at target end based on some governance in place.

With these guiding principles, specific strategies can be employed with a set of criteria to judge their applicability. One of the critical facets is deployment of heavy workforce to get the work done in parallel, which will cause integration challenges eventually and spend time rectifying the de-duplication or rationalization of the objects or platform to the extent. Better planning & review of the above parameters, will help data engineers/Technical architects to build a robust, scalable data ingestion platform to support future data explosions.  Me & my team did implement an enterprise NextGen platform transformations working closely on the blueprinting of the design phase, taking into consideration of all factors which may increase my deployment cycles/ year and work well in DevOps model.

- Evaluated the platform on-prem vs cloud (AWS, Azure, GCP)
- ETL Tools - IICS, Snaplogic, Talend, Matillion, Steamsets, PySpark, Databricks etc.
- Agile, SDLC, Hybrid implementation
- Storage Platform - Redshift, S3, Snowflake, Athena etc.

Hope this helps all to rethink & realign the strategies next time.