<h1 style="text-align:center;font-family:'Times New Roman';color:darkblue;font-weight:700;font-size:40px"> Application Load Balancer Logs DataAnalysis  </h1>

<hr style="border: 0.001px solid black">

<body>
    <h1>AWS ALB Log Analysis</h1>
    <p style ="font-weight:400;font-size:18.5px">This Project provides a comprehensive solution for analyzing AWS Application Load Balancer (ALB) logs using Athena and Quicksight. The ALB logs are stored in an S3 bucket and are generated by an EC2 instance hosting an Apache server. The solution leverages AWS services to efficiently process and visualize the log data.</p>
    <h2>Key Features:</h2>
    <ul>
        <li style ="font-weight:400;font-size:18.5px"><strong>Seamless Integration:</strong> The project demonstrates how to configure an S3 bucket to receive ALB logs and set up a data pipeline to process them.</li>
        <li style ="font-weight:400;font-size:18.5px"><strong>Athena Querying:</strong> Using AWS Athena, the repository showcases how to define a schema for ALB logs and query the data using SQL syntax, enabling efficient analysis and retrieval of specific information.</li>
        <li style ="font-weight:400;font-size:18.5px"><strong>Quicksight Visualization:</strong> With AWS Quicksight, the repository illustrates how to create interactive and visually appealing dashboards to gain insights from the ALB log data.</li>
        <li style ="font-weight:400;font-size:18.5px"><strong>Auto Scaling Integration:</strong> The solution supports auto scaling of the EC2 instances hosting the web page, allowing for dynamic scaling of resources based on demand. The maximum number of instances is set to two.</li>
        <li style ="font-weight:400;font-size:18.5px"><strong>Streamlined Deployment:</strong> The project includes step-by-step instructions and configuration files to quickly deploy the ALB log analysis solution in your own AWS environment.</li>
    </ul>
    <p style ="font-weight:400;font-size:18.5px">By leveraging the power of Athena, Quicksight, and S3 integration, this repository offers an efficient and scalable solution for analyzing ALB logs. Gain valuable insights into your web traffic patterns, detect anomalies, and optimize your application performance.</p>
</body>
</html>

# The Process Architecture 👇

![1](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/0168c5a2-33d1-4cd4-9aed-901e3add76dc)


---

# 1️⃣ Create two instances having Apache webservers running on it
###  For more info on creating instances with apache webservers visit:
### https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-and-Data-analysis-Project-/blob/main/Supportive%20Documents/Apache%20website.pdf

---

# 2️⃣ Create two S3 buckets for the project


<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• we will be requiring one bucket to store the logs of the data and one for storing query responses of the Athena</b></p>
  </body>


![2](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/58d57040-9d14-4f0b-9840-66659bee4f96)

<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• The structure of the folder in your S3 bucket where you are storing logs should be 👇        
</b></p>
  </body>
  

![3](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/59b84923-84b4-43f1-97a1-1a5382d06db5)



<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• you will be required to give access to the load balancer to write the data to S3 bucket For that you have to attach the bucket policy to the buck where you are going to store the logs from the ALB.</b></p>
  </body>

---

## ✒️ Bucket policy for allowing Load balancer to log data to the S3 bucket 👇


```python
{
    "Version": "2012-10-17",
    "Id": "AWSConsole-AccessLogs-Policy-1668943070662",
    "Statement": [
        {
            "Sid": "AWSConsoleStmt-1668943070662",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::718504428378:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::webserveralblog/prefix/AWSLogs/436117849909/*"
        },
        {
            "Sid": "AWSLogDeliveryWrite",
            "Effect": "Allow",
            "Principal": {
                "Service": "delivery.logs.amazonaws.com"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::webserveralblog/prefix/AWSLogs/436117849909/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control"
                }
            }
        },
        {
            "Sid": "AWSLogDeliveryAclCheck",
            "Effect": "Allow",
            "Principal": {
                "Service": "delivery.logs.amazonaws.com"
            },
            "Action": "s3:GetBucketAcl",
            "Resource": "arn:aws:s3:::webserveralblog"
        }
    ]
}
```

---

# 3️⃣ Create Target group of your webserver instances

<body>
    <p style ="font-weight:400;font-size:18.5px"><b>1. Create Target Groups by instances</b></p>
  </body>

![4](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/8cccfdb5-c8fc-4a57-81e7-8c125954eff7)


<body>
    <p style ="font-weight:400;font-size:18.5px"><b>2. Add instances to your target group</b></p>
  </body>

![5](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/c972351a-792c-46dc-a5a3-d7faff91534a)



<body>
    <p style ="font-weight:400;font-size:18.5px"><b>3. Choose Option include as pending below and then choose create target group</b></p>
  </body>

![6](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/54ac3706-1e3c-4e51-a7b3-4561e2eaf753)


---

# 4️⃣ Create & Configure LoadBalancer


<body>
    <p style ="font-weight:400;font-size:18.5px"><b>1. Create application load balancer</b></p>
  </body>


![7](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/7fb3525c-5795-49a1-bb6e-014890c18aa2)


<body>
    <p style ="font-weight:400;font-size:18.5px"><b>2. Select network mapping zones</b></p>
  </body>

![8](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/95806fb8-0dff-4f22-8761-fe5cad8b8e97)



<body>
    <p style ="font-weight:400;font-size:18.5px"><b>3. In the listener routing add the target group you have created then choose create load balancer option</b></p>
  </body>

![9](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/ca85a0f8-5f13-4773-9d67-50827a2b7120)



