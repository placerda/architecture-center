
[!INCLUDE [header_file](../../../includes/sol-idea-header.md)]

This solution outlines a secure machine learning architecture aimed at Banks and other Financial Institutions. It is designed to help you take your machine-learning workloads to the cloud in an agile and secure fashion. AzureML form the core of the solution. This platform works seamlessly with other services, such as Azure Data Lake Storage Gen2, Azure Data Factory, Azure Synapse Analytics, and Power BI.

## Architecture

![Secure ML Architecture for Finance Institutions](../media/fsi-secure-ml-architecture.png)

*Download a [Visio file](https://arch-center.azureedge.net/fsi-vteam-secure-data-science.vsdx) of this architecture.*

<!-- TODO: INCLUIR ITEM REATIVO AO AOAI e REORODENAR OS CIRCULOS -->

<!-- LINK to Visio File: https://microsoft.sharepoint.com/:u:/t/AIFSIAmericas/EbvEs9xDW-lAnpO3P_xIYIsB_hGA9YUiUXCvRRYm9dMM6A?e=6JSguy -->

### Dataflow

1. Users connect to the Data Science & Machine Learning environment through a VPN Connection or an ExpressRoute link. This is a secure connection that allows the users to access the Azure resources.

2. Users work in Compute Instances created within a secure Azure ML Workspace to train Machine Learning models.

3. Once the Data Scientists have concluded their experiments in the Compute instances, they could move forward with implementing the pipelines using more robust computation (Compute Clusters).

<!-- TODO: ITEM 4 ADICONAL OPCIONAL EM QUE SE PODE USAR UM LLM PARA MELHORAR A SOLUCAO -->

5. Users can deploy the models for Batch or Real-Time consumption through Inference endpoints deployed on Managed Endpoints. All these endpoints are secured by Private Endpoints connected with the other Azure ML resources. 

6. All resources (Azure ML Workspace, Storage Account, Azure Key Vault, and Azure Container Registry) use private endpoints allocated in a customer VNet to ensure the private traffic.

7. To enable outbound internet access from the private computing resources, users can use a hub VNet, that houses firewall. The hub VNet can peer with the VNet that houses Azure Machine Learning workspace. This allows the Azure Machine Learning workspace to communicate with the firewall. The firewall is configured to allow outbound internet access from the Azure Machine Learning workspace, preventing data exfiltration.

8. The inbound and outbound network traffic is configured according to the public internet access requirements. Some service tags are registered to allow proper traffic according to the [documentation](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-secure-workspace-vnet?tabs=required%2Cpe%2Ccli).

9. The customer VNet for Azure ML can also communicate with other VNets through peering. For example, it is essential to securely connect to a Data Lake storage to use the data from different layers: bronze, silver, and gold, when developing ML models.

10. A secure Databricks environment can also be used along with the other services to provide more data processing capabilities using the Spark engine. Users can also leverage this integration to monitor Azure Databricks runs, register models, and deploy them in Azure Machine Learning. More details about this approach can be found on this [repo](https://github.com/Azure-Samples/aml-adb-managed-endpoints).

### Components

* [Azure Machine Learning](https://learn.microsoft.com/azure/machine-learning/overview-what-is-azure-machine-learning) is used to develop and deploy machine learning models quickly and easily, reducing project costs by providing a cloud-based environment for MLOps with a central model registry. It also provides a secure environment for data scientists to work on their projects.

* [Azure Databricks](https://learn.microsoft.com/azure/databricks/introduction/) is an Apache Spark-based analytics platform optimized for Azure, providing workspaces for data engineering and science tasks. You can attach Azure Databricks to your solution to enhance data transformation and preparation. You can also use [MLflow](https://www.mlflow.org/) for tracking experiments and registering Databricks models on AzureML model registry. A Databricks cluster with secure cluster connectivity enabled has no public IP addresses on its nodes.

<!--TODO: AZURE OPENAI -->

* [Azure Storage Account](https://learn.microsoft.com/azure/storage/common/storage-account-overview) is a secure, highly available, and massively scalable way to store data objects, including Data Lake Storage used to store your training datasets. It provides a unique namespace for your data accessible from anywhere in the world over HTTP or HTTPS. The data stored in the storage account is durable and protected, allowing you to access it with ease.

* [Azure Data Lake Storage Gen2](https://learn.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) is a set of features designed to support big data analytics built on Azure Blob Storage. It offers file system semantics, file-level security, and scalability, as well as low-cost, tiered storage, high availability and disaster recovery capabilities.

* [Azure Private Endpoints](https://learn.microsoft.com/azure/private-link/private-endpoint-overview) are secure network interfaces that use private IP addresses from virtual networks to connect to services powered by Azure Private Link. This allows services like AzureML, Azure Key Vault and Azure Container Registry to be brought into the virtual network with increased security.

* [Azure VPN Gateway](https://learn.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) is a service that uses a virtual network gateway to send encrypted traffic between an Azure virtual network and on-premise locations over the public internet, as well as between Azure virtual networks over the Microsoft network.

* [Azure Express Route](https://learn.microsoft.com/azure/expressroute/expressroute-introduction) is a service that provides private connectivity between Azure and on-premises locations over a dedicated private connection facilitated by a connectivity provider, allowing you to extend your on-premises network to Azure securely.

* [Azure Firewall](https://learn.microsoft.com/azure/firewall/overview) is a managed, cloud-based network security service that protects your Azure Virtual Network resources. It provides stateful packet filtering to protect your Azure virtual network resources from common network threats.

* [Azure Key Vault](https://learn.microsoft.com/azure/key-vault/general/basic-concepts) is used by Azure Machine Learning utilizes to secure credentials such as the storage account connection string, passwords to Azure Container Repository instances, and connection strings to data stores.

* [Azure Container Registry](https://learn.microsoft.com/azure/container-registry/container-registry-intro) is used by Azure Machine Learning to securely and scalably store and manage Docker container images for model deployment into production. Additionally, users can use their Azure Container Registry to store custom code, packages, and other resources needed for model building, deployment, and management.

## Scenario details

<!-- 
This should be an explanation of the business problem and why this scenario was built to solve it.
>> What prompted them to solve the problem?
>> What services were used in building out this solution?
>> What does this example scenario show? What are the customer's goals?

> What were the benefits of implementing the solution?
This solution built to solve secure ML network designes for banks and FSI. This architecture design can be used for following scenarios -->

Instituições financeiras usualmente tem requisitos de segurança, compliance e governança que requerem arquiteturas que considerem todos estes aspectos. Nesta seção são apresentados os elementos que tipicamente são necessários nesse tipo de cenario.

### Segurança

* [No public internet access](https://learn.microsoft.com/azure/private-link/private-endpoint-overview). You don't need to expose your Azure resources to the public internet by using Azure Private Endpoints from within your virtual network. This provides an additional layer of security for your resources. A private endpoint is a network interface that uses a private IP address from your virtual network. This network interface connects you privately and securely to a service powered by Azure Private Link.

* [Network security and isolation](https://learn.microsoft.com/azure/machine-learning/concept-enterprise-security#network-security-and-isolation). To restrict network access to Azure Machine Learning resources, you can use Azure Virtual Network (VNet). VNets allow you to create network environments that are partially or fully isolated from the public internet. This reduces the attack surface for your solution and the chances of data exfiltration.

* [Data exfiltration prevention](https://learn.microsoft.com/azure/machine-learning/how-to-prevent-data-loss-exfiltration). Azure Machine Learning has several inbound and outbound network dependencies. By limiting inbound and outbound requirements, you can minimize data exfiltration risk by malicious agents within your organization.

### Inovação

* [Large Language Models](prompt flow). bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla.

### Escalabilidade

* [Autoscale on massive data processing](https://learn.microsoft.com/en-us/azure/databricks/delta-live-tables/auto-scaling). bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla.

* [Escalabilidade no treinamento](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-train-tensorflow). bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla.

* [Autoscale MOE](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-autoscale-endpoints). bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla.

### Agilidade

* [Data Governance](https://learn.microsoft.com/en-us/azure/databricks/data-governance/best-practices). bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla.

* [MLOps](https://learn.microsoft.com/en-us/azure/machine-learning/concept-model-management-and-deployment). bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla.

### IA Responsável

* [Responsible AI](https://learn.microsoft.com/en-us/azure/machine-learning/concept-responsible-ai). bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla.

## Potential use cases

This solution is aimed at financial services companies such as banks, insurers, and capital market companies that want to gain agility and efficiency by bringing their data science workloads to the cloud, but with particular attention to the solution's security.
 
In addition to companies in the FSI segment, other businesses interested in creating a secure data science environment in the cloud can also benefit from this solution. 

## Contributors

*This article is maintained by Microsoft. It was originally written by the following contributors.*

 - [Asmita Usturge](https://www.linkedin.com/in/asmitausturge/) | Cloud Solution Architect
 - [Luiz Braz](https://www.linkedin.com/in/lfbraz/) | Technical Specialist
 - [Paulo Lacerda](https://www.linkedin.com/in/paulolacerda/) | Cloud Solution Architect

*To see non-public LinkedIn profiles, sign in to LinkedIn.*

<!-- ## Next steps -->

<!-- Links to articles on Microsoft Learn. Could also be to appropriate sources outside of Learn, such as third-party documentation, GitHub repos, or an official technical blog post. -->

<!-- * [Enterprise security and governance for Azure Machine Learning](https://learn.microsoft.com/en-us/azure/machine-learning/concept-enterprise-security) -->

## Related resources

Related architecture guides:

* [Machine learning operations (MLOps) v2](/azure/architecture/data-guide/technology-choices/machine-learning-operations-v2)

Fully deployed architectures:

* [How to create a secure workspace by using template](https://learn.microsoft.com/en-us/azure/machine-learning/tutorial-create-secure-workspace-template)

LLM guides:

* [Get started generating text using Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/ai-services/openai/quickstart)

* [Azure Machine Learning prompt flow](https://learn.microsoft.com/en-us/azure/machine-learning/prompt-flow/overview-what-is-prompt-flow)

Secure MLOps solutions:

* [Secure MLOps solutions with Azure network security](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/ai/network-security-mlops)

ML Enterprise Governance:

* [Enterprise security and governance for Azure Machine Learning](https://learn.microsoft.com/en-us/azure/machine-learning/concept-enterprise-security)