# gICS deployment on Kubernetes infrastructure



## Objectives

The primary objectives of this document is present steps to deploy gICS web application on kubenetes so that DB server is MySQL and located on separate standalone server.

## Deployment Process

Follow these steps:

1. Create a separate DB server. For this project, we will be using MySQL server.
2. Download the gICS deployment package from  https://www.ths-greifswald.de/en/researchers-general-public/gics/#
3. Extract the contents of the gICS package. Inside, you will find a folder named 'sqls' containing three script files.
4. Connect to the MySQL server and execute each script.
5. After execution, verify the presence of two databases: 'gics' and 'gras', along with all their tables. Additionally, ensure that two user accounts exist: 'gras_user' and 'gics_user'. The passwords for these users can be found within the script files.
6. Test the connection to the DB server and each database from other devices.
7. Prepare the Kubernetes infrastructure. Following operations will be performed on the server hosting the Kubernetes infrastructure.
8. Confirm that ports 8080 and 9990 are accessible on your Kubernetes infrastructure. You can check this by inspecting the firewall IP table.
9. Create the following directories on the host of the Kubernetes infrastructure:
   * sudo mkdir -p /opt/gics/deployments
   * sudo mkdir -p /opt/gics/logs
   * sudo mkdir -p /opt/gics/addins
   * sudo mkdir -p /opt/gics/jboss
  
and set their access permissions (read, write and execute permissions for everyone). In the gICS package you downloaded, find the three folders Deployments, log and jboss, and copy their contents to your directories with same names.
 
10. Create a yml file : gICS.yml , according to the following content. Then, execute : `kubectl apply -f gICS.yml´   to deploy it.
```yaml

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
```
9. Check your deployment, you must have two pods , also you need to see pods log to make sure about DB connection   
10. Create a gics-service.yml file using following content , We use this yml file to create a service that makes our web application available for external network requests.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: gics-wildfly-service
spec:
  selector:
    app: gics-wildfly
  ports:
    - name: http
      protocol: TCP
      port: 8080
      targetPort: 8080
    - name: management
      protocol: TCP
      port: 9990
      targetPort: 9990
  type: NodePort
  
```

12. run this command : kubectl get services
13. When you want to access the web application you can find the ports that need to be replaced (instead of 8080 and 9990) e.g.  8080:30851/TCP,9990:30243/TCP
14.  You can access to gICS web application on http://SERVER IP ADDRESS:30851/gics-web , wildfly console on  http://SERVER IP ADDRESS:30243/console , and wsdl XML interface on http://SERVER IP ADDRESS:30851/gics/gicsService?wsdl