<body>
    <p style ="font-weight:400;font-size:18.5px"><b>4. Edit load balancer attributes</b></p>
  </body>

![10](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/ebf06580-b6e8-466c-a66b-3e03a09d1ad3)



<body>
    <p style ="font-weight:400;font-size:18.5px"><b>5. Enable monitoring access logs and browse to the location as shown below 👇    
</b></p>
  </body>

![11](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/85717ad4-6a6d-4220-876d-5f6ab5c92fd3)


<body>
    <p style ="font-weight:400;font-size:18.5px"><b>6. Once you configure the Load Balancer you will start to get log files in the S3 bucket as shown below 👇    
</b></p>
  </body>

![12](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/86cc3f51-13e9-4b1a-b39a-16c0dacac7c1)


---

# 5️⃣ Configure the Athena query results Storage location

![2 query result storage](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/874e16b4-b99f-4ccc-bc02-8938ca0f597f)


---

# 6️⃣ Create Database

![3 Create database](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/e32dfb7f-b149-4414-bac8-10df8ec353f0)


---

# 7️⃣ Create Table of the Logs

## • Query to make Table 👇


```python
CREATE EXTERNAL TABLE IF NOT EXISTS alb_logs (
            type string,
            time string,
            elb string,
            client_ip string,
            client_port int,
            target_ip string,
            target_port int,
            request_processing_time double,
            target_processing_time double,
            response_processing_time double,
            elb_status_code int,
            target_status_code string,
            received_bytes bigint,
            sent_bytes bigint,
            request_verb string,
            request_url string,
            request_proto string,
            user_agent string,
            ssl_cipher string,
            ssl_protocol string,
            target_group_arn string,
            trace_id string,
            domain_name string,
            chosen_cert_arn string,
            matched_rule_priority string,
            request_creation_time string,
            actions_executed string,
            redirect_url string,
            lambda_error_reason string,
            target_port_list string,
            target_status_code_list string,
            classification string,
            classification_reason string
            )
            ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
            WITH SERDEPROPERTIES (
            'serialization.format' = '1',
            'input.regex' = 
        '([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*):([0-9]*) ([^ ]*)[:-]([0-9]*) ([-.0-9]*) ([-.0-9]*) ([-.0-9]*) (|[-0-9]*) (-|[-0-9]*) ([-0-9]*) ([-0-9]*) \"([^ ]*) (.*) (- |[^ ]*)\" \"([^\"]*)\" ([A-Z0-9-_]+) ([A-Za-z0-9.-]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^\"]*)\" ([-.0-9]*) ([^ ]*) \"([^\"]*)\" \"([^\"]*)\" \"([^ ]*)\" \"([^\s]+?)\" \"([^\s]+)\" \"([^ ]*)\" \"([^ ]*)\"')
            LOCATION 's3://your-alb-logs-directory/AWSLogs/<ACCOUNT-ID>/elasticloadbalancing/<REGION>/'

```

![4 Create_table](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/03f22460-0df8-4605-b61f-0bd35d8a17a3)


<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• Do the Data Exploratrion to gather insights.  
</b></p>
  </body>
  

---

# 8️⃣ Create Dataset in your Quicksight for the Log data

## Step-1
<body>
    <p style ="font-weight:400;font-size:18.5px"><b>📝 Here your datasource is Athena    
</b></p>
  </body>

![create dataset1](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/4cbd4512-0585-48c9-9a87-61a2b67dab8d)


## Step-2
<body>
    <p style ="font-weight:400;font-size:18.5px"><b>📝 Name your datasource </b></p>
  </body>

![create dataset2](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/6a396af4-03b4-4abb-80ab-6e32506aa7f1)


## Step-3
<body>
    <p style ="font-weight:400;font-size:18.5px"><b>📝 Select the Database & table where you have your Log data    
</b></p>
  </body>

![create dataset3](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/5ed87909-0825-407c-b752-4f31894c8b47)


## Step-4 
<body>
    <p style ="font-weight:400;font-size:18.5px"><b>📝 Choose visualize option and you are now ready to visualize your data    
</b></p>
  </body>

![create dataset4](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/f37ba3f7-3214-4fbd-9c27-0af2567493f1)


---

# 9️⃣ Analysis & Datavisualization dashboard in Quicksight

<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• Below visualization shows the count of records by Status code 👇 
</b></p>
  </body>

![visualisation1](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/6a4add60-1027-48a6-b291-a26f421d8143)


<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• Below visualization shows the amount of traffic received by each webservers,here each Target IP represents webservers, The traffic represents the number of client ips visited the webservers 👇 
</b></p>
  </body>

![visualization2](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/e131ceaa-282a-4306-accd-ef1a6cfb2b57)

<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• Below visualization shows what type of request is made by the client you can see the client used GET request the most👇 
</b></p>
  </body>

![visualisation3](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/a171988a-18a4-4863-8bf5-5fbea3b81698)

<body>
    <p style ="font-weight:400;font-size:18.5px"><b>• Below visualization shows how Load balancer equally distributed the bytes sent and bytes received by each servers 👇 
</b></p>
  </body>

![visualization 4](https://github.com/DURGESH99P/Application-load-balancer-Datapipeline-----Data-analysis-Project-/assets/94096617/e88f3ef2-6e4c-42ca-8805-0e594a20fd4c)

<body>
    <p style ="font-weight:400;font-size:18.5px"><b>✒️Tip: Explore this projects and be more creative with the Visualizations.
</b></p>
  </body>

---
