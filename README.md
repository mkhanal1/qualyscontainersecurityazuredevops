# Qualys Container Security in Azure DevOps
An Azure DevOps Pipeline Workshop to showcase scanning of container images during build phase of the pipeline using Qualys Container Security (CS) before being pushed to the registry for deployment.

## License
_**THIS SCRIPT IS PROVIDED TO YOU "AS IS."  TO THE EXTENT PERMITTED BY LAW, QUALYS HEREBY DISCLAIMS ALL WARRANTIES AND LIABILITY FOR THE PROVISION OR USE OF THIS SCRIPT.  IN NO EVENT SHALL THESE SCRIPTS BE DEEMED TO BE CLOUD SERVICES AS PROVIDED BY QUALYS**_

## Description
In this workshop, you build a solution where Qualys CS can be used to detect vulnerabilities in the Container Image and pass/fail builds based on conditions listed below:
  1. Vulnerability Severity
  2. Qualys Vulnerability Identifiers (QIDs)
  3. CVEs
  4. Software Names
  5. CVSS Score

The condition is specified in the [file](/jq_filter.txt) using [Jq syntax](https://stedolan.github.io/jq/manual/).

```**_An example_**

[.vulnerabilities[] | select(.severity>=3) | {qid, title: .title}] | length as $vuln_count | if $vuln_count > 0 then error("\($vuln_count) vulnerabilities with severity >= 3 found!") else "No vulnerabilities found with severity >=3" end```

**CS Sensor Mode**

We will run Qualys sensor in CICD mode because the script in this workshop will tag the image(s) specified as "qualys_scan_target:<image-id>" since the sensor is built to only scan those images, hence it has some efficiencies to better support CI pipelines. The CS sensor is hosted in public repository (qualys/qcs-sensor) in DockerHub.
  1. General (Host)
  2. Registry
  3. Build (CI/CD)

The workshop adapts the CI scripts available in [repo](https://github.com/Qualys/community/tree/master/containerSecurity#validate-docker-image-without-plugin).

## **Prerequisites:**
  1. [**An Azure Container Registry**](/prerequisite/azurecontainerregistry.md)
  2. [**A Qualys Subscription**](https://www.qualys.com/free-trial/)
 
## Usage
**Task 1:** Use the [Azure DevOps Demo Generator](https://azuredevopsdemogenerator.azurewebsites.net/) to provision the project to your Azure DevOps Org. Use the below GitHub link as source template
_https://raw.githubusercontent.com/mkhanal1/qualyscontainersecurityazuredevopsdemo/master/containersecuritydemo.zip_

  ![Image1](/images/1.png?raw=true)
  ![Image2](/images/2.png?raw=true)

**Task 2:** Import this repository to Azure GIT

![Image3](/images/3.png?raw=true)


**Task 3:** Edit your pipeline variables in Variable Groups
The template file has these 9 variables.

  * **QUALYS_API_SERVER:** "Qualys baseurl for CS API"
  * **QUALYS_PASSWORD:** "Qualys password to call CS API"
  * **QUALYS_USERNAME:** "Qualys username to call CS API"
  * **SENSOR_ACTIVATION_ID:** " Activation Id for the container sensor"
  * **SENSOR_CUSTOMER_ID:** "Qualys subscription’s customerId"
  
  Eg: _https://{storage-account}.blob.core.windows.net/{container-name}/QualysContainerSensor.tar_
  
  
**Task 4:** Edit the Build Pipeline

  * Select the Task **Build an image** and edit the parameters
  
    Parameter|Value|Notes|
    ---------|-----|-----|
    Container Registry Type | Azure Container Registry | Azure Container Registry to connect to it by using an Azure Service Connection |
    Azure subscription | Name of service connection | select the Azure subscription from the list and click 'Authorize'. |
    Azure Container Registry | Name of the registry | The container image will be built and pushed to this container registry in the selected Azure Subscription |
  
    ![Image4](/images/4.png?raw=true)
  
  * Select the Task **Push an image**
  
    Use the same parameters as described in Task "Build an image"
