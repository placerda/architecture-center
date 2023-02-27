
[!INCLUDE [header_file](../../../includes/sol-idea-header.md)]

This solution outlines a secure machine learning architecture aimed at Banks and other Financial Institutions. It is designed to help you take your machine-learning workloads to the cloud in an agile and secure fashion. AzureML and Azure Databricks form the core of the solution. This platform works seamlessly with other services, such as Azure Data Lake Storage Gen2, Azure Data Factory, Azure Synapse Analytics, and Power BI.

## Architecture

![Secure ML Architecture for Finance Institutions](../media/fsi-secure-ml-architecture.png)

*Download a [Visio file](https://arch-center.azureedge.net/fsi-vteam-secure-data-science.vsdx) of this architecture.*

<!-- LINK to Visio File: https://microsoft.sharepoint.com/:u:/t/AIFSIAmericas/EbvEs9xDW-lAnpO3P_xIYIsB_hGA9YUiUXCvRRYm9dMM6A?e=6JSguy -->

### Dataflow

1. Users connect to the Data Science & Machine Learning environment through a VPN Connection or an ExpressRoute link. This is a secure connection that allows the users to access the Azure resources.

2. They work in Compute Instances created within a secure Azure ML Workspace to train Machine Learning models (alternatively, they can choose to work on Databricks Workspaces as well).

3. To enable outbound internet access from your private computing resources, you can use a hub VNet that houses your firewall. The hub VNet can be peered with the VNet that houses your Azure Machine Learning workspace. This allows the Azure Machine Learning workspace to communicate with the firewall. The firewall can then be configured to allow outbound internet access from the Azure Machine Learning workspace.

4. The inbound and outbound network traffic is configured according to the public internet access requirements. Some service tags are registered to allow proper traffic according to the [documentation](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-secure-workspace-vnet?tabs=required%2Cpe%2Ccli).

5. All resources (Azure ML Workspace, Storage Account, Azure Key Vault, and Azure Container Registry) use private endpoints allocated in a customer VNet to ensure the private traffic.

6. The customer VNet for Azure ML can also communicate with other VNets through peering. For example, it is essential to securely connect to a Data Lake storage to use the data from different layers: bronze, silver, and gold, when developing ML models.

7. Once the Data Scientists concluded their experiments in the Compute instances they can move forward with implementing the pipelines using more robust computation (Compute Clusters).

8. They can also deploy the models for Batch or Real-Time consumption through Inference endpoints deployed on Managed Online Endpoints. All these endpoints are secured by Private Endpoints connected with the other Azure ML resources.

9. A secure Databricks environment can also be used along with the other services to provide more data processing capabilities using the Spark engine. You can also leverage this integration to monitor Azure Databricks runs, register models, and deploy them in Azure Machine Learning.

### Components

* [Azure Machine Learning](https://learn.microsoft.com/azure/machine-learning/overview-what-is-azure-machine-learning) is used to develop and deploy machine learning models quickly and easily, reducing project costs by providing a cloud-based environment for MLOps with a central model registry. It also provides a secure environment for data scientists to work on their projects.

* [Azure Databricks](https://learn.microsoft.com/azure/databricks/introduction/) is an Apache Spark-based analytics platform optimized for Azure, providing workspaces for data engineering and science tasks. You can attach Azure Databricks to your solution to enhance data transformation and preparation. Databricks experiments and models can be tracked and registered on AzureML with [MLflow](https://www.mlflow.org/). A Databricks cluster with secure cluster connectivity enabled has no public IP addresses on its nodes.

* [Azure Storage Account](https://learn.microsoft.com/azure/storage/common/storage-account-overview) is a secure, highly available, and massively scalable way to store data objects, including Data Lake Storage used to store your training datasets. It provides a unique namespace for your data accessible from anywhere in the world over HTTP or HTTPS. The data stored in the storage account is durable and protected, allowing you to access it with ease.

* [Azure Data Lake Storage Gen2](https://learn.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) is a set of features designed to support big data analytics, built on Azure Blob Storage. It offers file system semantics, file-level security, and scalability, as well as low-cost, tiered storage, high availability and disaster recovery capabilities.

* [Azure Private Endpoints](https://learn.microsoft.com/azure/private-link/private-endpoint-overview) are secure network interfaces that use private IP addresses from virtual networks to connect to services powered by Azure Private Link. This allows services like AzureML, Azure Key Vault and Azure Container Registry to be brought into the virtual network with increased security.

* [Azure VPN Gateway](https://learn.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) is a service that uses a virtual network gateway to send encrypted traffic between an Azure virtual network and on-premise locations over the public Internet, as well as between Azure virtual networks over the Microsoft network.

* [Azure Express Route](https://learn.microsoft.com/azure/expressroute/expressroute-introduction) is a service that provides private connectivity between Azure and on-premises locations over a dedicated private connection facilitated by a connectivity provider, allowing you to securely extend your on-premises network to Azure.

* [Azure Firewall](https://learn.microsoft.com/azure/firewall/overview) is a managed, cloud-based network security service that protects your Azure Virtual Network resources. It provides stateful packet filtering to protect your Azure virtual network resources from common network threats.

* [Azure Key Vault](https://learn.microsoft.com/azure/key-vault/general/basic-concepts) is used by Azure Machine Learning utilizes to secure credentials such as the storage account connection string, passwords to Azure Container Repository instances, and connection strings to data stores.

* [Azure Container Registry](https://learn.microsoft.com/azure/container-registry/container-registry-intro) is used by Azure Machine Learning to securely and scalably store and manage Docker container images for model deployment into production. Additionally, users can use their Azure Container Registry to store custom code, packages, and other resources needed for model building, deployment, and management.

## Scenario details

> This should be an explanation of the business problem and why this scenario was built to solve it.
>> What prompted them to solve the problem?
>> What services were used in building out this solution?
>> What does this example scenario show? What are the customer's goals?

> What were the benefits of implementing the solution?

### Potential use cases

This solution is aimed at financial services companies such as banks, insurers, and capital market companies that want to gain agility and efficiency by bringing their data science workloads to the cloud, but with particular attention to the solution's security.
 
In addition to companies in the FSI segment, other businesses interested in creating a secure data science environment in the cloud can also benefit from this solution. 

## Contributors

*This article is maintained by Microsoft. It was originally written by the following contributors.*

 - [Asmita Usturge](https://www.linkedin.com/in/asmitausturge/) | Cloud Solution Architect
 - [Luiz Braz](https://www.linkedin.com/in/lfbraz/) | Cloud Solution Architect
 - [Paulo Lacerda](https://www.linkedin.com/in/paulolacerda/) | Cloud Solution Architect

*To see non-public LinkedIn profiles, sign in to LinkedIn.*

## Next steps

> Links to articles on Microsoft Learn. Could also be to appropriate sources outside of Learn, such as third-party documentation, GitHub repos, or an official technical blog post.

Examples:
* [Azure Kubernetes Service (AKS) documentation](/azure/aks)
* [Azure Machine Learning documentation](/azure/machine-learning)
* [What are Azure Cognitive Services?](/azure/cognitive-services/what-are-cognitive-services)

## Related resources

> Use "Related resources" for architecture information that's relevant to the current article. It must be content that the Azure Architecture Center TOC refers to, but may be from a repo other than the AAC repo.
> Links to articles in the AAC repo should be repo-relative, for example (../../solution-ideas/articles/article-name.yml).

> Here are example sections:

Related architecture guides:

* [Artificial intelligence (AI) - Architectural overview](/azure/architecture/data-guide/big-data/ai-overview)
* [Choosing a Microsoft cognitive services technology](/azure/architecture/data-guide/technology-choices/cognitive-services)

Fully deployable architectures:

* [Chatbot for hotel reservations](/azure/architecture/example-scenario/ai/commerce-chatbot)
* [Build an enterprise-grade conversational bot](/azure/architecture/reference-architectures/ai/conversational-bot)
* [Speech-to-text conversion](/azure/architecture/reference-architectures/ai/speech-ai-ingestion)