
## Redesigning Kaizen's Cloud Data Lake for the Future


### About Kaizen Gaming 

Kaizen Gaming is a GameTech company that operates in online gaming and sports betting industry.
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
These led to the accumulation of technical debt and the eventual need to refactor the architecture to meet Kaizen 
new scale and ambitions.

The challenges faced by the data platform primarily stem from  resource coupling and the way our infrastructure was originally set up.  
- **Quotas**: Critical resources such as storage and compute were shared across multiple teams, resulting in contention
for key quotas (e.g., maximum network IPs, maximum I/O operations per second). These shared limits affected multiple teams and environments.
- **Software Development Life Cycle (SDLC)** Storage was also a shared resource. A single storage account served all environments (Dev, Staging, Prod)
and was used by multiple teams, which made true environment isolation impossible. Developers had to workaround this limitation
to test their applications safely.
- **Cost transparency**:  Due to the shared nature of core resources, attributing costs to specific teams or projects was difficult.
Accurate cost tracking relied heavily on custom tagging and policies, which were often inconsistent or incomplete.
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

CAF, Data Landing zones, Hierachy, Old architecture, Design considerations, New architecture (zoomed-in and zoomed-out), 
challenges revisited.


## Migration 


## Conclusion 

Results, 
problems 