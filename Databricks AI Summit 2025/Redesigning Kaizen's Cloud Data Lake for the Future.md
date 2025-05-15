
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

Data Lake house architecture spans several years of lifetime and this means that earlier decisions that where made  
with different constraints and requirements maybe be now outdated. Earlier decisions were made to balance on time delivery 
of business value so this meant that scoping and prioritizing was done with a focus on the business needs. 
These led to the accumulation of technical debt and the eventual need to refactor the architecture to meet Kaizen 
new scale and ambitions.

The main challenges that the data platform faces are:
 - **Common resources**: Key resources like Storage and Compute are shared between teams.
This means that quotas like maximum Network, maximum I/O operation per second where shared between and environments. 
For example a common storage account was used for all environments (Dev, Staging, Prod) which also means that 
a proper Software Development Life Cycle (SDLC) was not in place with complete environment isolation. 
Another issue is 
 - **Infrastructure setup**... 



