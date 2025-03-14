## Prerequisites

1. Download the [Learn2Hire_v2.yml](https://github.com/andyliuamzn/aws-analytics-workshop-assessment/blob/main/Learn2Hire_v2.yml) and upload it into cloudformation to create the required network services.
 
2. Create the [MSK cluster](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/) with the following settings.

```
- Step 1: Cluster Settings

Cluster name: aws-analytics-workshop-msk
Cluster type: Provisioned
Apache Kafka version: 3.6.0 (recommended)
Broker type: Standard brokers
Broker size: kafka.m7g.large
Number of zones: 2
Brokers per zone: 1
Storage: 200 GiB
Cluster storage mode: EBS storage only
Cluster configuration:
    |- Custom configuration: aws-analytics-workshop-msk-config
    |- Configuration revision: Revision 1```

- Step 2: Networking

VPC: aws-analytics-workshop-vpc
First zone: us-east-1a, aws-analytics-workshop-public-subnet-1
Second zone: us-east-1b, aws-analytics-workshop-public-subnet-2
Security group: aws-analytics-workshop-sg

- Step 3: Security

Access control methods: Unauthenticated access
Between clients and brokers: Plaintext
Within the cluster: TLS encryption
Encrypt data at rest: Use AWS managed key

- Step 4: Monitoring

Monitoring: Basic monitoring
Broker log delivery: Deliver to Amazon CloudWatch Logs
    |- Log group: aws-analytics-workshop-log-group
```

3. Create the [EC2 instance](https://console.aws.amazon.com/ec2/) with the following settings.

```
Name: aws-analytics-workshop-ec2
Amazon Machine Imange (AMI): Amazon Linux 2023 AMI
Instance type: m5.large
Key pair: Please click create new key pair name and select it once the creation is complete.
Network: aws-analytics-workshop-vpc
Subnet: aws-analytics-workshop-public-subnet-1
Auto-assign public IP: Enable
Firewall: Select exisitng security group
Common security groups: aws-analytics-workshop-sg
Configure storage: 30 GiB gp3 Root volume
```

4. Connect to your EC2 instance and Install the required resources.

```
$ ssh -i <path-of-your-keypair.pem> ec2-user@<your-ec2-public-ip>
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Tue Mar 11 01:44:06 2025 from xx.xx.xx.xx
[ec2-user@ip-172-xx-xx-133 ~]$
[ec2-user@ip-172-xx-xx-133 ~]$ sudo yum -y install java-11
[ec2-user@ip-172-xx-xx-133 ~]$ wget https://archive.apache.org/dist/kafka/3.6.0/kafka_2.13-3.6.0.tgz
[ec2-user@ip-172-xx-xx-133 ~]$ tar -xzf kafka_2.13-3.6.0.tgz
```

5. Create Topic with 2 partitions and 2 replication factors, and try to check the topic info.
```
$ export BS=<your-broker-endpoint>
$ cd kafka_2.13-3.6.0/bin
$ ./kafka-topics.sh --bootstrap-server $BS --create --topic <your-topic-name> --partitions 2 --replication-factor 2
$ ./kafka-topics.sh --bootstrap-server $BS --describe --topic <your-topic-name>
```

6. Wrtie data into topic and read data from the topic.
```
./kafka-console-producer.sh --bootstrap-server $BS --topic <your-topic-name>
./kafka-console-consumer.sh --bootstrap-server $BS --topic <your-topic-name>
```
