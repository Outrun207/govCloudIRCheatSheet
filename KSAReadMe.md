# TD IR Knowledge Skills and Abilities

This document is to serve as a guide for identifying crucial AWS threat detection and incident response (TD/IR) skills. It is a hybrid resource, both able to be utilized as a tool during TD/IR and as a guide for developing qualifying and training materials for team members. Ideally, AWS TD/IR operators should have the Knowledge, Skills, and Abilities for each service outlined below as a minimum. These items can be incorporated into a JQS. Free workshops and training materials have also been included under each service's section. The immediately following section can be used as a quick reference guide during IR to guide analysts to information that will answer common IR questions. 

For the best experience, please render this Markdown document. 

# AWS TD/IR and Security Services

## Analyst Questions

### How can I see the history of activity for a particular principal in an Account or Organization? 

[CloudTrail](## AWS CloudTrail), [CloudWatch Logs](## Amazon CloudWatch Logs)

### How can I see which principals interacted with a particular resource or Service? 

[CloudTrail](## AWS CloudTrail), [CloudWatch Logs](## Amazon CloudWatch Logs), [AWS Config](## AWS Config)

### Where are the typical places that AWS logs are stored?

[CloudTrail Event History](### Critical CloudTrail KSAs), [S3](## Amazon S3), [CloudWatch Logs](## Amazon CloudWatch Logs)

### How can I query CloudWatch logs via the CLI? 

[CloudWatch Logs CheatSheet](### CloudWatch Logs CLI and Bash CheatSheet)

### How can I perform structured queries on CloudTrail, VPC Flow Logs, DNS Logs, or other logs stored in S3? 

[AWS Athena](## AWS Athena) 

### How can I monitor network traffic within my VPC

[VPC Flow Logs](## VPC Flow Logs), [Route53 Resolver](## Amazon Route53 Resolver DNS Logs)

### How can I be alerted if a specific threshold of metrics for a service or resource is met? 

[CloudWatch Metrics](## Amazon CloudWatch Metrics), [CloudWatch Alarms](## Amazon CloudWatch Alarms)

### How can I aggregate security findings from multiple sources such as GuardDuty, Config, and partner solutions? 

[Security Hub](## AWS Security Hub)

### How can I easily measure my account against a minimum baseline standard of compliance rules?

[Security Hub Security Standards](### Security Hub Critical KSAs) 

### How can I perform threat monitoring an AWS Account to detect malicious traffic and anomalous behavior? 

[GuardDuty](## Amazon GuardDuty), [Config](## AWS Config)

### How can I get an inventory of the resources in an AWS Account? 

[Config](## AWS Config)

## AWS CloudTrail 

With AWS CloudTrail, you can monitor your AWS deployments in the cloud by getting a history of AWS API calls for your account, including API calls made via the AWS Management Console, the AWS SDKs, the command line tools, and higher-level AWS services. You can also identify which users and accounts called AWS APIs for services that support CloudTrail, the source IP address the calls were made from, and when the calls occurred. You can integrate CloudTrail into applications using the API, automate trail creation for your organization, check the status of your trails, and control how administrators turn CloudTrail logging on and off. 

### How AWS CloudTrail Differs for AWS GovCloud (US)

Incase you are familiar with CloudTrail in AWS Commercial Regions, the following list details the differences for using this service in AWS GovCloud (US) Regions compared to other AWS Regions:

  * As of November 22, 2021, AWS CloudTrail will change how trails can be used to capture global service events. After the change, events created by CloudFront, IAM, and AWS STS will be recorded in the region in which they were created, the AWS GovCloud (US-West) region, us-gov-west-1. This makes CloudTrail's treatment of these services consistent with that of other AWS global services.
  * To continue receiving global service events outside of AWS GovCloud (US-West), be sure to convert single-region trails using global service events outside of AWS GovCloud (US-West) into multi-region trails. Also update the region of your lookup-events API calls to view global service events. For more information about using the CLI to update or create trails for global service events and update lookup events, see Viewing CloudTrail events with the AWS CLI and Using update-trail.
  * When logging CloudTrail data events, Amazon DynamoDB API activity on streams is currently not available.
  * For all AWS GovCloud (US) accounts created after 12/15/2014, AWS CloudTrail event log delivery to Amazon S3 is enabled automatically. However, you must set up Amazon SNS notifications. You can turn off logging through the AWS CloudTrail console for the AWS GovCloud (US) Region.
  * If you are using AWS Direct Connect, you must enable CloudTrail in your standard AWS account (not your AWS GovCloud (US) account) and enable logging.
  * The Amazon S3 and Amazon SNS policy statements must refer to the ARN for AWS GovCloud (US) Regions. For more information, see Amazon Resource Names (ARNs) in GovCloud (US) Regions.

### Critical CloudTrail KSAs

**Knowledge** 

What is CloudTrail? 

CloudTrail is a record of API calls to an AWS account. By default, CloudTrail captures management events, though read and write events can be configured to be recorded as well. CloudTrail events can be viewed using the 'Event History' feature on the CloudTrail page and are either logged to CloudWatch Logs and/or S3. 

What are CloudTrail events?

An event in CloudTrail is the record of an activity in an AWS account. This activity can be an action taken by a user, role, or service that is monitorable by CloudTrail. CloudTrail events provide a history of both API and non-API account activity made through the AWS Management Console, AWS SDKs, command line tools, and other AWS services. There are three types of events that can be logged in CloudTrail: management events, data events, and CloudTrail Insights events. By default, trails log management events, but not data or Insights events. 

Management events provide visibility into management operations that are performed on resources in your AWS account. These are also known as control plane operations. Example management events include:

* Configuring security (for example, IAM AttachRolePolicy API operations)
* Registering devices (for example, Amazon EC2 CreateDefaultVpc API operations)
* Configuring rules for routing data (for example, Amazon EC2 CreateSubnet API operations)
* Setting up logging (for example, AWS CloudTrail CreateTrail API operations)

Data events provide information about the resource operations performed on or in a resource. These are also known as data plane operations. Data events are often high-volume activities. **If you suspect exfiltration through S3 enable data events on the relevent CloudTrail to capture S3 puts/gets**. The following data types are recorded:

* Amazon S3 object-level API activity (for example, GetObject, DeleteObject, and PutObject API operations) on buckets and objects in buckets
* AWS Lambda function execution activity (the Invoke API)
* Amazon DynamoDB object-level API activity on tables (for example, PutItem, DeleteItem, and UpdateItem API operations)
* Amazon S3 Object Lambda access points API activity, such as calls to CompleteMultipartUpload and GetObject
* Amazon Elastic Block Store (EBS) direct APIs, such as PutSnapshotBlock, GetSnapshotBlock, and ListChangedBlocks on Amazon EBS snapshots
* Amazon S3 API activity on access points
* Amazon DynamoDB API activity on streams
* AWS Glue API activity on tables

Data events are not logged by default when you create a trail. To record CloudTrail data events, you must explicitly add to a trail the supported resources or resource types for which you want to collect activity. 

Additional charges apply for logging data events. 

What are Insights events?

CloudTrail Insights events capture unusual API call rate or error rate activity in your AWS account. If you have Insights events enabled, and CloudTrail detects unusual activity, Insights events are logged to a different folder or prefix in the destination S3 bucket for your trail. You can also see the type of insight and the incident time period when you view Insights events on the CloudTrail console. Insights events provide relevant information, such as the associated API, error code, incident time, and statistics, that help you understand and act on unusual activity. Unlike other types of events captured in a CloudTrail trail, Insights events are logged only when CloudTrail detects changes in your account's API usage or error rate logging that differ significantly from the account's typical usage patterns. Examples of activity that might generate Insights events include:

* Your account typically logs no more than 20 Amazon S3 deleteBucket API calls per minute, but your account starts to log an average of 100 deleteBucket API calls per minute. An Insights event is logged at the start of the unusual activity, and another Insights event is logged to mark the end of the unusual activity.

* Your account typically logs 20 calls per minute to the Amazon EC2 AuthorizeSecurityGroupIngress API, but your account starts to log zero calls to AuthorizeSecurityGroupIngress. An Insights event is logged at the start of the unusual activity, and ten minutes later, when the unusual activity ends, another Insights event is logged to mark the end of the unusual activity.

* Your account typically logs less than one AccessDeniedException error in a seven-day period on the AWS Identity and Access Management API, DeleteInstanceProfile. Your account starts to log an average of 12 AccessDeniedException errors per minute on the DeleteInstanceProfile API call. An Insights event is logged at the start of the unusual error rate activity, and another Insights event is logged to mark the end of the unusual activity.

These examples are provided for illustration purposes only. Your results may vary depending on your use case.

Insights events are disabled by default when you create a trail. To record CloudTrail Insights events, you must explicitly enable Insights event collection on a new or existing trail.  

What are organization trails?

An organization trail is a configuration that enables delivery of CloudTrail events in the management account and all member accounts in an AWS Organizations organization to the same Amazon S3 bucket, CloudWatch Logs, and CloudWatch Events. Creating an organization trail helps you define a uniform event logging strategy for your organization. 

**Skills**

Critical CloudTrail operation skills: 

* View event history for an AWS account 

* Create a trail

* Learn the logging and configuration status of an existing trail. 

* View your log files

* Monitor events with CloudWatch Logs

* Additionally Log management and data events

* Enable log encryption

* Enable log file integrity
 
* Share log files with other AWS account

* Aggregate logs from multiple accounts

For more detail please see this [page](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-workflow.html). 

**Abilities**
    
Ability to critically analyze AWS API calls to trace actors in any given AWS environment. 

Ability to communicate timeline and chain of events to Administrators, Stake Holders, and Leaders. 

### AWS CLI CheatSheet 

**CloudTrail status and management in account/s**

Create a trail: 

```aws cloudtrail create-trail --name Trail1 --s3-bucket-name my-bucket --is-multi-region-trail```

Get a trail status: 

```aws cloudtrail get-trail-status --name Trail1```

Describe multiple trails: 

```aws cloudtrail describe-trails --trail-name-list Trail1 Trail2```

Make an existing trail start logging again: 

```aws cloudtrail start-logging --name Trail1```

**Query Events:** 

Query for console login events: 

```aws cloudtrail lookup-events --lookup-attributes AttributeKey=EventName,AttributeValue=ConsoleLogin```

Return N number of last events: 

```aws cloudtrail lookup-events --max-results <integer>```

Query events in a specific time range: 

```aws cloudtrail lookup-events --start-time <timestamp> --end-time <timestamp>```

Query by Attribute: 

AccessKeyId: 
```aws cloudtrail lookup-events --lookup-attributes AttributeKey=AccessKeyId,AttributeValue=AKIAIOSFODNN7EXAMPLE```

EventName:
```aws cloudtrail lookup-events --lookup-attributes AttributeKey=EventName,AttributeValue=RunInstances```

Username: 
```aws cloudtrail lookup-events --lookup-attributes AttributeKey=Username,AttributeValue=root```

### Example CloudTrail Log

The following example shows that an IAM user named Alice used the AWS CLI to call the Amazon EC2 StartInstances action by using the ec2-start-instances command for instance i-ebeaf9e2.

```
{"Records": [{
    "eventVersion": "1.0",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "EX_PRINCIPAL_ID",
        "arn": "arn:aws:iam::123456789012:user/Alice",
        "accessKeyId": "EXAMPLE_KEY_ID",
        "accountId": "123456789012",
        "userName": "Alice"
    },
    "eventTime": "2014-03-06T21:22:54Z",
    "eventSource": "ec2.amazonaws.com",
    "eventName": "StartInstances",
    "awsRegion": "us-east-2",
    "sourceIPAddress": "205.251.233.176",
    "userAgent": "ec2-api-tools 1.6.12.2",
    "requestParameters": {"instancesSet": {"items": [{"instanceId": "i-ebeaf9e2"}]}},
    "responseElements": {"instancesSet": {"items": [{
        "instanceId": "i-ebeaf9e2",
        "currentState": {
            "code": 0,
            "name": "pending"
        },
        "previousState": {
            "code": 80,
            "name": "stopped"
        }
    }]}}
}]}
```

### Training

[AWS CloudTrail Workshop](https://mng.workshop.aws/cloudtrail.html)

[CloudTrail Logging and Monitoring Workshop](https://cloudtrail-with-cloudwatch.workshop.aws/)

[CoudTrail Log Analytics Workshop](https://cloudtrail.aesworkshops.com/)

[A Security Operator's Guide to Practical CloudTrail Analysis](https://www.youtube.com/watch?v=Tr78kq-Oa70)

### Documentation 

[AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)

---

## Amazon S3 

Amazon Simple Storage Service (Amazon S3) is an object storage service that offers industry-leading scalability, data availability, security, and performance. Amazon S3 is an object storage service that stores data as objects within buckets. An object is a file and any metadata that describes the file. A bucket is a container for objects. To store your data in Amazon S3, you first create a bucket and specify a bucket name and AWS Region. Then, you upload your data to that bucket as objects in Amazon S3. Each object has a key (or key name), which is the unique identifier for the object within the bucket.

S3 provides features that you can configure to support your specific use case. For example, you can use S3 Versioning to keep multiple versions of an object in the same bucket, which allows you to restore objects that are accidentally deleted or overwritten. 

Often, logs and other files important to TD/IR will be stored in S3. S3 is a typical location to store CloudTrail logs, as well as individual service logs. 

### How Amazon S3 Differs for AWS GovCloud (US)

You cannot do a direct copy of the contents of an Amazon S3 bucket in the AWS GovCloud (US) Regions to or from another AWS Region.

If you use Amazon S3 policies, use the AWS GovCloud (US) ARN identifier. For more information, see Amazon Resource Names (ARNs) in GovCloud (US) Regions.

In AWS GovCloud (US) Regions, Amazon S3 has three endpoints. If you are processing export-controlled data, use one of the SSL endpoints. If you have FIPS requirements, use a FIPS 140-2 endpoint (https://s3-fips.us-gov-west-1.amazonaws.com or https://s3-fips.us-gov-east-1.amazonaws.com). You can access VPC endpoints for Amazon S3 over both the FIPS and non-FIPS endpoints. For a list of AWS GovCloud (US) endpoints, see Service Endpoints.

Amazon S3 bucket names are unique to the AWS GovCloud (US) Regions. Bucket names in the AWS GovCloud (US) Regions are not shared across other standard AWS Regions.

MFA delete is not available in AWS GovCloud (US) Regions.

Amazon S3 Transfer Acceleration is not available in AWS GovCloud (US).

S3 Replication Time Control (S3 RTC) is not available in AWS GovCloud (US).

Amazon S3 Object Lambda Access Points are not available in AWS GovCloud (US).


### Critical KSAs

**Knowledge** 

What is Amazon S3?

Amazon Simple Storage Service (Amazon S3) is an object storage service that offers industry-leading scalability, data availability, security, and performance. Customers of all sizes and industries can use Amazon S3 to store and protect any amount of data for a range of use cases, such as data lakes, websites, mobile applications, backup and restore, archive, enterprise applications, IoT devices, and big data analytics. Amazon S3 provides management features so that you can optimize, organize, and configure access to your data to meet your specific business, organizational, and compliance requirements. 

Access management

Amazon S3 provides features for auditing and managing access to your buckets and objects. By default, S3 buckets and the objects in them are private. You have access only to the S3 resources that you create. To grant granular resource permissions that support your specific use case or to audit the permissions of your Amazon S3 resources, you can use the following features.

* S3 Block Public Access – Block public access to S3 buckets and objects. By default, Block Public Access settings are turned on at the account and bucket level.
* AWS Identity and Access Management (IAM) – Create IAM users for your AWS account to manage access to your Amazon S3 resources. For example, you can use IAM with Amazon S3 to control the type of access a user or group of users has to an S3 bucket that your AWS account owns.
* Bucket policies – Use IAM-based policy language to configure resource-based permissions for your S3 buckets and the objects in them.
* Access control lists (ACLs) – Grant read and write permissions for individual buckets and objects to authorized users. As a general rule, we recommend using S3 resource-based policies (bucket policies and access point policies) or IAM policies for access control instead of ACLs. ACLs are an access control mechanism that predates resource-based policies and IAM. For more information about when you'd use ACLs instead of resource-based policies or IAM policies, see Access policy guidelines.
* Access Analyzer for S3 – Evaluate and monitor your S3 bucket access policies, ensuring that the policies provide only the intended access to your S3 resources.

Buckets

A bucket is a container for objects stored in Amazon S3. You can store any number of objects in a bucket and can have up to 100 buckets in your account. To request an increase, visit the Service Quotas Console. 

Every object is contained in a bucket. For example, if the object named photos/puppy.jpg is stored in the DOC-EXAMPLE-BUCKET bucket in the US West (Oregon) Region, then it is addressable using the URL https://DOC-EXAMPLE-BUCKET.s3.us-west-2.amazonaws.com/photos/puppy.jpg. For more information, see Accessing a Bucket.

When you create a bucket, you enter a bucket name and choose the AWS Region where the bucket will reside. After you create a bucket, you cannot change the name of the bucket or its Region. Bucket names must follow the bucket naming rules. You can also configure a bucket to use S3 Versioning or other storage management features. 

Objects

Objects are the fundamental entities stored in Amazon S3. Objects consist of object data and metadata. The metadata is a set of name-value pairs that describe the object. These pairs include some default metadata, such as the date last modified, and standard HTTP metadata, such as Content-Type. You can also specify custom metadata at the time that the object is stored.

An object is uniquely identified within a bucket by a key (name) and a version ID (if S3 Versioning is enabled on the bucket). For more information about objects, see Amazon S3 objects overview.

Keys

An object key (or key name) is the unique identifier for an object within a bucket. Every object in a bucket has exactly one key. The combination of a bucket, object key, and optionally, version ID (if S3 Versioning is enabled for the bucket) uniquely identify each object. So you can think of Amazon S3 as a basic data map between "bucket + key + version" and the object itself.

Every object in Amazon S3 can be uniquely addressed through the combination of the web service endpoint, bucket name, key, and optionally, a version. For example, in the URL https://DOC-EXAMPLE-BUCKET.s3.us-west-2.amazonaws.com/photos/puppy.jpg, DOC-EXAMPLE-BUCKET is the name of the bucket and /photos/puppy.jpg is the key.


**Skills**

* Create a bucket (to receive logs and store artifacts)

* List buckets in an account 

* List objects in a bucket 

* Download objects from a bucket 

**Abilities**

Locate block storage of AWS logs and retrieve them from S3 buckets. 

Navigate buckets and paths to locate IR artifacts to aid in an IR investigation. 

Share TD/IR data and artifacts between buckets/accounts. 

### AWS CLI CheatSheet 

List buckets in an account: 

```aws s3 ls``` 

List bucket contents: 

```aws s3 ls s3://<bucket>```

Copy from bucket (Download via CLI): 

```aws s3 cp s3://<bucket>/<object> <destination>```

### Training

[AWS S3 Tutorial](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html)

### Documentation 

[AWS S3 Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

---

## Amazon CloudWatch Logs 

You can use Amazon CloudWatch Logs to monitor, store, and access your log files from Amazon Elastic Compute Cloud (Amazon EC2) instances, AWS CloudTrail, Route 53, and other sources.

CloudWatch Logs enables you to centralize the logs from all of your systems, applications, and AWS services that you use, in a single, highly scalable service. You can then easily view them, search them for specific error codes or patterns, filter them based on specific fields, or archive them securely for future analysis. CloudWatch Logs enables you to see all of your logs, regardless of their source, as a single and consistent flow of events ordered by time, and you can query them and sort them based on other dimensions, group them by specific fields, create custom computations with a powerful query language, and visualize log data in dashboards. 

### How Amazon CloudWatch Logs Differs for AWS GovCloud (US)

Use SSL (HTTPS) when you make calls to the service in AWS GovCloud (US) Regions. In other AWS Regions, you can use HTTP or HTTPS. 

### Critical KSAs

**Knowledge** 

Query your log data 

You can use CloudWatch Logs Insights to interactively search and analyze your log data. You can perform queries to help you more efficiently and effectively respond to operational issues. CloudWatch Logs Insights includes a purpose-built query language with a few simple but powerful commands. We provide sample queries, command descriptions, query autocompletion, and log field discovery to help you get started. Sample queries are included for several types of AWS service logs. 

Monitor logs from Amazon EC2 instances 

You can use CloudWatch Logs to monitor applications and systems using log data. For example, CloudWatch Logs can track the number of errors that occur in your application logs and send you a notification whenever the rate of errors exceeds a threshold you specify. CloudWatch Logs uses your log data for monitoring; so, no code changes are required. For example, you can monitor application logs for specific literal terms (such as "NullReferenceException") or count the number of occurrences of a literal term at a particular position in log data (such as "404" status codes in an Apache access log). When the term you are searching for is found, CloudWatch Logs reports the data to a CloudWatch metric that you specify. Log data is encrypted while in transit and while it is at rest. 

Monitor AWS CloudTrail logged events 

You can create alarms in CloudWatch and receive notifications of particular API activity as captured by CloudTrail and use the notification to perform troubleshooting. 

Log retention 

By default, logs are kept indefinitely and never expire. You can adjust the retention policy for each log group, keeping the indefinite retention, or choosing a retention period between 10 years and one day.

Archive log data 

You can use CloudWatch Logs to store your log data in highly durable storage. The CloudWatch Logs agent makes it easy to quickly send both rotated and non-rotated log data off of a host and into the log service. You can then access the raw log data when you need it.

Log Route 53 DNS queries 

You can use CloudWatch Logs to log information about the DNS queries that Route 53 receives. 

How to start logging

Many AWS services and resources integrate natively with CloudWatch Logs. Inspect resources or services in question to ensure that they are configured to log to CloudWatch Logs. If a service or resource is not configured to log to CloudWatch Logs, enable logging though the Console, CLI, or SDK. 

Enabling logging form certain AWS Services: https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AWS-logs-and-resource-policy.html

**Skills**

* Identify all log groups in an account 

* Identify log streams within a log group 

* Filter events within a log stream to identify targeted event 

* Download log streams and relevant data from CloudWatch Logs

* Setup a service/resource to send logs to CloudWatch Logs 

**Abilities**

Analyze logs from multiple services to identify IR and forensic artifacts. 

Locate relevant log data to aid in investigation. 

Determine whether a specific service or resource is being logged, and start logging if necessary. 

### CloudWatch Logs CLI and Bash CheatSheet 

To Install CloudWatch logs agent on an Ubuntu instance follow these steps: 
```
curl https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py -O

sudo python ./awslogs-agent-setup.py --region us-east-1

```

Search log entries using the AWS CLI

```
aws logs filter-log-events --log-group-name my-group [--log-stream-names LIST_OF_STREAMS_TO_SEARCH] [--filter-pattern VALID_METRIC_FILTER_PATTERN]
```

Search log entries over a given time range using the AWS CLI

```
aws logs filter-log-events --log-group-name my-group [--log-stream-names LIST_OF_STREAMS_TO_SEARCH] [--start-time 1482197400000] [--end-time 1482217558365] [--filter-pattern VALID_METRIC_FILTER_PATTERN]
```

### Training

[CloudWatch Logs Management and Governance Workshop](https://mng.workshop.aws/cloudwatch.html)

[Monitor All Your Things: Amazon CloudWatch in Action with BBC](https://www.youtube.com/watch?v=uuBuc6OAcVY)

### Documentation 

[CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)

---

## AWS Athena 

AWS Athena is an interactive query service that makes it easy to analyze data directly in Amazon Simple Storage Service (Amazon S3) using standard SQL. With a few actions in the AWS Management Console, you can point Athena at your data stored in Amazon S3 and begin using standard SQL to run ad-hoc queries and get results in seconds.

Athena is serverless, so there is no infrastructure to set up or manage, and you pay only for the queries you run. Athena scales automatically—running queries in parallel—so results are fast, even with large datasets and complex queries. 

Athena helps you analyze unstructured, semi-structured, and structured data stored in Amazon S3. Examples include CSV, JSON, or columnar data formats such as Apache Parquet and Apache ORC. You can use Athena to run ad-hoc queries using ANSI SQL, without the need to aggregate or load the data into Athena.

### How Athena Differs for AWS GovCloud (US)

This service has no differences between AWS GovCloud (US) Regions and the standard AWS Regions. 

### Critical KSAs

**Knowledge** 

Databases 

A database in Athena is a logical grouping for tables you create in it. 

Tables

For each dataset that you'd like to query, Athena must have an underlying table it will use for obtaining and returning query results. Therefore, before querying data, a table must be registered in Athena. The registration occurs when you either create tables automatically or manually. 

To create a table, you can run DDL statements in the Athena console, using a JDBC or an ODBC driver, or using the Athena Add table wizard. 

After you create a table, you can use SQL SELECT statements to query it, including getting specific file locations for your source data. Your query results are stored in Amazon S3 in the query result location that you specify. 

SQL 

Athena queries use Structured Query Language (SQL). [Please see the SQL Reference for Athena for more information](https://docs.aws.amazon.com/athena/latest/ug/ddl-sql-reference.html)

**Skills** 

* Create a database 

* Create a table 

* Run Athena Queries 

* Partition data before running queries on it 

**Abilities**

Perform queries on logs stored in S3 during incident response to identify targeted activity and add to the killchain. 

### Athena CheatSheet 

The following resource is one of the highest quality Athena "cheat sheets" available: 

[East Timor AWS Incident Response Guide](https://easttimor.github.io/aws-incident-response/)

Create table for CloudTrail in Athena using manual partitioning. Please note - replace names and account numbers as applicable: 

```
CREATE EXTERNAL TABLE cloudtrail_logs (
eventversion STRING,
useridentity STRUCT<
               type:STRING,
               principalid:STRING,
               arn:STRING,
               accountid:STRING,
               invokedby:STRING,
               accesskeyid:STRING,
               userName:STRING,
sessioncontext:STRUCT<
attributes:STRUCT<
               mfaauthenticated:STRING,
               creationdate:STRING>,
sessionissuer:STRUCT<  
               type:STRING,
               principalId:STRING,
               arn:STRING, 
               accountId:STRING,
               userName:STRING>>>,
eventtime STRING,
eventsource STRING,
eventname STRING,
awsregion STRING,
sourceipaddress STRING,
useragent STRING,
errorcode STRING,
errormessage STRING,
requestparameters STRING,
responseelements STRING,
additionaleventdata STRING,
requestid STRING,
eventid STRING,
resources ARRAY<STRUCT<
               ARN:STRING,
               accountId:STRING,
               type:STRING>>,
eventtype STRING,
apiversion STRING,
readonly STRING,
recipientaccountid STRING,
serviceeventdetails STRING,
sharedeventid STRING,
vpcendpointid STRING
)
PARTITIONED BY (region string, year string, month string, day string)
ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION 's3://CloudTrail_bucket_name/AWSLogs/Account_ID/CloudTrail/';
```
Identify Security Group Changes on an ENI: 

```select eventname, useridentity.username, sourceIPAddress, eventtime, requestparameters from cloudtrail_logs
where (requestparameters like '%sg-5887f224%' or requestparameters like '%sg-e214609e%' or requestparameters like '%eni-6c5ca5a8%')
and eventtime > '2017-02-15T00:00:00Z'
order by eventtime asc;
```

### Training

[Athena Workshop](https://athena-in-action.workshop.aws/)

### Documentation 

[Athena User Guide](https://docs.aws.amazon.com/athena/latest/ug/what-is.html)

---

## VPC Flow Logs - Amazon Virtual Private Cloud 

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data can be published to Amazon CloudWatch Logs or Amazon S3. After you create a flow log, you can retrieve and view its data in the chosen destination.

Flow logs can help you with a number of tasks, such as:

    Diagnosing overly restrictive security group rules

    Monitoring the traffic that is reaching your instance

    Determining the direction of the traffic to and from the network interfaces

Flow log data is collected outside of the path of your network traffic, and therefore does not affect network throughput or latency. You can create or delete flow logs without any risk of impact to network performance. 

### How VPC FlowLogs Differs for AWS GovCloud (US)

The following field is not permitted to contain export-controlled data:

    Destination log group name

### Critical KSAs

**Knowledge** 

You can create a flow log for a VPC, a subnet, or a network interface. If you create a flow log for a subnet or VPC, each network interface in that subnet or VPC is monitored.

Flow log data for a monitored network interface is recorded as flow log records, which are log events consisting of fields that describe the traffic flow. 

After you create a flow log, it can take several minutes to begin collecting and publishing data to the chosen destinations. Flow logs do not capture real-time log streams for your network interfaces.

You can create flow logs for network interfaces that are created by other AWS services, such as:

    * Elastic Load Balancing
    * Amazon RDS
    * Amazon ElastiCache
    * Amazon Redshift
    * Amazon WorkSpaces
    * NAT gateways
    * Transit gateways
    * Etc... 

Not all traffic is monitored, e.g. the following traffic is excluded:

    * Traffic that goes to Route53.
    * Traffic generated for Windows license activation.
    * Traffic to and from 169.254.169.254 (instance metadata).
    * Traffic to and from 169.254.169.123 for the Amazon Time Sync Service.
    * DHCP traffic.
    * Traffic to the reserved IP address for the default VPC router.

A flow log record represents a network flow in your VPC. By default, each record captures a network internet protocol (IP) traffic flow (characterized by a 5-tuple on a per network interface basis) that occurs within an aggregation interval, also referred to as a capture window. 

The aggregation interval is the period of time during which a particular flow is captured and aggregated into a flow log record. By default, the maximum aggregation interval is 10 minutes. When you create a flow log, you can optionally specify a maximum aggregation interval of 1 minute. Flow logs with a maximum aggregation interval of 1 minute produce a higher volume of flow log records than flow logs with a maximum aggregation interval of 10 minutes. 

VPC FlowLogs are typically either stored in S3 or CloudWatch Logs, or both. 

**Skills**

* Create a VPC Flow Log 

* Specify specific fields within a Flow Log

* Send Flow Logs to CloudWatch Logs and/or S3

* Set an aggregation interval for a Flow Log 

**Abilities**

Analyze logged network traffic between Elastic Network Interfaces within a VPC to identify targeted traffic. 

### Examples of a VPC Flow Log: 

The Version 2 Default Flow Log pattern for fields is as follows: 

```version, account-id, interface-id, srcaddr, dstaddr, srcport, dstport, protocol, packets, bytes, start, end, action, log-status```

In this example, SSH traffic (destination port 22, TCP protocol) to network interface eni-1235b8ca123456789 in account 123456789010 was allowed. 

```2 123456789010 eni-1235b8ca123456789 172.31.16.139 172.31.16.21 20641 22 6 20 4249 1418530010 1418530070 ACCEPT OK``` 

In this example, RDP traffic (destination port 3389, TCP protocol) to network interface eni-1235b8ca123456789 in account 123456789010 was rejected. 

```2 123456789010 eni-1235b8ca123456789 172.31.9.69 172.31.9.12 49761 3389 6 20 4249 1418530010 1418530070 REJECT OK```

In this example, no data was recorded during the aggregation interval.

```2 123456789010 eni-1235b8ca123456789 - - - - - - - 1431280876 1431280934 - NODATA```

### AWS CLI CheatSheet

Create a Flow Log to capture all rejected traffic for a specific network interface: 

```
aws ec2 create-flow-logs \
    --resource-type NetworkInterface \
    --resource-ids eni-11223344556677889 \
    --traffic-type REJECT \
    --log-group-name my-flow-logs \
    --deliver-logs-permission-arn arn:aws:iam::123456789101:role/publishFlowLogs
```

Display details for all flow logs in account/region: 

```aws ec2 describe-flow-logs```

Get log events from a specific stream: 

```
aws logs get-log-events --log-group-name my-logs --log-stream-name 20150601
```

### Training

[VPC Traffic Mirroring Workshop](https://www.awsnetworkshops.com/020_introduction/vpcflowlogs/)

### Documentation 

[VPC User Guide - Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

---

## Amazon Route53 Resolver DNS Logs 

When you create a VPC using Amazon VPC, Route 53 Resolver automatically uses a Resolver on the VPC to answer DNS queries for local Amazon VPC domain names for EC2 instances (ec2-192-0-2-44.compute-1.amazonaws.com) and records in private hosted zones (acme.example.com). For all other domain names, Resolver performs recursive lookups against public name servers. 

### How Route53 Resolver Logs Differs for AWS GovCloud (US)

This service has no differences between AWS GovCloud (US) Regions and the standard AWS Regions. 

### Critical KSAs

**Knowledge** 

When you create a VPC using Amazon VPC, Route 53 Resolver automatically uses a Resolver on the VPC to answer DNS queries for local Amazon VPC domain names for EC2 instances (ec2-192-0-2-44.compute-1.amazonaws.com) and records in private hosted zones (acme.example.com). For all other domain names, Resolver performs recursive lookups against public name servers. 

AmazonProvidedDNS is an Amazon Route 53 Resolver server, and this option enables DNS for instances that need to communicate over the VPC's internet gateway. The DNS server does not reside within a specific subnet or Availability Zone in a VPC. The string AmazonProvidedDNS maps to a DNS server running on 169.254.169.253 (and the reserved IP address at the base of the VPC IPv4 network range, plus two) and fd00:ec2::253. For example, the DNS Server on a 10.0.0.0/16 network is located at 10.0.0.2. For VPCs with multiple IPv4 CIDR blocks, the DNS server IP address is located in the primary CIDR block. 

When you create a VPC, the Route 53 Resolver that is created by default, maps to a DNS server that runs on a reserved IP address for the VPC network range, plus 2. For example, the DNS Server on a 10.0.0.0/16 network is located at 10.0.0.2. For VPCs with multiple IPv4 CIDR blocks, the DNS server IP address is located in the primary CIDR block. The DNS server does not reside within a specific subnet or Availability Zone in a VPC. For more information, see DNS support for your VPC and Amazon DNS server in the Amazon Virtual Private Cloud User Guide.

The Resolver additionally contains endpoints that you configure to answer DNS queries to and from your on-premises environment.

You also can integrate DNS resolution between Resolver and DNS resolvers on your network by configuring forwarding rules. Your network can include any network that is reachable from your VPC, such as the following:

    The VPC itself

    Another peered VPC

    An on-premises network that is connected to AWS with AWS Direct Connect, a VPN, or a network address translation (NAT) gateway

Before you start to forward queries, you create Resolver inbound and/or outbound endpoints in the connected VPC.

You can log the following DNS queries:

* Queries that originate in Amazon Virtual Private Cloud VPCs that you specify, as well as the responses to those DNS queries.
* Queries from on-premises resources that use an inbound Resolver endpoint.
* Queries that use an outbound Resolver endpoint for recursive DNS resolution.
* Queries that use Route 53 Resolver DNS Firewall rules to block, allow, or monitor domain lists.

**Skills**

* Configure Query Logging to send logs to either S3 or CloudWatch Logs

**Abilities**

* Capture and analyze DNS traffic within a VPC 

### Query Log Example: 

```          
      {
        "srcaddr": "4.5.64.102",
        "vpc_id": "vpc-7example",
        "answers": [
            {
                "Rdata": "203.0.113.9",
                "Type": "PTR",
                "Class": "IN"
            }
        ],
        "firewall_rule_group_id": "rslvr-frg-01234567890abcdef",
        "firewall_rule_action": "BLOCK",
        "query_name": "15.3.4.32.in-addr.arpa.",
        "firewall_domain_list_id": "rslvr-fdl-01234567890abcdef",
        "query_class": "IN",
        "srcids": {
            "instance": "i-0d15cd0d3example"
        },
        "rcode": "NOERROR",
        "query_type": "PTR",
        "transport": "UDP",
        "version": "1.100000",
        "account_id": "111122223333",
        "srcport": "56067",
        "query_timestamp": "2021-02-04T17:51:55Z",
        "region": "us-east-1"
    }
```

### AWS CLI CheatSheet 

Create a query log and send to a specific ARN

```aws route53resolver create-resolver-query-log-config --name <value> --destination-arn <value>```

### Training

[Log your VPC DNS queries with Route 53 Resolver Query Logs](https://aws.amazon.com/blogs/aws/log-your-vpc-dns-queries-with-route-53-resolver-query-logs/)

### Documentation 

[AWS VPC - Resolver query logging](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs.html)

---

## Amazon CloudWatch Events/Event Bridge 

Amazon EventBridge (**formerly CloudWatch Events**) is a serverless event bus service that makes it easy to connect your applications with data from a variety of sources. EventBridge delivers a stream of real-time data from your own applications, and AWS services and routes that data to targets such as AWS Lambda. You can set up routing rules to determine where to send your data to build application architectures that react in real time to all of your data sources. EventBridge allows you to build event driven architectures, which are loosely coupled and distributed.

Existing CloudWatch Events users can access their existing default bus, rules, and events in the new EventBridge console and in the CloudWatch Events console. EventBridge uses the same CloudWatch Events API, so all of your existing CloudWatch Events API usage remains the same. 

Use EventBridge to listen for specific API Calls in CloudTrail or events like a GuardDuty finding and connect these events to an alerting mechanism like SNS or send them to a log group or SIEM.  

### How Event Bridge Differs for AWS GovCloud (US)

Use SSL (HTTPS) when you make calls to the service in AWS GovCloud (US) Regions. In other AWS Regions, you can use HTTP or HTTPS.

Amazon EventBridge Schema Registry is not supported in AWS GovCloud (US) Regions

Setting up partner event sources to receive events from Software-as-a-Service (SaaS) Partner applications and services is not supported in AWS GovCloud (US) Regions

Support for Dead Letter Queues (DLQs) is not supported in AWS GovCloud (US) Regions

### Critical KSAs

**Knowledge** 

An **event** indicates a change in an environment such as an AWS environment, a SaaS partner service or application, or one of your applications or services. The following are examples of events:

* Amazon EC2 generates an event when the state of an instance changes from pending to running.
* Amazon EC2 Auto Scaling generates events when it launches or terminates instances.
* AWS CloudTrail publishes events when you make API calls.

For a list of services that generate events, including sample events from each service, see [Events from AWS services](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-service-event.html). 

An event bus is a pipeline that receives events. Rules associated with the event bus evaluate events as they arrive. Each rule checks whether an event matches the rule's criteria. You associate a rule with a specific event bus, so the rule only applies to events received by that event bus. 

The most common event buses are:

* The default event bus in each account receives events from AWS services. - This is the most common scenario. Many accounts only will have the default event bus. 
* A custom event bus sends events to or receives events from a different account.
* A custom event bus sends events to or receives events from a different Region to aggregate events in a single location.
* A partner event bus receives events from a SaaS partner. For more information, see Receiving events from a SaaS partner with Amazon EventBridge.

A rule matches incoming events and sends them to targets for processing. A single rule can send an event to multiple targets, which then run in parallel. Rules are based either on an event pattern or a schedule. An event pattern defines the event structure and the fields that a rule matches. Rules that are based on a schedule perform an action at regular intervals. 

A target is a resource or endpoint that EventBridge sends an event to when the event matches the event pattern defined for a rule. The rule processes the event data and sends the pertinent information to the target. To deliver event data to a target, EventBridge needs permission to access the target resource. You can define up to five targets for each rule. 

Some examples of targets include: 

* An SNS topic configured to notify when a specific CloudTrail API call is detected.
* A lambda function configured to respond to a specific event. 
* A Kinesis Firehose for events matching a pattern. 

You can customize the text from an event before EventBridge passes the event to the target of a rule. Using the input transformer in the console or the API, you define variables that use JSON path to reference values in the original event source. You can define up to 100 variables, assigning each a value from the input. Then you can use those variables in the Input Template as <variable-name>. For more information see the [Transforming Target Input page](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-transform-target-input.html#eb-transform-input-examples).

**Skills**

* Identify Event Busses in the account/region

* Create an EventBridge rule to detect specific CloudTrail API calls 

* Create EventBridge rules to detect other types of events 

* Create input transforms to control the format in which events are delivered to targets

**Abilities**

Detect specific events and API calls within an AWS Account/Region and send these events off to notification or automation infrastructure. 

### AWS CLI CheatSheet 

Create a rule: 

This example creates a rule that triggers when any EC2 instance in the region changes state:

```aws events put-rule --name "EC2InstanceStateChanges" --event-pattern "{\"source\":[\"aws.ec2\"],\"detail-type\":[\"EC2 Instance State-change Notification\"]}"  --role-arn "arn:aws:iam::123456789012:role/MyRoleForThisRule"``` 

This example creates a rule that triggers when any EC2 instance in the region is stopped or terminated:|

```aws events put-rule --name "EC2InstanceStateChangeStopOrTerminate" --event-pattern "{\"source\":[\"aws.ec2\"],\"detail-type\":[\"EC2 Instance State-change Notification\"],\"detail\":{\"state\":[\"stopped\",\"terminated\"]}}" --role-arn "arn:aws:iam::123456789012:role/MyRoleForThisRule"```

Add targets to rules: 

This example adds a Lambda function as the target of a rule:
```aws events put-targets --rule DailyLambdaFunction --targets "Id"="1","Arn"="arn:aws:lambda:us-east-1:123456789012:function:MyFunctionName"```

This example sets an Amazon Kinesis stream as the target, so that events caught by this rule are relayed to the stream:

```aws events put-targets --rule EC2InstanceStateChanges --targets "Id"="1","Arn"="arn:aws:kinesis:us-east-1:123456789012:stream/MyStream","RoleArn"="arn:aws:iam::123456789012:role/MyRoleForThisRule"```

Test an event pattern: 

This example tests whether the pattern "source:com.mycompany.myapp" matches the specified event. In this example, the output would be "true":

```aws events test-event-pattern --event-pattern "{\"source\":[\"com.mycompany.myapp\"]}" --event "{\"id\":\"1\",\"source\":\"com.mycompany.myapp\",\"detail-type\":\"myDetailType\",\"account\":\"123456789012\",\"region\":\"us-east-1\",\"time\":\"2017-04-11T20:11:04Z\"}"```

### Training

[Event-Driven with EventBridge](https://event-driven-architecture.workshop.aws/2-event-bridge.html)

### Documentation 

[EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)

---

## Amazon CloudWatch Metrics

Metrics are the fundamental concept in CloudWatch. A metric represents a time-ordered set of data points that are published to CloudWatch. Think of a metric as a variable to monitor, and the data points as representing the values of that variable over time. For example, the CPU usage of a particular EC2 instance is one metric provided by Amazon EC2. The data points themselves can come from any application or business activity from which you collect data. 

### How CloudWatch Metrics Differs for AWS GovCloud (US)

The GetMetricWidgetImage API is not available

Dashboard sharing is not available in AWS GovCloud (US) Regions

### Critical KSAs

**Knowledge** 

Metrics are data about the performance of your systems. By default, many services provide free metrics for resources (such as Amazon EC2 instances, Amazon EBS volumes, and Amazon RDS DB instances). You can also enable detailed monitoring for some resources, such as your Amazon EC2 instances, or publish your own application metrics. Amazon CloudWatch can load all the metrics in your account (both AWS resource metrics and application metrics that you provide) for search, graphing, and alarms. 

Metrics exist only in the Region in which they are created. Metrics cannot be deleted, but they automatically expire after 15 months if no new data is published to them. Data points older than 15 months expire on a rolling basis; as new data points come in, data older than 15 months is dropped.

Metrics are uniquely defined by a name, a namespace, and zero or more dimensions. Each data point in a metric has a time stamp, and (optionally) a unit of measure. You can retrieve statistics from CloudWatch for any metric. 

Metric data is kept for 15 months, enabling you to view both up-to-the-minute data and historical data. 

**Skills**

* Perform a query using CloudWatch Metrics Insights 

* Create a metrics dashboard 

**Abilities**

Collect and monitor metrics within AWS to identify anomalous activity 

Graph metrics to aid in visual story telling to communicate TD/IR information 

### AWS CLI CheatSheet 

The following list-metrics example displays the metrics for Amazon SNS.

```
aws cloudwatch list-metrics \
    --namespace "AWS/SNS"
    
```

The following example uses the get-metric-statistics command to get the CPU utilization for an EC2 instance with the ID i-abcdef.

```
aws cloudwatch get-metric-statistics --metric-name CPUUtilization --start-time 2014-04-08T23:18:00Z --end-time 2014-04-09T23:18:00Z --period 3600 --namespace AWS/EC2 --statistics Maximum --dimensions Name=InstanceId,Value=i-abcdef 
```

### Training

[AWS Management and Governance Tools Workshop](https://mng.workshop.aws/cloudwatch/metricsfromlogs.html)

### Documentation 

[CloudWatch Metrics User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)

---

## Amazon CloudWatch Alarms 

You can use an alarm to automatically initiate actions on your behalf. An alarm watches a single metric over a specified time period, and performs one or more specified actions, based on the value of the metric relative to a threshold over time. The action is a notification sent to an Amazon SNS topic or an Auto Scaling policy. You can also add alarms to dashboards.

Alarms invoke actions for sustained state changes only. CloudWatch alarms do not invoke actions simply because they are in a particular state. The state must have changed and been maintained for a specified number of periods.

When creating an alarm, select an alarm monitoring period that is greater than or equal to the metric's resolution. For example, basic monitoring for Amazon EC2 provides metrics for your instances every 5 minutes. When setting an alarm on a basic monitoring metric, select a period of at least 300 seconds (5 minutes). Detailed monitoring for Amazon EC2 provides metrics for your instances with a resolution of 1 minute. When setting an alarm on a detailed monitoring metric, select a period of at least 60 seconds (1 minute). 

### How CloudWatch Alarms Differs for AWS GovCloud (US)

The GetMetricWidgetImage API is not available

Dashboard sharing is not available in AWS GovCloud (US) Regions

### Critical KSAs

**Knowledge** 

When you’re collecting the metrics you need from your application workload in CloudWatch, you need to determine what you want to alarm on. When to alarm is an important decision: you are effectively saying, “The system has hit a problem that someone should know about.” If you alarm too much and too often, alarms tend to get ignored – the exact opposite of what you want. If one important alarm is lost in a sea of less-critical alarms, a service disruption could go on longer than required and have a negative impact on your business.

When you create an alarm, you first select a metric. Keep three key concepts in mind:

* The threshold for the alarm – For instance, setting an Amazon EC2 Auto Scaling event to scale out when CPU utilization exceeds 90% may be too high, because you may not be able to get new resources started in time before the CPU saturates at 100%. On the other hand, setting the scale-out event at 50% CPU utilization may be too low, causing more resources to be added when the CPU would have only increased to 65%, which the workload would have handled without needing a scale-out event.
* The statistic you’re measuring – This statistic defines if you’re looking at the metric in question as an average value, summed value, a maximum value, a minimum value, a P90 value, or a sample count. Looking at the metric value with different types of statistical variance gives you more flexibility in setting alarms. For instance, percentiles are very powerful in making good approximations and can provide insights into the consistency of your applications’ response time compared to that of an average.
* The period, threshold, and data points to alarm – Understanding how these features work together helps make sure that you don’t over-alarm, and that the system has a chance to self-heal before an alarm is triggered.

**Skills**

* Create a CloudWatch Metric and Associated Alarm 
* Link a CloudWatch Alarm to a notification or 

**Abilities**

Utilize CloudWatch Alarms in order to identify specific states within AWS resources or AWS applications in order to detect anomalous activity. 

### AWS CLI CheatSheet 

The following example uses the put-metric-alarm command to send an Amazon Simple Notification Service email message when CPU utilization exceeds 70 percent:

```aws cloudwatch put-metric-alarm --alarm-name cpu-mon --alarm-description "Alarm when CPU exceeds 70 percent" --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 70 --comparison-operator GreaterThanThreshold  --dimensions "Name=InstanceId,Value=i-12345678" --evaluation-periods 2 --alarm-actions arn:aws:sns:us-east-1:111122223333:MyTopic --unit Percent```

The following example illustrates how to specify multiple dimensions. Each dimension is specified as a Name/Value pair, with a comma between the name and the value. Multiple dimensions are separated by a space:

```aws cloudwatch put-metric-alarm --alarm-name "Default_Test_Alarm3" --alarm-description "The default example alarm" --namespace "CW EXAMPLE METRICS" --metric-name Default_Test --statistic Average --period 60 --evaluation-periods 3 --threshold 50 --comparison-operator GreaterThanOrEqualToThreshold --dimensions Name=key1,Value=value1 Name=key2,Value=value2```

Retrieves the specified alarms. You can filter the results by specifying a prefix for the alarm name, the alarm state, or a prefix for any action.

```aws cloudwatch describe-alarms --alarm-names "myalarm"```

The following example uses the describe-alarm-history command to retrieve history for the Amazon CloudWatch alarm named "myalarm":

```aws cloudwatch describe-alarm-history --alarm-name "myalarm" --history-item-type StateUpdate```

The following example uses the set-alarm-state command to temporarily change the state of an Amazon CloudWatch alarm named "myalarm" and set it to the ALARM state for testing purposes:

aws cloudwatch set-alarm-state --alarm-name "myalarm" --state-value ALARM --state-reason "testing purposes"

### Training

[AWS Management and Governance Tools Workshop](https://mng.workshop.aws/cloudwatch/alarms.html)

[Use Amazon EventBridge rules to run AWS Systems Manager automation in response to CloudWatch alarms](https://aws.amazon.com/blogs/mt/use-amazon-eventbridge-rules-to-run-aws-systems-manager-automation-in-response-to-cloudwatch-alarms/)

### Documentation 

[CloudWatch Alarms User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)

---

## AWS Security Hub

AWS Security Hub provides you with a comprehensive view of your security state in AWS and helps you check your environment against security industry standards and best practices.

Security Hub collects security data from across AWS accounts, services, and supported third-party partner products and helps you analyze your security trends and identify the highest priority security issues. 

### How AWS Security Hub Differs for AWS GovCloud (US)

Not all integrations with AWS Services and third-party partners are available in the AWS GovCloud (US) Region.

For a list of the supported integrations in the AWS GovCloud (US) Region, see Integrations that are supported in AWS GovCloud (US-East) and AWS GovCloud (US-West).

Not all security controls are supported in the AWS GovCloud (US) Region. For details, see the following lists in the AWS Security Hub User Guide.

* [Controls that are not supported in AWS GovCloud (US-East)](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-regions.html#securityhub-control-support-govuseast1)

* [Controls that are not supported in AWS GovCloud (US-West)](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-regions.html#securityhub-control-support-govuseast1)


Cross-Region finding aggregation is not supported in the AWS GovCloud (US) Region.

### Security Hub Critical KSAs

**Knowledge** 

You can enable Security Hub from the AWS Management Console or the API.

Findings: 

In AWS Security Hub, a finding can originate from one of the following types of finding providers.

* An enabled integration with another AWS service

* An enabled integration with a third-party provider

* An enabled control in Security Hub

An example of a finding may be an alert from GuardDuty, or a notification from Config that a resource is out of compliance. 

Security Hub also automatically updates and deletes findings.

All findings are automatically deleted if they were not updated in the past 90 days.

Security Standards: 

AWS Security Hub uses service-linked AWS Config rules to perform most of its security checks for controls. Many security checks use AWS Config managed or custom rules to establish the compliance requirements. To run these checks, you must have AWS Config enabled. [See How Security Hub uses AWS Config rules to run security checks](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-awsconfigrules.html). Others use custom Lambda functions, which are managed by Security Hub and are not visible to customers. 

To support these controls, AWS Config must be enabled on all accounts – both the master account and member accounts – in each Region where Security Hub is enabled. AWS Config must be configured to record at a minimum the resources that are required for the standards that you have enabled.

[AWS Config resources required for CIS controls](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-cis-config-resources.html)

[AWS Config resources required for PCI DSS controls](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-cis-config-resources.html)

[AWS Config resources required for AWS Foundational Security Best Practices controls](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-cis-config-resources.html)

Security Hub recommends that you enable resource recording in AWS Config before you enable Security Hub standards. If Security Hub tries to run security checks when resource recording is not enabled, the checks return errors.

Security Hub does not manage AWS Config for you. If you already have AWS Config enabled, you can continue to configure its settings through the AWS Config console or APIs. 

Automated Response and Remediation: 

You can create AWS Security Hub custom actions to automate Security Hub with Amazon EventBridge. For custom actions, the event type is Security Hub Findings - Custom Action. 

With Amazon EventBridge, you can automate your AWS services to respond automatically to system events such as application availability issues or resource changes. Events from AWS services are delivered to EventBridge in near-real time and on a guaranteed basis. You can write simple rules to indicate which events you are interested in and what automated actions to take when an event matches a rule. The actions that can be automatically triggered include the following:

  * Invoking an AWS Lambda function

  * Invoking the Amazon EC2 run command

  * Relaying the event to Amazon Kinesis Data Streams

  * Activating an AWS Step Functions state machine

  * Notifying an Amazon SNS topic or an Amazon SQS queue

  * Sending a finding to a third-party ticketing, chat, SIEM, or incident response and management tool

Security Hub automatically sends all new findings and all updates to existing findings to EventBridge as EventBridge events. You can also create custom actions that allow you to send selected findings and insight results to EventBridge.

You then configure EventBridge rules to respond to each type of event.

**Skills**

* Enable Security Hub for a Region/Account/Organization 

* View findings and be able to sort them by Severity, Workflow Status, Record State, Region, Source, Resource, and Time 

* Send findings to Security Hub Custom Action for auto remediation 

* Integrate necessary partner products with Security Hub 

* Enable Security Hub Security Standards to measure account compliance 

**Abilities**

Aggregate security findings from AWS and partner products, and send them to specific workflows. 

View a consolidated dashboard to understand account compliance status 

### AWS CLI CheatSheet 

The following enable-security-hub example enables AWS Security Hub for the requesting account. It configures Security Hub to enable the default standards. For the hub resource, it assigns the value Security to the tag Department.

```
aws securityhub enable-security-hub \
    --enable-default-standards \
    --tags '{"Department": "Security"}'
    
```

The following get-findings example returns findings for the PCI DSS standard.

```
aws securityhub get-findings \
    --filters '{"GeneratorId":[{"Value": "pci-dss","Comparison":"PREFIX"}]}' \
    --max-items 1
```

The following create-action-target example creates a custom action. It provides the name, description, and identifier for the action. **please note** that an EventBridge rule will be needed to link findings to this custom action. 

```
aws securityhub create-action-target \
    --name "Send to remediation" \
    --description "Action to send the finding for remediation tracking" \
    --id "Remediation"
``` 

### Training

[Integration, Prioritization, and Response with AWS Security Hub](https://security-hub-workshop.awssecworkshops.com/)

[Scaling Threat Detection and Response in AWS](https://security-hub-workshop.awssecworkshops.com/)

[How to act on your security and compliance alerts with AWS Security Hub - Re:Enforce Session](https://www.youtube.com/watch?v=HsWtPG_rTak)

### Documentation 

[Security Hub User Guide](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html)

---

## Amazon GuardDuty

Amazon GuardDuty is a threat detection service that continuously monitors your AWS accounts and workloads for malicious activity and delivers detailed security findings for visibility and remediation. GuardDuty uses several techniques to identify indicators of compromise, such as machine learning (ML), anomaly detection, and integrated threat intelligence to identify and prioritize potential threats. GuardDuty is capable of analyzing tens of billions of events across multiple AWS data sources, such as AWS CloudTrail event logs, Amazon Virtual Private Cloud (VPC) flow Logs, and DNS query logs. 

GuardDuty is a fully managed service. Once it is enabled, it will monitor the specific regions within an account it is activated in. No special configuration is needed, but customer managed IP lists or exceptions can be added to GuardDuty. 

### How GuardDuty Differs for AWS GovCloud (US)

Using AWS CloudFormation to set up Amazon GuardDuty resources in AWS GovCloud (US) is not currently supported.

The Enable GuardDuty StackSet feature to enable Amazon GuardDuty in multiple accounts at the same time is currently unavailable due to the lack of AWS CloudFormation support currently in AWS GovCloud (US). To bypass this limitation, use the Python scripts described in the Amazon GuardDuty documentation.

Cross-region data transfer is not supported.
Member invite notifications through Personal Health Dashboard and email is not supported.

The following findings are not available inAWS GovCloud (US):

*  CredentialAccess:IAMUser/AnomalousBehavior
*  DefenseEvasion:IAMUser/AnomalousBehavior
*  Discovery:IAMUser/AnomalousBehavior
*  Exfiltration:IAMUser/AnomalousBehavior
*  Impact:IAMUser/AnomalousBehavior
*  InitialAccess:IAMUser/AnomalousBehavior
*  Persistence:IAMUser/AnomalousBehavior
*  PrivilegeEscalation:IAMUser/AnomalousBehavior

The following retired findings are still active in AWS GovCloud (US) as replacements for the above:

*  Discovery:S3/BucketEnumeration.Unusual
*  Impact:S3/ObjectDelete.Unusual
*  Impact:S3/PermissionsModification.Unusual
*  Persistence:IAMUser/NetworkPermissions
*  Persistence:IAMUser/ResourcePermissions
*  Persistence:IAMUser/UserPermissions
*  PrivilegeEscalation:IAMUser/AdministrativePermissions
*  Recon:IAMUser/NetworkPermissions
*  Recon:IAMUser/ResourcePermissions
*  Recon:IAMUser/UserPermissions
*  ResourceConsumption:IAMUser/ComputeResources
*  Stealth:IAMUser/LoggingConfigurationModified
*  UnauthorizedAccess:IAMUser/ConsoleLogin

### Critical KSAs

**Knowledge** 

GuardDuty is a monitoring service that analyzes AWS CloudTrail management and Amazon S3 data events, VPC Flow Logs, and DNS logs to generate security findings for your account. Once GuardDuty is enabled, it starts monitoring your environment immediately. GuardDuty can be disabled at any time to stop it from processing all AWS CloudTrail events, VPC Flow Logs, and DNS logs. You do not need to enable AWS CloudTrail, Amazon S3 data events, VPC Flow Logs, and DNS logs before starting GuardDuty. Amazon GuardDuty pulls independent streams of data directly from those services. **Please note** that is best practice to enable these logs and monitor them independently from GuardDuty. 

GuardDuty is a Regional service, meaning any of the configuration procedures you follow on this page must be repeated in each region that you want to monitor with GuardDuty. We highly recommend that you enable GuardDuty in all supported AWS Regions.

All GuardDuty findings are associated with a detector, which is an object that represents the GuardDuty service. The detector is a regional entity, and a unique detector is required in each region GuardDuty operates in. When you enable GuardDuty in a region a new detector with a unique 32 alphanumeric detector ID is generated in that region. The detector ID format looks like this: ```12abc34d567e8fa901bc2d34e56789f0```

A finding is a potential security issue discovered by GuardDuty. Findings are displayed in the GuardDuty console and contain a detailed description of the security issue. You can also retrieve your generated findings by calling the GetFindings and ListFindings API operations. GuardDuty findings are classified into multiple [families](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types-active.html) and [severities](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings-summary.html). 

Suppression rules allow you to create very specific combinations of attributes to suppress findings. For example, you can define a rule through the GuardDuty filter to auto-archive Recon:EC2/Portscan from only those instances in a specific VPC, running a specific AMI, or with a specific EC2 tag.

GuardDuty natively integrates with Security Hub.

**Skills**

* Enable GuardDuty for a given account and region 

* Sort findings by type, resource, severity, and time 

* Archive and export findings 

* Suppress findings 

* Add a Trusted IP List 

* ADd a Threat List 

**Abilities**

Enable continuous threat monitoring in a given AWS Account and Region to detect malicious activity.

Import threat and exception lists to focus detection. 

Review GuardDuty findings to identify resources and principals involved in a security incident. 

### Sample GuardDuty Finding - JSON Format 

```
[
  {
    "schemaVersion": "2.0",
    "accountId": "123456789012",
    "region": "us-east-1",
    "partition": "aws",
    "id": "00be5f156ab3889054363980571f288e",
    "arn": "arn:aws:guardduty:us-east-1:123456789012:detector/5eba18ab930aa9c74d4a898668c2a849/finding/00be5f156ab3889054363980571f288e",
    "type": "Backdoor:EC2/DenialOfService.UdpOnTcpPorts",
    "resource": {
      "resourceType": "Instance",
      "instanceDetails": {
        "instanceId": "i-99999999",
        "instanceType": "m3.xlarge",
        "outpostArn": "arn:aws:outposts:us-west-2:123456789000:outpost/op-0fbc006e9abbc73c3",
        "launchTime": "2016-08-02T02:05:06.000Z",
        "platform": null,
        "productCodes": [
          {
            "productCodeId": "GeneratedFindingProductCodeId",
            "productCodeType": "GeneratedFindingProductCodeType"
          }
        ],
        "iamInstanceProfile": {
          "arn": "arn:aws:iam::123456789012:example/instance/profile",
          "id": "GeneratedFindingInstanceProfileId"
        },
        "networkInterfaces": [
          {
            "ipv6Addresses": [],
            "networkInterfaceId": "eni-bfcffe88",
            "privateDnsName": "GeneratedFindingPrivateDnsName",
            "privateIpAddress": "10.0.0.1",
            "privateIpAddresses": [
              {
                "privateDnsName": "GeneratedFindingPrivateName",
                "privateIpAddress": "10.0.0.1"
              }
            ],
            "subnetId": "GeneratedFindingSubnetId",
            "vpcId": "GeneratedFindingVPCId",
            "securityGroups": [
              {
                "groupName": "GeneratedFindingSecurityGroupName",
                "groupId": "GeneratedFindingSecurityId"
              }
            ],
            "publicDnsName": "GeneratedFindingPublicDNSName",
            "publicIp": "198.51.100.0"
          }
        ],
        "tags": [
          {
            "key": "GeneratedFindingInstaceTag1",
            "value": "GeneratedFindingInstaceValue1"
          },
          {
            "key": "GeneratedFindingInstaceTag2",
            "value": "GeneratedFindingInstaceTagValue2"
          },
          {
            "key": "GeneratedFindingInstaceTag3",
            "value": "GeneratedFindingInstaceTagValue3"
          },
          {
            "key": "GeneratedFindingInstaceTag4",
            "value": "GeneratedFindingInstaceTagValue4"
          },
          {
            "key": "GeneratedFindingInstaceTag5",
            "value": "GeneratedFindingInstaceTagValue5"
          },
          {
            "key": "GeneratedFindingInstaceTag6",
            "value": "GeneratedFindingInstaceTagValue6"
          },
          {
            "key": "GeneratedFindingInstaceTag7",
            "value": "GeneratedFindingInstaceTagValue7"
          },
          {
            "key": "GeneratedFindingInstaceTag8",
            "value": "GeneratedFindingInstaceTagValue8"
          },
          {
            "key": "GeneratedFindingInstaceTag9",
            "value": "GeneratedFindingInstaceTagValue9"
          }
        ],
        "instanceState": "running",
        "availabilityZone": "GeneratedFindingInstaceAvailabilityZone",
        "imageId": "ami-99999999",
        "imageDescription": "GeneratedFindingInstaceImageDescription"
      }
    },
    "service": {
      "serviceName": "guardduty",
      "detectorId": "5eba18ab930aa9c74d4a898668c2a849",
      "action": {
        "actionType": "NETWORK_CONNECTION",
        "networkConnectionAction": {
          "connectionDirection": "OUTBOUND",
          "localIpDetails": {
            "ipAddressV4": "10.0.0.23"
          },
          "remoteIpDetails": {
            "ipAddressV4": "198.51.100.0",
            "organization": {
              "asn": "-1",
              "asnOrg": "GeneratedFindingASNOrg",
              "isp": "GeneratedFindingISP",
              "org": "GeneratedFindingORG"
            },
            "country": {
              "countryName": "GeneratedFindingCountryName"
            },
            "city": {
              "cityName": "GeneratedFindingCityName"
            },
            "geoLocation": {
              "lat": 0,
              "lon": 0
            }
          },
          "remotePortDetails": {
            "port": 21,
            "portName": "FTP"
          },
          "localPortDetails": {
            "port": 24198,
            "portName": "Unknown"
          },
          "protocol": "UDP",
          "blocked": false
        }
      },
      "resourceRole": "ACTOR",
      "additionalInfo": {
        "sample": true
      },
      "eventFirstSeen": "2021-10-26T19:54:10.000Z",
      "eventLastSeen": "2021-10-26T19:54:10.000Z",
      "archived": false,
      "count": 1
    },
    "severity": 8,
    "createdAt": "2021-10-26T19:54:10.663Z",
    "updatedAt": "2021-10-26T19:54:10.663Z",
    "title": "EC2 instance i-99999999 is behaving in a manner that may indicate it is being used to perform a Denial of Service (DoS) attack using UDP protocol on TCP port.",
    "description": "EC2 instance i-99999999 is behaving in a manner that may indicate it is being used to perform a Denial of Service (DoS) attack using UDP protocol on TCP port."
  }
]
```

### AWS CLI CheatSheet 

The following list-findings example displays a list of all findingIds for the current region sorted by severity from highest to lowest.

```
aws guardduty list-findings \
    --detector-id 12abc34d567e8fa901bc2d34eexample \
    --sort-criteria '{"AttributeName": "severity","OrderBy":"DESC"}'
```

The following list-findings example displays a list of all findingIds that match a specified finding type.

```
aws guardduty list-findings \
    --detector-id 12abc34d567e8fa901bc2d34eexample \
    --finding-criteria  '{"Criterion":{"type": {"Eq":["UnauthorizedAccess:EC2/SSHBruteForce"]}}}'
```

The following get-findings example retrieves the full JSON finding details of the specified finding.

```
aws guardduty get-findings \
    --detector-id 12abc34d567e8fa901bc2d34eexample \
    --finding-id 1ab92989eaf0e742df4a014d5example
```

This example shows how to create a new detector, which enables GuardDuty, in the current region.:

```
aws guardduty create-detector \
    --enable
```

The following create-ip-set example creates and activates a trusted IP set in the current region.

```aws guardduty create-ip-set \
    --detector-id 12abc34d567e8fa901bc2d34eexample \
    --name new-ip-set \
    --format TXT
    --location s3://AWSDOC-EXAMPLE-BUCKET/customtrustlist.csv
    --activate
```

This example shows how to upload a threat intel set to GuardDuty and activate it immediately.

```aws guardduty create-threat-intel-set \
    --detector-id b6b992d6d2f48e64bc59180bfexample \
    --name myThreatSet \
    --format TXT \
    --location s3://EXAMPLEBUCKET/threatlist.csv \
    --activate
```

### Training

[Integration, Prioritization, and Response with AWS Security Hub](https://security-hub-workshop.awssecworkshops.com/)

[Scaling Threat Detection and Response in AWS](https://security-hub-workshop.awssecworkshops.com/)

### Documentation 

[GuardDuty User Guide](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)

---

## AWS Config

AWS Config is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources. Config continuously monitors and records your AWS resource configurations and allows you to automate the evaluation of recorded configurations against desired configurations. With Config, you can review changes in configurations and relationships between AWS resources, dive into detailed resource configuration histories, and determine your overall compliance against the configurations specified in your internal guidelines. This enables you to simplify compliance auditing, security analysis, change management, and operational troubleshooting.

### How AWS Config Differs for AWS GovCloud (US)

The implementation of AWS Config is different for AWS GovCloud (US) in the following ways:

AWS Config recording of third-party resources or custom resource types are not supported in AWS GovCloud (US).

AWS Config multi-account multi-region data aggregation are not supported in AWS GovCloud (US).

AWS Config deployment of rules across an AWS Organization are not supported in AWS GovCloud (US).

Some AWS Config managed rules are not supported in AWS GovCloud (US):

https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/govcloud-config.html
  
### Critical KSAs

**Knowledge** 

An AWS resource is an entity you can work with in AWS, such as an Amazon Elastic Compute Cloud (EC2) instance, an Amazon Elastic Block Store (EBS) volume, a security group, or an Amazon Virtual Private Cloud (VPC). For a complete list of AWS resources supported by AWS Config, see Supported Resource Types.

With AWS Config, you can do the following:

*Evaluate your AWS resource configurations for desired settings.
*Get a snapshot of the current configurations of the supported resources that are associated with your AWS account.
*Retrieve configurations of one or more resources that exist in your account.
*Retrieve historical configurations of one or more resources.
*Receive a notification whenever a resource is created, modified, or deleted.
*View relationships between resources. For example, you might want to find all resources that use a particular security group.
*When you turn on AWS Config, it first discovers the supported AWS resources that exist in your account and generates a configuration item for each resource.

AWS Config also generates configuration items when the configuration of a resource changes, and it maintains historical records of the configuration items of your resources from the time you start the configuration recorder. By default, AWS Config creates configuration items for every supported resource in the region. 

AWS Config supports the following resource types: https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html

Use AWS Config to evaluate the configuration settings of your AWS resources. You do this by creating AWS Config rules, which represent your ideal configuration settings. AWS Config provides customizable, predefined rules called managed rules to help you get started."

A conformance pack is a collection of AWS Config rules and remediation actions that can be deployed and monitored as a single entity in your AWS account.

An aggregator is an AWS Config resource type that collects AWS Config data from multiple accounts and regions. Use an aggregator to view the resource configuration and the compliance data recorded in AWS Config for multiple accounts and regions.

AWS Config allows you to remediate noncompliant resources that are evaluated by AWS Config Rules. AWS Config applies remediation using AWS Systems Manager Automation documents. These documents define the actions to be performed on noncompliant AWS resources evaluated by AWS Config Rules. You can associate SSM documents by using AWS Management Console or by using APIs.

AWS Config provides a set of managed automation documents with remediation actions. You can also create and associate custom automation documents with AWS Config rules.

To apply remediation on noncompliant resources, you can either choose the remediation action you want to associate from a prepopulated list or create your own custom remediation actions using SSM documents. AWS Config provides a recommended list of remediation action in the AWS Management Console. To learn more see [Setting up Auto Remediation](https://docs.aws.amazon.com/config/latest/developerguide/remediation.html#setup-autoremediation) 

**Skills**

* Enable AWS Config in a given Account or Region 

* Enable managed Config rules to measure the compliance of specific resources 

* Upload and deploy a Conformance Pack

* Review the AWS Config Dashboard to determine which resources are in/out of compliance 

* View the inventory of AWS resources 

* View a configuration timeline of a specific resource 

**Abilities**

Inventory and measure AWS resources in a given account against specific rules and security standards. 

Review a timeline of resource compliance and identify when/and which principoals initated any changes to bring a resource out of compliance. 

### AWS CLI CheatSheet 

The following command creates a configuration recorder that tracks changes to all supported resource types, including global resource types:

```
aws configservice put-configuration-recorder --configuration-recorder name=default,roleARN=arn:aws:iam::123456789012:role/config-role --recording-group allSupported=true,includeGlobalResourceTypes=true
```

The following command creates a configuration recorder that tracks changes to only those types of resources that are specified in the JSON file for the --recording-group option:

```
aws configservice put-configuration-recorder --configuration-recorder name=default,roleARN=arn:aws:iam::123456789012:role/config-role --recording-group file://recordingGroup.json
```

The following command provides JSON code to add an AWS managed Config rule:

```
aws configservice put-config-rule --config-rule file://RequiredTagsForEC2Instances.json
```

RequiredTagsForEC2Instances.json

```
{
  "ConfigRuleName": "RequiredTagsForEC2Instances",
  "Description": "Checks whether the CostCenter and Owner tags are applied to EC2 instances.",
  "Scope": {
    "ComplianceResourceTypes": [
      "AWS::EC2::Instance"
    ]
  },
  "Source": {
    "Owner": "AWS",
    "SourceIdentifier": "REQUIRED_TAGS"
  },
  "InputParameters": "{\"tag1Key\":\"CostCenter\",\"tag2Key\":\"Owner\"}"
}
```

The following command starts an on-demand evaluation for two AWS managed rules:

```
aws configservice start-config-rules-evaluation --config-rule-names s3-bucket-versioning-enabled cloudtrail-enabled
```

### Training

[AWS Config Workshop](https://mng.workshop.aws/config.html)

### Documentation 

[AWS Config User Guide](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html)

---

# AWS Runbooks for Incident Response - Shortened version for Offline use. 

[AWS Github Repo](https://github.com/aws-samples/aws-incident-response-playbooks)

---

## [AWS Playbook #1 Credential Leakage/Compromise](https://github.com/aws-samples/aws-incident-response-playbooks/blob/master/playbooks/IRP-CredCompromise.md)

### Introduction

This playbook is provided as a template to customers using AWS products and who are building their incident response capability.  You should customize this template to suit your particular needs, risks, available tools and work processes.

Security and Compliance is a shared responsibility between you and AWS. AWS is responsible for “Security of the Cloud”, while you are responsible for “Security in the Cloud”. For more information on the shared responsibility model, [please review our documentation](https://aws.amazon.com/compliance/shared-responsibility-model/).

You are responsible for making your own independent assessment of the information in this document. This document: (a) is for informational purposes only, (b) references current AWS product offerings and practices, which are subject to change without notice, and (c) does not create any commitments or assurances from AWS and its affiliates, suppliers or licensors. This document is provided “as is” without warranties, representations, or conditions of any kind, whether express or implied. The responsibilities and liabilities of AWS to its customers are controlled by AWS agreements, and this document is not part of, nor does it modify, any agreement between AWS and its customers.

### Part 1: Acquire, Preserve, Document Evidence
1. You become aware of potential indicators of compromise (IoCs). These could come in various forms:
    - An internal ticketing system (the sources of the ticket are varied and could include any of the means below)
    - Where credentials have been compromised, you are less likely to be contacted via the threat actor, as in this situation typically they want to maximize their use of resources and minimize the chance of being discovered, unless the compromise is being used to exfiltrate data or cause some other kind of public incident such as through the modification of your publicly facing resources
2. Confirm a ticket/case has been raised for the incident. If not, manually raise one
3. Determine and begin to document any end-user impact/experience of the issue. From a user’s perspective, for this type of scenario, there may be no direct user impact. Findings should be documented in the ticket/case related to the incident
4. In the case of automatically created tickets/cases, determine what internal alarms/metrics are currently indicating an issue (what caused the ticket to be created?) This may be an Amazon CloudWatch metric indicating an API was called that included a reference to an AWS policy, or an AWS Config rule that indicates some aspect of your AWS Identity and Access Management (IAM) configuration has fallen out of compliance, or an Amazon GuardDuty alert that indicates possible credential compromise. It may also be a billing alert where your billing costs have passed a predetermined threshold, triggering an alarm and notification. Determine exactly which of these has caused the ticket/case to be raised
5. Determine the set of credentials that have been compromised. Note that there will be additional steps in Part 3 to deal with scenarios where threat actors have planted resources to gain an account foothold.
6. Determine the likely time at which the credentials were compromised (any API actions taken post that time should be considered as malicious and any resources created post that time should be considered compromised). As an example, for a GuardDuty finding, note the “eventFirstSeen” field in the “Service” section of the finding.
7. If there is service disruption for your application occurring (see point 2, above), determine if there are any known events that could be causing that disruption that may not be related to the credential leakage (deployed CRs or other application modifications, for example). Check the deployment pipeline to determine if any changes have been made leading up to the event (the time the event was first recorded, or the time the automated system cut the ticket/case)
8. Incident Communications:
    1. Identify stakeholder roles from the application entry in the Configuration Management Database (CMDB) entry for that application, or via the application’s risk register
    2. Open a conference bridge war room for the incident
    3. Notify identified stakeholders including (if required) legal personnel, technical teams and developers and add them to the ticket and the war room, so they are updated as the ticket is updated
9.	External Communications:
    1. Ensure your organizations legal counsel is informed and is included in status updates to internal stakeholders and especially in regards to external communications.
    2. For colleagues in the organization that are responsible for providing public/external communication statements, ensure these internal stakeholders are added to the ticket so they receive regular status updates regarding the incident and can complete their own requirements for communications within and external to the business.
    3. If there are regulations in your jurisdiction requiring reporting of such incidents, ensure the people in your organization responsible for notifying local or federal law enforcement agencies are also notified of the event/added to the ticket. Consult your legal advisor and/or law enforcement for guidance on collecting and preserving the evidence and chain of custody.
    4. There may not be regulations, but either open databases, government agencies or NGOs may track this type of activity. Your reporting may assist others


### Part 2: Contain the Incident

The immediate task will be to disable compromised credentials or revoke permissions associated with those credentials, thereby preventing any further API activity using the compromised credentials.

1. For the compromised credential identified from Part 1, disable those credentials
    1. If they are long term IAM user credentials, [disable them using the IAM console or API](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
    2. If they are short term credentials obtained via the AWS Security Token Service (AWS STS) they will be associated with an IAM role. There are a couple of options available to disable these:
        1. [Revoke all current role sessions](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_revoke-sessions.html) (note that if the threat actor has the ability to obtain new credentials, this won’t solve the problem)
        2. If the threat actor is able to obtain another set of credentials and the activity continues, it will then be necessary to remove all IAM policies attached to the role, modify the attached policies to block all access, or modify the role’s trust policy to prevent the actor from assuming the role. As the credentials will remain valid for the specified time duration once issued, it is important to note that modifying the trust policy will allow any current valid credentials to continue to be used whilst still valid
            **Note that the above actions (step 2, substeps 1 and 2) will stop all users from using credentials obtained by assuming the role, including any legitimate users or applications**
2. The compromised credentials should now be disabled. Verify this by checking the AWS CloudTrail console for the next 30 minutes or so for ongoing credential use, whether by access key, IAM user, or Role.

### Part 3: Eradicate the Incident

Now it is time to further investigate what API actions the compromised credentials have performed since the compromise occurred, and then determine if additional resources need to be deleted, terminated, or investigated further. From Part 1, you have identified the compromised credentials, and the likely time of compromise. It will now be necessary to determine what API actions have been performed and what resources have been created, deleted or modified after that time.

1. Using your preferred monitoring tool, access CloudTrail and search for all API actions performed by the compromised credentials, from the date and time of compromise through to the current time.
2. [Create an Athena table referencing the bucket containing your CloudTrail logs](https://aws.amazon.com/premiumsupport/knowledge-center/athena-tables-search-cloudtrail-logs/) that link also includes example queries that can be run in Athena
3. In the Athena console, run a query that shows all API actions taken by the compromised credentials post-compromise date/time
4. From the resulting list, determine which API calls:
    - Accessed sensitive data (such as S3 GetObject)
    - Created new AWS resources, such as databases, Amazon Elastic Compute Cloud (Amazon EC2) instances, AWS Lambda functions or S3 buckets, etc.
    - Services that create resources  should also be carefully checked [for ….], these include Amazon EC2 Auto Scaling Groups, Amazon EC2 Spot Fleets, Amazon Elastic Kubernetes Service (Amazon EKS)/Amazon Container Service (Amazon ECS) clusters, etc.
    - Created or modified AWS identity resources that could be used to extend a foothold into the account (or other accounts, for example with AWS Security Token Service (AWS STS) API methods such as AssumeRole). Within an account, API methods including (but not limited to) the following should also be investigated:
        - CreateUser, CreateRole, AssumeRole*, Get*Token, Attach*Policy, RunInstances (especially with PassRole), \*Image*, *Provider, Tag*, Untag*, Create*, Delete*, Update*.
    4. Deleted existing AWS resources
    5. Modified existing AWS resources
5. Based on the results of the previous step, determine if any applications are potentially impacted:
    1. Obtain the ARN and/or tag information for each resource impacted (from step 4, above)
    2. Go back to the CMDB and determine which application that resource belongs to
    3. Notify the application owner based on the results of the above steps
6. Based on the above results, if additional credential-obtaining resources have been created (IAM users, roles, EC2 instances with roles, etc.), take the following steps:
    1. Disable and/or delete any credentials for those resources, as per the steps outlined in Part 2, Step 1
    2. Use your preferred logging tool to analyze CloudTrail again, this time specifying the additionally discovered credentials as a query parameter, as outlined in Part 3, steps 1 through to 6 (this step). Continue to iterate through this process until you discover that there were no further resources created capable of obtaining valid credentials for the AWS account.
    3. For each set of newly discovered compromised credentials, go through the following steps from 7 in Part 3 (below) and all relevant steps in Part 4, also below
7. For new resources created during the compromise: Delete all resources created by the compromised credentials that were created post the date/time of the compromise occurring
8. For handling resources that were modified or deleted during the compromise, see Part 4

### Part 4: Recover from the Incident

1. For resources that were modified during the compromise:
    1. If the resource can be destroyed and replaced, do so. For example, EC2 instances in an EC2 Auto Scaling group, with a Launch Configuration referencing a non-compromised AMI, or Launch Templates used to create EC2 instances, terminate the instance, allow EC2 Auto Scaling to add a new one
    2. If the resource cannot be replaced, either:
        1. Restore the resource from a known good backup, or;
        2. Prepare a new resource and configure it into the application’s infrastructure, while isolating the compromised resource and removing it from the application’s infrastructure. Update the CMDB accordingly
        3. Either destroy the compromised resource, or continue to leave it isolated for post-incident forensics
2. For resources that were deleted during the compromise:
    1. Determine what (if any) application the resource belonged to, by checking in the CMDB, or confirming the resource’s tag(s) (Check AWS Config if the tags aren’t listed in the CloudTrail entry and the [resource is supported by AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html))
    2. If the deleted resource can be restored from backup, commence the restore procedure
    3. If the deleted resource cannot be restored from backup, consult the CMDB to obtain the resource’s configuration, and recreate the resource and configure it into the application’s infrastructure

### Part 5: Post-Incident Activity

This activity contains two parts. Firstly, some compromised resources may require forensic analysis, either to fulfil regulatory obligations or improved incident handling, both taking input from the root cause analysis that will result from forensic investigation. The second part is a “sharpen the saw” activity which helps teams to assess their response to the actual incident, determine what worked and what didn’t, update the process based on that information and record these findings.

Firstly, perform any required forensic investigation to determine (for compromised resources) what methods the actors may have used and to determine if additional risks and risk mitigations are required for the resources and/or applications in question.

1. For any compromised resources that have been isolated for further analysis, perform the forensic activity on those resources and incorporate the findings into the post-incident report.
2. Ensure that the CMDB is correctly updated to reflect the current status of all resources and applications impacted

Secondly, review the incident itself and the response to it, to determine if anything needs to be changed for handling any similar incidents in the future.

1. Review the incident handling and the incident handling process with key stakeholders identified in Part 1, Step 8.
2. Document lessons learned, including attack vector(s) mitigation(s), misconfiguration, etc.
3. Store the artifacts from this process with the application information in the CMDB entry for the application and also in the CMDB entry for the credential compromise response process.

---

## [AWS Playbook #2 Unintended access to an Amazon Simple Storage Service (Amazon S3) bucket](https://github.com/aws-samples/aws-incident-response-playbooks/blob/master/playbooks/IRP-DataAccess.md)

### Introduction
This playbook is provided as a template to customers using AWS products and who are building their incident response capability. You should customize this template to suit your particular needs, risks, available tools and work processes.
Security and Compliance is a shared responsibility between you and AWS. AWS is responsible for “Security of the Cloud”, while you are responsible for “Security in the Cloud”. For more information on the shared responsibility model, please review our documentation.
You are responsible for making your own independent assessment of the information in this document. This document: (a) is for informational purposes only, (b) references current AWS product offerings and practices, which are subject to change without notice, and (c) does not create any commitments or assurances from AWS and its affiliates, suppliers or licensors. This document is provided “as is” without warranties, representations, or conditions of any kind, whether express or implied. The responsibilities and liabilities of AWS to its customers are controlled by AWS agreements, and this document is not part of, nor does it modify, any agreement between AWS and its customers.


### This Incident Type
By default, all S3 buckets are private and can be accessed only by users that are explicitly granted access.  Sometimes, customers will make changes to their bucket configuration to meet their requirements, or the requirements of their application. These changes (such as changes to a bucket policy, for example) may result in unintended access being granted to IAM principals or unauthenticated users. This playbook covers steps that can be used to deal with unintended access.

## Incident Response Process
---
### Part 1: Acquire, Preserve, Document Evidence

1. You become aware that there has been a possible unintended data access to an Amazon S3 bucket. This information could come via different means, for example:
    - A message from a contractor or third-party service provider
    - From an alert in one of your own monitoring systems either internal or external to AWS. For example, in AWS, this might include an AWS Config managed rule, AWS CloudTrail via Amazon EventBridge or Amazon CloudWatch Events and Amazon Simple Notification Service (Amazon SNS), via Amazon GuardDuty, AWS Security Hub or a similar service
    - From a threat actor (for example, requesting a ransom or they will disclose data)
2. At this point, you may not know if the issue is due to a mis-configured bucket, or a set of compromised credentials:
    1. Use [AWS Identity and Access Management (IAM) Access Analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html) to determine if any S3 buckets may have overly permissive configuration, giving unintended access to unauthorized principals
    2. Use IAM Access Analyzer to determine if any IAM role trust policy provides unintended access to principals in the same or other AWS accounts
    3. [Review CloudTrail logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/get-and-view-cloudtrail-log-files.html) to determine if recent (prior to the date on which the unintended access occurred, if known) changes had been made to bucket configuration, such as the bucket’s Block All Public Access settings, object ACLs, or bucket policies. You may choose to do this using a tool such as [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/cloudtrail-logs.html#tips-for-querying-cloudtrail-logs)
    4. Review CloudTrail logs to determine if recent (prior to unintended access date, if known) changes have been made to IAM role trust policies
3. If you are already aware of the S3 bucket(s) involved, Firstly move to **Part 2** to contain the incident. Once that is done, return here and then move on to step 5. If you have not established which bucket(s) are involved, continue to step 4.
4. If you do not know which bucket is involved:
    1. If you do not know which bucket is involved, but do know which data is involved:
        1. Use internal tools that links that data to a bucket, such as checking a Configuration Management Database (CMDB)
        2. Use the [S3 ls command](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html#using-s3-commands-listing-buckets) from the [AWS Command Line Interface (AWS CLI)](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) to list out the contents of your S3 bucket(s). For buckets with many objects, you can filter by specifying specific key prefixes in the ls command. This will be especially useful if you already know the data objects involved.
        3. There are several common third-party tools that can also be used to search though files in Amazon S3
        4. Query CloudTrail Data Event logs for one of the files in question, and determine the name of the bucket from the CloudTrail event. [You can do this using Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/cloudtrail-logs.html#query-examples-cloudtrail-logs) This will also provide you the identity of the principal making the API call
5. If the buckets are under your administrative control (either in an AWS account you control, or in an AWS account for which you have authorization and appropriate access, for example via an IAM Role), move ahead to **Part 2** to contain the incident, then return to this point and move on to step 7. If they are not under your administrative control, move on to step 6
6. If you do not have administrative control of the S3 buckets involved, you will need to establish a communication channel with the provider/team/individual that does. This may involve opening a new case on the provider’s service platform, contacting their customer service, directly contacting your technical point of contact (PoC) in that organization, or something similar. Do this as a matter of urgency, as in Part 2, below, you will need to request that they take action to revoke the changes giving access.
7. Confirm a ticket/case has been raised for the incident. If not, manually raise one.
8. Determine if any user cases are already open for the bucket(s) that can be correlated to any notifications or data you already have in relation to the incident. Document any noted end-user impact/experience of the issue in the relevant ticket.
9. In the case of automatically created tickets/cases, determine what internal alarms/metrics are currently indicating an issue (what caused the ticket to be created? - see 1a, above)
10. Determine the application impacted (if any, this may be done quickly via Resource Tags, or by using your CMDB)
11. Determine if there are any known events that could be causing service disruption (for example, an application change roll-out that is now resulting in the mishandling API interactions with the S3 bucket.) This could include but may not be limited to:
    1. Writing incorrect files to the bucket
    2. Incorrectly deleting files from the bucket
    3. Modifying file or bucket permissions, ACLs, or Block Public Access settings
12. Determine for the bucket and/or data in question, what is the [data classification](https://d1.awsstatic.com/whitepapers/compliance/AWS_Data_Classification.pdf) of the data that has been (or allegedly has been) accessed? The classification level may determine the incident response path taken, however the remainder of this playbook will assume that the classification of the data indicates some level of business impact (whether reputational, financial or other). This classification may be recorded in your CMDB or a specific data classification document.
13. Incident Communications:
    1. Identify stakeholder roles from the application entry in the Configuration Management Database (CMDB) entry for that application, or via the application’s risk register
    2. Open a conference bridge war room for the incident
    3. Notify other applicable internal stakeholders including legal personnel, technical teams and developers as required, and add them to the ticket and the war room, so they are updated as the ticket is updated

### Part 2: Contain the Incident

By now, ideally, you should have confirmed whether there has been an unintended access incident and whether the incident relates to a set of leaked credentials, misconfigured bucket policies, modified object ACLs, or a combination of these. Firstly, if compromised credentials were involved, disable those credentials or revoke permissions associated with those credentials, thereby preventing any further API activity using the compromised credentials. Next, move on to bucket policies (if relevant). Finally, move on to Block Public Access settings.

1. For the **compromised credential** details obtained from reviewing CloudTrail logs in Part 1, disable those credentials if they are from AWS accounts under your administrative control
    1. If they are long term IAM user credentials, [disable them using the IAM console or API](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
    2. If they are short term credentials obtained via AWS Security Token Service (AWS STS) they will be associated with an IAM role. There are a couple of options available to disable these:
        1. [Revoke all current roles sessions](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_revoke-sessions.html) (note that if the actor has the ability to obtain new credentials, this won’t solve the problem)
2. The compromised credentials should now be disabled. Verify this by checking the CloudTrail console for the next 30 minutes or so for ongoing credential use, whether by access key, IAM user, or Role.
3. If the credentials used to obtain data are not under your administrative control, and the access was obtained using credentials (i.e., **not** unauthenticated access) the **bucket policy** is too permissive. IAM roles or users from other AWS accounts also need permissions to access a bucket in your account via the bucket policy. Therefore, it is necessary to modify the bucket’s bucket policy to restrict access to those principals:
    1. From the bucket(s) identified in Part 1, review the bucket policies to determine which principals have access to the buckets for data plane and/or management plane operations:
        1. **Do this first**: You will need to modify control plane access (for example, S3 PutBucketPolicy) to prevent unauthorized users from making changes to the bucket’s configuration (bucket policies, bucket ACL, bucket or account level Block Public Access settings)
        2. You will need to modify data plane access Put*/Get* to prevent unauthorized users from accessing or modifying data that they should not have access to, for all applicable read and write operations
        3. For specific objects of concern, identify any AWS accounts or predefined groups listed as having permission in the [object’s ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) (IAM users cannot be listed individually in an ACL). Another approach (if your permission model does not involve ACLs) is to modify the ACLs for all objects in the bucket to remove all other accounts or predefined groups and simply apply FULL_CONTROL to the bucket owner only (not the object owner - potentially from a different AWS account)

### Part 3: Eradicate the Incident

Key components of the eradication process include the following:

1. Mitigate any vulnerabilities that were exploited
2. Rotate compromised passwords/credentials

Most of the steps in this section are taken from the [security best practices for Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/security-best-practices.html). Check back with that document frequently for updates.


1. Eradicate any attack vectors related to systems writing to or reading from the S3 bucket(s). As per **Part 2**, if an actor has gained access to systems that are writing to a bucket, the actor  could now effectively use the instance’s credentials (via the AWS CLI or AWS SDK) on an application instance, for example, to read data from the bucket:
    1. [Configure IMDSv2](https://aws.amazon.com/blogs/security/defense-in-depth-open-firewalls-reverse-proxies-ssrf-vulnerabilities-ec2-instance-metadata-service/) on all application instances/role holding instances

    2. [Rotate SSH credentials](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) for EC2 Linux. Consider the following steps:
        1. Determine which instances are using that key pair: `aws ec2 describe-instances --query "Reservations[*].Instances[].[KeyName, InstanceId]"` (Windows CMD example)
        1. Decide on the following:
            - If the instances should be terminated
                1. Establish if the instances can be terminated without impacting your application (for example, using your CMDB, your EC2 instance tags, etc.)
            - Is it sufficient to rotate an instance's user credentials by:
                - Modifying the contents of the `authorized_keys` file; or,
                - Removing the user; and/or,
                - Remove/modify the user's .ssh directory
                - On Windows, modify the allowed RDP users via `Remote Desktop Users`.

            [At scale, use AWS Systems Manager to manage instance configuration](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-ssm-docs.html).
      2. If the instances have been launched using some kind of scaling mechanism:
        1. Determine if you have any [Launch Configurations]( https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchConfiguration.html) referencing the key pair. If so, create either a new Launch Configuration referencing the new key pair and, associate it to the required resources, such as EC2 Auto Scaling Groups, or instead move on to step 4 and create a launch Template, giving you all the current features for EC2 Auto Scaling
        2. Determine if you have any existing [Launch Templates]( https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html) for EC2 referencing the existing key pair. If so, delete that template, follow the steps below and then create a new template once a new key pair has been created.
        5. Delete the key pair from your EC2 console
        6. Terminate the instances
        7. Create a new key pair in AWS and issue it to any required personnel
        8. Launch instances using the new key pair
        9. Update user passwords for domain or local users using Microsoft Windows Remote Desktop Protocol (RDP) and verify users in the Remote Desktop Users group within Windows and/or Group Policy (for example, if you are adding Active Directory Global Security Groups to the Remote Desktop Users group on local operating systems, a fairly common Group Policy task)
    3. [Revoke credentials for all existing role sessions]( https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_revoke-sessions.html). This will block access to AWS APIs from the EC2 instance until that instance obtains a new set of role credentials. The instance can obtain new role credentials by waiting until the existing set of credentials are rotated automatically, or by detaching and re-attaching the same role to the EC2 instance. If there are concerns that a threat actor has remote access to the instance (for example, by SSH or RDP), per step 2 in this section, terminate the existing EC2 instance and create a new one after revoking existing sessions. Note that if the instance is accessed using [Key Pairs defined in EC2]( https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2-keypairs.html) you should determine if other instances are using the same key pairs and take appropriate action to prevent the threat actor also accessing those instances. Such steps might include:

2. Mitigate vulnerabilities related to your service configurations. For example, with S3, determine which configuration items need to be updated:
    1. Return modified object ACLs to their secure settings, removing unauthenticated access permissions
    2. Implement least privilege in bucket policies
    3. Ensure only appropriate principals have permission to perform data or control plane operations on the bucket
    4. Ensure IAM policies are appropriately scoped and if necessary, [Permission Boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) and/or [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_examples.html) have been deployed appropriately
    5. Encrypt data at rest using [Amazon S3 Server Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) - this protects the data at rest, and means that principals must have permissions in **both **S3 and AWS Key Management Service (AWS KMS) (the AWS KMS Customer Master Key (CMK) key policy) to access the data
    6. [Enable versioning](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) in S3 to enable data recovery, and consider:
    7. [Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock.html) to prevent the deletion of critical data

3. If credential compromise was involved:
    1. Flag for review the process for handling user credentials during Move/Add/Change (MAC) procedures for the post-incident activity
    2. The compromised credentials should have been disabled in Part 2, you may now consider issuing new credentials to impacted users
    3. Close down/remove user accounts or roles if they are not valid accounts/roles (or IAM IdPs, AWS Single Sign-On (AWS SSO) instances, etc.)
    4. Enable [MFA Delete for S3]( https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeletingObjects.html#DeletingObjectsfromanMFA-EnabledBucket), which means users must provide a valid MFA token along with the request to delete any versioned object in the bucket. This requires also enabling S3 Versioning
    5. If you have objects that need to be public, rather than setting the ACL so that the object is world-readable, consider using an S3 pre-signed URL with a suitable expiry time and have your application pass that to the unauthenticated user to allow them to access the object.

### Part 4: Recover from the Incident

The next step is to determine if the incident has been mitigated, or if additional tuning is needed to eradicate it. This includes reviewing pre- and post-mitigation logs to:

* Determine the impact of the mitigations already performed
* Identify any other attack signatures to attempt to eradicate or further mitigate the incident

Once this has been completed, it will likely be necessary to restore any lost or corrupted data.

Restore lost or modified data:

1. If you have [S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html), [restore an earlier version of the object](https://docs.aws.amazon.com/AmazonS3/latest/dev/RestoringPreviousVersions.html) to replace the modified object. If the object has been deleted using a simple delete command (without specifying a version ID for the object, it will now have a [delete marker](https://docs.aws.amazon.com/AmazonS3/latest/dev/DeleteMarker.html) in place of the object. you can [delete the delete marker](https://docs.aws.amazon.com/AmazonS3/latest/dev/RemDelMarker.html) to recover the object
2. If the object was deleted and the delete command specified an object Version ID, that version of the object will be permanently deleted. You will need to establish if previous versions of the object still exist, or of the object can be retrieved from another location (see below steps)
3. If you move data to other S3 storage classes (S3 IA, Amazon Simple Storage Service Glacier, etc.) as part of a back-up or archival process, determine if the files still exist in that storage class and retrieve them (remember that files *can be deleted* from other storage classes). [Lifecycle policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-transition-general-considerations.html) are used to move objects between storage classes
4. If you are using S3 Cross Region Replication (CRR) restore the data from the target bucket to the source bucket as necessary
5. If none of the previous actions allow you to restore the deleted or modified data, you will need to determine if the data is retrievable from another source (such as an on-premise system or an S3 bucket in a different account

From the logs obtained in PART 1, review those obtained at the time of the initial investigation. Now that mitigations are in place, you need to go back and obtain those metrics and logs again, and compare them to the incident metrics and logs obtained earlier.

If data plane activity has returned to pre-attack levels and the logs show no further evidence of malicious use, the attack has been mitigated (at least for now). Continue to monitor the service post-attack; if suspicious data plane activity reoccurs, take the following steps:

1.  Return to Part 1 and follow those steps again
2. Review logs and related data to determine if you have correctly identified the attack vector, or if the actor has changed the vector in response to the mitigation

### Part 5: Post-Incident Activity

This “sharpen the saw” activity helps teams to assess their response to the actual incident, determine what worked and what didn’t, update the process based on that information and record these findings.


1. Review how the incident was handled, as well as the incident handling process generally, with key stakeholders identified in Part 1.
2. Document lessons learned, including attack vector(s) mitigation(s), misconfiguration, etc.
3. Store the artifacts from this process with the application information in the CMDB entry for the application and also in the CMDB entry for the S3 bucket and the data leak incident response process.
4. Update risk documents based on any newly discovered threat/vulnerability combinations that were discovered as a result of lessons learned
5. If new application or infrastructure configuration is required to mitigate any newly identified risks, conduct these change activities and update application and component configuration in the CMDB
6. Ask the following questions and assign people to resolve any issues that come up as a result:
    1. What information would have helped me respond to this incident more swiftly?
    2. What detection would have alerted me to the issue sooner?
    3. What configuration (technical or process) would have prevented this data being exposed in this way?
    4. What automation, tooling or access would have made it easier to investigate the root cause?
7. For any actions that are raised as the result of parts 3, 4 and 5, assign those actions and follow up to ensure they are completed


# Appendix

This section contains links to useful resources not otherwise contained in the sections above. 

## How services differe in GovCloud

https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/using-services.html

## Free AWS Workshops 

https://workshops.aws/

## AWS Skillbuilder - SelfPaced learning on AWS

https://explore.skillbuilder.aws/learn

## East Timor AWS Athena Runbooks and Example Queries

https://easttimor.github.io/aws-incident-response/


