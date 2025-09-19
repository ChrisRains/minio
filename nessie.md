# Install Nessie iceberg catalog

ok, also here we will start with some persistent volumes: 
<br>(don't forget to create directory)

kubectl apply -f nessie-pv.yaml
[nessie-pv.yaml](deployments/nessie-pv.yaml)

and then create the container: 
kubectl apply -f nessie-deployment.yaml

```
... spec:
      containers:
      - name: nessie-container
        image: projectnessie/nessie:latest
        ports:
        - containerPort: 19120
        env:
        - name: QUARKUS_PROFILE
          value: prod
        - name: QUARKUS_HTTP_PORT
          value: "19120"
        - name: QUARKUS_LOG_CONSOLE_FORMAT
          value: "%d{yyyy-MM-dd HH:mm:ss} %-5p [%c{1.}] (%t) %s%e%n"
        - name: QUARKUS_LOG_LEVEL
          value: INFO
...          
```
<br>[nessie-depl.yaml](deployments/nessie-depl.yaml)

so the container is now creating, using port 19120 for communication. 

so let's reroute that port to the local port 30012: 

kubectl apply -f nessie-services.yaml
<br>[nessie-services.yaml](deployments/nessie-services.yaml)

now you should be able to test the installation: 

http://localhost:30021
![img.png](images/nessie1.png)
<br>or
<br>http://localhost:30021/api/v2/config

![nessie2.png](images/nessie2.png)

There is one more thing to do, the default database is in_memory, means, the catalog will be 
lost when pod is shutting down. But we can make the catalog persistent, here I am using my locally setup postgres
instance. Beforehand I created the user nessie_admin in my postgres DB:

```
...     spec:
      containers:
      - name: nessie-container
        image: projectnessie/nessie:latest
        ports:
        - containerPort: 19120
        env:
        - name: QUARKUS_PROFILE
          value: prod
        - name: QUARKUS_HTTP_PORT
          value: "19120"
        - name: QUARKUS_LOG_CONSOLE_FORMAT
          value: "%d{yyyy-MM-dd HH:mm:ss} %-5p [%c{1.}] (%t) %s%e%n"
        - name: QUARKUS_LOG_LEVEL
          value: INFO
        - name: nessie.version.store.type
          value: JDBC
        - name: quarkus.datasource.jdbc.url
          value: "jdbc:postgresql://host.docker.internal:5432/nessie_db"
        - name: quarkus.datasource.username
          value: nessie_admin
        - name: quarkus.datasource.password
          value: nessie_password
...          
```

now the catalog will survive a shutdown :-)

For some reason I had problems using nessie.version.store.type JDBC2, so still using JDBC for now
