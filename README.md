# Implementing-AWS-WAF-with-ALB-to-block-SQL-Injection-Geo-Location-and-Query-string

## Purpose of the Hands on


Setting up an Application Load Balancer in AWS Elastic Load Balancer by divide the incoming traffic to both EC2 Instances (servers) in a Round Robin manner. To prevent access from specific geographical locations, safeguard against SQL injections, and restrict certain Query String parameters. 
## Architecture

![Architecture](https://github.com/user-attachments/assets/fde74253-d92c-4514-a1ab-a4e9d5f4b35a)



### Step-by-Step Implementation

1. Launch two EC2 Instances <b>(MyEC2Server1)</b>-<b>(MyEC2Server2)</b>
   - instance-type t2.micro
   - Keypair.pem
   - auto-assign public IPV4 address
   - Security groupe web: <b>MyWebserverSG</b>
   - inbound rule: SSH, HTTP,HTTPs
   - user-data file://apache_install.sh(reference: 1-Launch two EC2 Instances.png)
2. Create a <b>Target groupe</b> for the <b>Load Balancer</b> to distribute traffic among these <b>instances</b>.(reference: 2-Create a Target groupe.png)
3. Create <b>Application Load Balancer</b>(reference: 3-Create Application Load Balancer.png)
4. Test the Load Balancer by copy the <b>DNS</b> name and past it to the browser, the ALB will equally divide the incoming traffic to both servers in a Round Robin manner.
   - Response coming from server 1(reference: 4-Response coming from server 1.png)
   - Response coming from server 2(reference: 4-Response coming from server 2.png)
   
5. Test <b>SQL Injection</b>
   - We will add the following URL parameter: /product?item=securitynumber+OR+1=1--
   - Syntax will be: http://<ELB DNS>/product?item=securitynumber+OR+1=1--
   - <b>Result</b>: <b>SQL Injection went inside the server and it doesn't know how to solve this URL</b>.(reference: 5-Test SQL Injection.png)
6. Test <b>Query String Parameter</b>
   - We will add the following URL parameter: /?admin=123456
   - Syntax will be: http://<ELB DNS>/?admin=123456
   - <b>Result</b>: <b>The server passes the Query String inside, there is no error the admin parameter becames an unused value</b>.(reference: 6-Test Query String Parameter.png)
     
7. In <b>WAF & Shield</b> we create a <b>Web ACL</b>
   - Add AWS resources: Application load balancer
   - Add rules GeolocationRestriction, QueryStringRestriction, SQL database(reference: 7-create a Web ACL.png)
  
8. ReTest <b>SQL Injection</b>
   - We will add the following URL parameter: /product?item=securitynumber+OR+1=1--
   - Syntax will be: http://<ELB DNS>/product?item=securitynumber+OR+1=1--
   - <b>Result</b>: <b>SQL Injection is blockedby WAF before it goes inside the server.</b>(reference: 8-ReTest SQL Injection.png)
   
9. ReTest <b>Query String Parameter</b>
   - We will add the following URL parameter: /?admin=123456
   - Syntax will be: http://<ELB DNS>/?admin=123456
   - <b>Result</b>: <b>The Query String which contains admin is blocked by WAF before it could go inside the server</b>(reference: 9-ReTest Query String Parameter.png)

 
    

> *Lab originally from: [Whizlabs - Implementing AWS WAF with ALB to block SQL Injection, Geo Location and Query string](https://www.whizlabs.com/labs/implementing-aws-waf-with-alb-to-block-sql-injection-geo-location-and-query-string/))*



