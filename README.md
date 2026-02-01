# **Spark Basics – Python – Azure (AKS)**

## **Objective**
The objective of this assignment is to design, implement, and deploy a Spark ETL job that reads data from Azure Data Lake Storage, enriches and processes it using Apache Spark, and stores the transformed output back into Azure in Parquet format.

The solution is containerized using Docker, deployed on Azure Kubernetes Service (AKS) using Terraform, and validated through unit tests.

---

## **Project Setup**

Repository forked from EPAM training repository:  
**M06_SparkBasics_PYTHON_AZURE**

Development performed locally using:

- Python 3.12  
- Apache Spark 3.5.x  
- Docker  
- Terraform  
- Azure CLI & kubectl  

---

## **Infrastructure Deployment (Terraform)**

Resources created:

- Azure Resource Group  
- Azure Storage Account (ADLS Gen2)  
- Azure Kubernetes Service (AKS)  
- Azure Container Registry (ACR)  

Commands used:

```bash
terraform init
terraform plan
terraform apply
```

---

## **Data Preparation**

Dataset **m06sparkbasics.zip** extracted locally and uploaded to Azure Data Lake Storage.

Containers used:

- raw  
- data  

---

## **Spark ETL Job Implementation**

### **Reading Data from Azure Storage**

Dependencies used:

- hadoop-azure  
- azure-storage-blob  
- azure-core  

Spark configuration example:

```python
spark.conf.set(
    "fs.azure.account.key.<storage-account-name>.dfs.core.windows.net",
    os.getenv("AZURE_STORAGE_KEY")
)
```

No secrets are hardcoded.

---

## **Handling Missing Latitude & Longitude**

Missing coordinates resolved using **OpenCage Geocoding API**.

Module used:  
`utils/opencage_client.py`

---

## **Geohash Generation**

4-character geohash generated using latitude and longitude.

Stored in column:  
`geohash_4`

---

## **Dataset Join**

Weather and Hotels datasets joined using:

- Left Join  
- Join key: `geohash_4`  

---

## **PII Encryption**

Encrypted fields:

- Hotel Name  
- Address  

Module used:  
`utils/encryption.py`

Encryption key provided via environment variable:  
`ENCRYPTION_KEY`

---

## **Writing Output Data**

Output written to:

- Azure Data Lake Storage  
- Container: `data`  
- Format: Parquet  
- Partitioning enabled  

---

## **Unit Testing**

Tests implemented using **pytest**.

Run tests using:

```bash
pytest -v
```

---

## **Deployment on Kubernetes (AKS)**

Spark job submitted using:

```bash
spark-submit \
--master k8s://https://<AKS_API_SERVER> \
--deploy-mode cluster \
--name sparkbasics \
--conf spark.kubernetes.container.image=<ACR_IMAGE> \
local:///opt/src/main/python/hotel_weather_geohash_etl.py
```

---

## **Resource Cleanup**

Resources removed using:

```bash
terraform destroy
```

Verification:

```bash
az resource list
```

---

## **Conclusion**

This project demonstrates implementation of a Spark ETL pipeline integrated with Azure cloud infrastructure, Docker containerization, Kubernetes deployment, and Terraform-based infrastructure automation.
