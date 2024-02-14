# gICS deployment on Kubernetes infrastructure



## Objectives

The primary objectives of this document is present steps to deploy gICS web application on kubenetes so that DB server is MySQL and located on separate standalone server.

## Deployment Process

Follow these steps:

1. Create a DB server. We use MySQL server for this project
2. Download gics depoyment package from https://www.ths-greifswald.de/en/researchers-general-public/gics/#
3. Extract content of gICS package - find sqls folder, you can find three script files there
4. Connect to MySQL server and run each script
5. After that check two DBs including gics and gras with all tables, in addition, you must have two users: gras_user and gics_user . You can find the password of these users inside of script files
6. Test the connection to DB server and each DB from other devices
7. Make sure the ports: 8080 and 9990 are accessible on your Kubernetes infrastructure
8. Deploy following yml file to create gics pod
```yaml
# Example YAML configuration
key1: value1
key2:
  - item1
  - item2
key3:
  nested_key1: nested_value1
  nested_key2: nested_value2

   ```bash
  apiVersion: apps/v1
kind: Deployment
metadata:
  name: gics-wildfly-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: gics-wildfly
  template:
    metadata:
      labels:
        app: gics-wildfly
    spec:
      containers:
      - name: gics-wildfly
        image: mosaicgreifswald/wildfly:24.0.1.Final-20220224
        ports:
        - containerPort: 8080
        - containerPort: 9990
        env:
          - name: GICS_FILE_LOG
            value: "TRUE"
          - name: GICS_LOG_LEVEL
            value: "DEBUG"  # Adjust the log level as needed
          - name: GICS_DB_HOST
            value: "YOUR DB SERVER ADDRESS"  # Update with your actual MySQL host IP address
          - name: GICS_DB_PORT
            value: "3306"  # Replace with your actual MySQL port
          - name: GICS_DB_NAME
            value: "gics"
          - name: GICS_DB_USER
            value: "gics_user"
          - name: GICS_DB_PASS
            value: "gics_password"
          - name: MYSQL_ROOT_PASSWORD
            value: "YOUR ROOT PASSWORD"
          - name: GRAS_DB_HOST
            value: "YOUR DB SERVER ADDRESS"  # Update with your actual MySQL host IP address
          - name: GRAS_DB_PORT
            value: "3306"  # Replace with your actual MySQL port
          - name: GRAS_DB_NAME
            value: "gras"
          - name: GRAS_DB_USER
            value: "gras_user"
          - name: GRAS_DB_PASS
            value: "gras_password"
          - name: WILDFLY_PASS
            value: "YOUR WILDFLY ADMIN PASSWORD"  # Set your WildFly admin password
          - name: JAVA_OPTS
            value: "-server -Xms1G -Xmx3G -XX:MetaspaceSize=256M -XX:MaxMetaspaceSize=1G -XX:StringTableSize=1000003 -Dorg.apache.cxf.stax.maxChildElements=100000 -Djava.net.preferIPv4Stack=true -Djava.awt.headless=true -Djboss.modules.system.pkgs=org.jboss.byteman"
          # Add other environment variables as needed based on your configuration
        volumeMounts:
          - name: jboss
            mountPath: /entrypoint-wildfly-cli
          - name: deployments
            mountPath: /entrypoint-wildfly-deployments
          - name: logs
            mountPath: /entrypoint-wildfly-logs
          - name: addins
            mountPath: /entrypoint-wildfly-addins
        command: ["/bin/bash", "-c", "./wait-for-it.sh $GICS_DB_HOST:$GICS_DB_PORT -t 120 && ./run.sh"]
      volumes:
        - name: jboss
          hostPath:
            path: /opt/gics/jboss  # Replace with the actual host path
        - name: deployments
          hostPath:
            path: /opt/gics/deployments  # Replace with the actual host path
        - name: logs
          hostPath:
            path: /opt/gics/logs  # Replace with the actual host path
        - name: addins
          hostPath:
            path: /opt/gics/addins  # Replace with the actual host path

9. Navigate to the project directory in your local  
10. pip install -r project/requirements.txt
11. python project/pipeline.py
12. python project/training.py

## Datasets

The analysis will be conducted using two distinct datasets:

1. [Diabetes Dataset](https://raw.githubusercontent.com/npradaschnor/Pima-Indians-Diabetes-Dataset/master/diabetes.csv): This dataset contains information relevant to diabetes.
2. [Chronic Kidney Disease Dataset](https://raw.githubusercontent.com/aiplanethub/Datasets/master/Chronic%20Kidney%20Disease%20(CKD)%20Dataset/ChronicKidneyDisease.csv): This dataset contains information relevant to chronic kidney disease.

## Methodology

In this project we employed Machine learning algorithms and statistical analysis to analyze the datasets and draw insights into the factors influencing diabetes and chronic kidney disease. 

## Project report 
Following link provide you with a detailed report about entire analysis and modeling process:
[Project Report](https://github.com/zeinabaliakbari/made-template-ws2324/blob/main/project/report.ipynb)

## My presentation file and video 
Following link provide you with my presentation pdf file:
[Presentation file](https://github.com/zeinabaliakbari/made-template-ws2324/blob/main/project/slides.pdf)

and you can download my presentation video from the following link:  
[Presentation video](https://github.com/zeinabaliakbari/made-template-ws2324/blob/main/project/presentation-video.mp4)




## Usage

To run the analysis on your local machine, follow these steps:

1. Clone the repository:

   ```bash
   git clone https://github.com/zeinabaliakbari/made-template-ws2324.git
2. Navigate to the project directory in your local  
3. pip install -r project/requirements.txt
4. python project/pipeline.py
5. python project/training.py

