# Running the Analysis to Produce the Estimates in Spark

This is the third section of instructions for setting up the Parallel Routing Analysis using OSRM and Postgres in Spark. In this section, I walk through running the analysis to produce estimates in Spark. To accomplish these tasks, I assume you have some familiarity with Amazon Web Services, the command line, Python, and Spark.

### Setup Spark Cluster

1. Setup the AWS CLI command line tool on your local computer, and make sure you configure it (`aws configure`) once it's set up with your AWS access key and secret key. To install the AWS CLI, follow these instructions: http://docs.aws.amazon.com/cli/latest/userguide/installing.html.

2. Setup folders in the S3 bucket you created when setting up the OSRM files using the AWS Console with a `logs` folder and a `bootstrap` folder. In the bootstrap folder, upload `spark-osrm-startup.sh` as the only file. Copy the S3 paths to the logs folder and the `spark-osrm-startup.sh` file, and edit `setup-osrm-spark.sh` with the appropriate log path and path to the bootstrap file.

3. From your local computer, spin up the Spark instances using the code in `setup-osrm-spark.sh`. You'll need to specify your zone, any VPC and subnet settings, the size and number of your instances, etc. before you run this, as this code is just an example. For more information on how to do this, go to https://aws.amazon.com/documentation/cli/. Note that you cannot use instances with less than 32GB of memory on the Master or Slave nodes, as the OSRM server requires at least 16GB of RAM to run, so I recommend using m4.2xlarge for these instances on spot pricing. You can run the code using `./setup-osrm-spark.sh` or `bash setup-osrm-spark.sh`. Note that you can also create your instances using the AWS Console using the EMR service.

### Create Analysis

1. In the AWS Console, get the IP Address of the Master EC2 instance for your cluster, and SSH into the Master instance. Once there, copy the get_estimates.py file to the instance and change the IP of the Postgres instance, password, and geographic level in the file to match your project, and be sure to specify the output location of your S3 bucket at the end of the file.

2. Export your S3 credentials to the right environment variables.

   ```bash
   export AWS_ACCESS_KEY_ID=<YOUR AWS ACCESS KEY>
   export AWS_SECRET_ACCESS_KEY=<YOUR AWS SECRET KEY>
   ```

3. Run the analysis using the following code. You can follow the progress of it in the terminal you ran the command or by checking `http://YOUR EC2 MASTER IP:8080`. This should take a while.

   ```bash
   sudo spark-submit --jars /mnt/postgresql-9.4.1212.jre6.jar get_estimates.py
   ```

4. Once complete, shut down your EMR cluster to save money, as you'll no longer need it, but keep the EC2 Instance running Postgres up, as we'll use it in the next step.