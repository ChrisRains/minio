# minio
This is my first project setting up an on premise MIN.IO landscape on Windows 11 using the iceberg catalog to store the files / tables

Precondition: kubectl and Rancher Desktop are already installed

First let's create a new namespace <minio-namespace> in rancher Desktop. Therefore you start the Cluster Dashboard and add the namespace in cluster / namespace section

Let's start setting up the min.io container to run a local s3 store:

[minio.md](minio.md)
<br>http://localhost:30001

Ok we can now store files in a S3 compatible object store :-). Now let's setup the iceberg catalog. 
Here we have several options to get this done. One option is to store the catalog in a postgres database. 
There is also an option to use a rest template from apache: iceberg-rest-fixture. 
But as we also want to test dremio, we will use the container from the nessie project:

[nessie.md](nessie.md)

now we can have a look here: 
<br>http://localhost:30021
or
http://localhost:30021/api/v2/config

Now let's install a GUI for our store, and a good one to start is the dremio community edition. 

so let's also get this container running : 
<br>[dremio.md](dremio.md)

and now we have a GUI to play around :-) 
http://localhost:30011




