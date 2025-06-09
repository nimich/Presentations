
## Redesigning Kaizen's Cloud Data Lake for the Future


### About Kaizen Gaming 

[Kaizen Gaming](https://kaizengaming.com/home) is a GameTech company that operates in online gaming and sports betting industry.
It has a presence globally under the commercial brand Betano on 18 countries as of 2025. 
Having over 13 million active users and offering 1.5 million gaming events yearly a huge amount of business data are generated each day.
Over 400 million transactions are generated daily while on peak service, over 700 thousand transactions are precessed per minute.

This imposing amount of data is stored in Kaizen's cloud data lake which is the backbone for all data and analytics operations.
Over 2 Petabytes of data are stored in the Lakehouse while the trend shows that the need for scaling up the operations will continue.  
For the past 10 years data in Kaizen has scaled from 27.2 million yearly transactions to over 121 billion a staggering increase over 4.500% 

Data are considered a stategic pillar for the company while data literacy is a key business capability. 
Over 150 people work actively in Data Engineering, Data Science, ML/AI and Business Intelligence to serve
85 departments and 1000+ end users. Data departments are transitioning from a centralized to a decentralized model 
following the Data Mesh principles. A central Data platform is responsible to provide capabilities in domain specific 
data teams to build their own data products by providing tool, services and infrastructure.

## Challenges and one Opportunity

Kaizen's data lake-house architecture spans several years of lifetime and this meant that earlier decisions that where made  
with different constraints and requirements are now outdated. Some of the earliest decisions were made to balance on time delivery 
of business value, so this meant that scoping and prioritizing was done with a focus on the business needs. 
These led to the accumulation of [technical debt](https://en.wikipedia.org/wiki/Technical_debt) and the eventual need to refactor the architecture to meet Kaizen 
new scale and ambitions.

The challenges faced by the data platform primarily stem from  resource coupling and the way our infrastructure was originally set up.  
- **Quotas**: Critical resources such as storage and compute were shared across multiple teams, resulting in contention
for key [quotas](https://learn.microsoft.com/en-us/azure/quotas/quotas-overview) (e.g., maximum network IPs, maximum I/O operations per second). These shared limits affected multiple teams and environments.
- **Software Development Life Cycle (SDLC)** Storage was also a shared resource. A single storage account served all environments (Dev, Staging, Prod)
and was used by multiple teams, which made true environment isolation impossible. Developers had to workaround this limitation
to test their applications safely.
- **Cost transparency**:  Due to the shared nature of core resources, attributing costs to specific teams or projects was difficult.
Accurate cost [allocation](https://www.finops.org/framework/capabilities/allocation/) relied heavily on custom tagging and policies, which were often inconsistent or incomplete.
- **Governance**: Fine-grained access control was challenging to implement, as it had to be retrofitted onto an 
existing architecture rather than being designed into it from the start.
- **Environment provisioning**: While we established separate compute environments for Dev, Staging, and Prod, 
their provisioning was not automated and required manual intervention via the Azure portal.
This led to slower lead times for new environments and made it difficult to adapt quickly to changing requirements. 
As a result, infrastructure change management was prone to errors.

So for the data platform an opportunity to address our technical debt and redesign the architecture was presented. 
An initiative across Kaizen's tech department was launched to migrate our cloud applications to a single Azure account (tenant)
designed with proper governance and security in mind from scratch.
This was a great opportunity to rethink our data lake-house architecture and design it to meet the needs of the future.
Our explicit goals was our new architecture to be:
- **Scalable**: The architecture should be able to handle the increasing volume of data and users without significant performance degradation.
- **Security**: We wanted to have security by design, with a focus on data protection, strong governance and compliance with regulations.
- **Cost Transparency**: We needed to be able to track costs accurately and attribute them to specific teams or projects.
- **Cost Efficiency**: Based on our cost transparency we wanted to be able to optimize our costs and reduce waste.

Under the hood we wanted to lay the foundation for:

**Domain boundaries**: Design with domain boundaries in mind, allowing teams to work independently and reducing coupling.
**Data Democratization**: Our ultimate is goal is to enable data democratization, allowing all teams in Kaizen to access
on-time and quality data for insights without barriers.

## Architecture 

### Cloud Adoption Framework and Resource Hierarchy

Databricks is hosted as a Service in the environments of all big Cloud Providers - AWS, Azure and GCP.
The choice of any of those providers does not matter that much, since on all of them the setup of databricks is similar.
A [well architected](https://learn.microsoft.com/en-us/azure/well-architected/what-is-well-architected-framework#audience) cloud setup can be achieved using the [Cloud Adoption Framework](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/overview) which is a set of tooling and documentation that helps to create an implementation using best practises.
Kaizen's Cloud Architecture is using Azure and their CAF implementation which is based on [Terraform](https://github.com/Azure/terraform-azurerm-caf-enterprise-scale?tab=readme-ov-file#overview), the most common Infrastructure as Code tool.
The key element of CAF in any provider is a [Landing Zone](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/) which is a container for cloud resources.
Relevant to our case here are the Data Landing Zones, containing data and workspaces, and the Data Management Landing Zone containing unity catalog resources.
In the context of Azure Cloud, a Landing Zone is implemented using a **Subscription**.

The hierarchy of cloud resources in Azure consists of the following high level [elements](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts#azure-terminology):

* Tenant : Represents a dedicated identity and access management service
* Subscription : Container for Resources. Allows Isolation of Quotas and Billing
* Resource Group : Logical containers used to group related resources in a subscription
* Resources : Entities managed by Azure ex. Storage Account, virtual network, Databricks workspace, etc

### Old Architecture Challenges and Design Considerations

In our old Cloud Architecture a single Azure tenant and a common subscription were used to host all our resources.
This caused problems with Overlapping Quotas.
Also a common storage was used for all our data regardless of their type.
Additionally, since we started small and expanded fast, all resources were created manually using the Azure Portal.
For all these reasons cost attribution and of course scaling were really difficult.

On our new approach, following the guidelines of the Cloud Adoption Framework for Azure, we separated our workloads to multiple Landing Zones.
The separation was based on differentiation of Environments and Lines of Business.
The result was to avoid quota overlap problems and to achieve a clearer cost attribution.
But there were some things to consider in this new setup.
We needed multiple Databricks Workspaces, but going too far meant hitting the (soft in Azure) [limit](https://www.databricks.com/blog/2022/03/10/functional-workspace-organization-on-databricks.html) of 50 workspaces per tenant.
Also, we needed to decide the sizing of networks used for the workspaces.

A small network would not allow many nodes to run concurrently, but a too big network would cause a waste of IP addresses.
Based on the above we also needed to decide on the Resource Synergy and how we should break up the available workspaces, key vaults, storages, etc.
Also, since we had witnessed the expanding costs of having a common storage with a single configuration, we proceeded to create multiple of them with different configurations.
So now we needed to decide on storage parameters such as performance, replication and redundancy for each case.
And for all the above, we needed to take into consideration, not only the current situation, but also the constant growth so that we remain future proof.

### New Architecture

In the new Architecture, having security in mind, we completely isolated production from other environments so an Azure Tenant was created specifically for production workloads and another Tenant for all other environments (dev, stg, uat, etc).
In each Tenant a Data Management Landing Zone was created to host the Unity Catalog and Metastore resources for Databricks.
Also multiple Data Landing zones were created, each related to different Line of Business Department and Environment.
In each Data Landing Zone, Resource Groups were used to logically isolate the resources of teams in each Department and if needed a Common Resource Group was created to host resources needed by all teams of a Department.
In each of these Team Resource Groups, the Databricks workspace and Key Vaults of the team were created together with the storage accounts hosting their catalogs. There might be multiple storages with the best configuration suiting the type of data in each case.

A department team has RGs with workspaces (and other resources) in multiple Data Landing Zones, each used as a separate environment.
There is an environment for teams to develop their jobs directly in the workspaces and the final code is commited to a git repository.
A staging environment is then used for End-to-End testing utilizing Databricks Asset Bundles for deployment through CI/CD pipelines.
Production data can be consumed in this procedure if needed by utilizing Delta Shares among the tenants.
Being read-only, the Delta Shares ensure that no data disaster will occur while testing!
Finally a Merge Request and Approval process takes place that leads through CI/CD to a production deployment.

### Challenges Revisited

Reexamining our past challenges, we can see that the new architecture addresses them effectively.
Scalability was increased through the introduction of clear boundaries and the separation of quotas across teams and environments.
FinOps practices were improved by enabling cost transparency and decoupling costs between teams.
Governance was strengthened with enhanced access control, isolation of sensitive data, standardization of resources, 
and more reliable change management.
These advancements collectively moved the organization closer to the goal of achieving data democratization.

## Migration

Adopting a new architecture required us to propose and execute a clear plan for transitioning our existing data lakehouse 
to the new environment.

We approached the migration in three key phases:

**Pre migration** 
In this phase, we defined the security and governance model and formalized our overall architecture by establishing 
conventions and resource specifications. We also assessed current limitations and blockers within the existing infrastructure.
For example, our custom-built pipeline orchestration framework needed to be refactored to support multiple dependencies
across workspaces.

**Migration**
During the migration phase, our goal was to enable teams to migrate at their own pace without disrupting business stakeholders.
Rather than enforcing a simultaneous migration across all teams, we proposed a decoupled approach, allowing each team to
manage their own migration timeline.

To support this, the platform team developed tools that abstracted the complexity of a phased migration and ensured 
consistency across environments. Additionally, we were responsible for provisioning the necessary infrastructure,
building supporting tools, and assisting teams in migrating their data and applications.
During migration the data platfrom team was required to create the infrastructure for all teams, provide the necessary 
tooling and support teams to migrate their data and applications.

**Post Migration** 
Once teams had migrated their pipelines and data to the new infrastructure, we focused on ensuring there was no performance
degradation compared to the previous architecture.
Migration also served as the foundation for cost optimization. With our new cost monitoring in place, 
we began implementing strategies such as policy-based cold tier storage, compute and storage reservations
and premium storage for streaming applications.

### Security 

A core principle of our new architecture was security by design. 
Given its decentralized nature, it was crucial to ensure that our security model aligned with this structure.

Following best practices, we clearly defined administrative roles and responsibilities, resulting 
in [three distinct groups](https://www.databricks.com/blog/2022/08/26/databricks-workspace-administration-best-practices-for-account-workspace-and-metastore-admins.html):
Account Management
Workspace Management
Metastore Management
For access control and delegation, we implemented Group-Based Access Control (GBAC). 
This involved syncing our cloud provider’s Identity and Access Management (IAM) system with Databricks Groups and Roles
via SCIM (System for Cross-domain Identity Management) provisioning. This integration allowed teams to manage privileges 
simply by updating their corresponding cloud groups.

To maintain consistency and control in production environments, we enforced the use of service accounts 
(known as Service Principals in Azure IAM). These accounts were solely responsible for executing production workloads,
writing data, and provisioning resources—customized to match each team’s level of operational maturity.

The actual migration consisted of three distinct areas to cover: Infrastructure, Data and Applications.

### Infrastructure

Our approach was to implement everything as Infrastructure as Code (IaC) using Terraform. 
To maximize modularity and reusability, we developed centralized [Terraform modules](https://developer.hashicorp.com/terraform/language/modules).
This strategy helped abstract the complexity of each component behind simple, well-defined interfaces.
Centralizing components is also a well-established pattern for reducing [change amplification](https://en.wikiversity.org/wiki/Software_Design/Change_amplification), 
a common symptom of software complexity that can lead to widespread, hard-to-manage changes.

The Data Platform team created modules for:

Landing zone-level resources, such as resource groups and subscriptions
Databricks account-level components, including catalogs and workspaces
Workspace-level infrastructure, such as clusters and related settings


### Code 

To migrate our application, we leveraged [Databricks Asset Bundles](https://docs.databricks.com/aws/en/dev-tools/bundles/), 
a feature that essentially wraps Terraform to simplify
deployments across multiple environments. This approach allowed us to centralize configuration in a single file, 
streamlining environment-specific settings. For example, we could define Databricks Runtime versions, cluster policies
and default catalogs per environment, applying them consistently across all jobs. 
Additionally, we used Databricks Volumes as the source for all artifacts and configuration files, 
replacing our previous reliance on the Databricks File System (DBFS).

### Data

Τhe most critical part of the migration was transferring data from the old architecture to the new one. 
The scale posed a major challenge—over 20,000 tables and 2 petabytes of data—which required a decentralized approach 
to allow teams to migrate at their own pace.

To avoid disruption for business stakeholders, we adopted a dual-access strategy, making data available in both the 
old and new architectures until teams were ready to fully transition.

To implement this, we used a combination of Delta Sharing and our internal tooling, with our top priority being 
to preserve stable dependencies for downstream users and applications.

Our table migration process followed these steps:

- Each table was made available for reading in both the old and new metastores.
- This was achieved using Delta Sharing, with views created on top of shared data to maintain consistent table names.
- When initiating migration, we used the Delta share as the source for a Delta clone, syncing the data to a temporary table in the new environment.
- Once the sync was complete, we dropped the view in the new tenant and renamed the temporary table to the original table name.
- In the old environment, the original table was archived, and a new view was created pointing to the table in the new tenant via a separate Delta Share.

By using this approach, we ensured minimal disruption to business stakeholders. Data remained accessible in both architectures
throughout the migration, allowing teams to transition at their own pace.

## Conclusion 

By adopting a new architecture, we successfully addressed the limitations of our previous lakehouse design and established
a foundation for a scalable, secure, and cost efficient data platform.

The migration process played a key role in decoupling team dependencies, enabling teams to work more independently 
and efficiently. As a result, more than 20 teams at Kaizen have already onboarded to the new architecture, 
with a goal of reaching 30 teams by the end of the year.
