# Cloud Computing

Cloud computing is the distribution of computing services via the internet (the cloud), including servers, storage, databases, networking, software, analytics, and intelligence, to provide faster innovation, more flexible resources, and economies of scale.

Includes following topics:
- AWS (S3)

## AWS

AWS is a cloud computing platform used to manage and maintain hardware and infrastructure of resources.

-	Reduces the expense and complexity of purchasing and running resources on-site for business and individuals, consists of a pay-per-user setup
-	Is a comprehensive computing platform offered by Amazon
-	Infrastructure as a service (IaaS), platform as a service (PaaS), and packaged software as a service (SaaS) are used to build it

### Amazon S3

S3 stands for simple storage service. It is an object storage service from Amazon that provides industry-leading scalability, data availability, security, and performance. 
Any file uploaded will be treated as an abstraction, or object.

S3 allows for:
-	Industry-leading scalability
-	Data availability (replicate across at 3 availability zones)
-	Data security 
-	Performance

S3 has simple management capabilities that allow you to organize your data and fine-tune access controls to match your specific needs.

A ^^bucket^^ is a logical container for an unlimited amount of objects. It can be look at as something similar to a folder.

Amazon S3 is designed for durability and saves data for millions of apps for businesses all over the world. (11 9s durability)

^^Workings of Amazon S3^^

The type of S3 storage class must be specified when files are uploaded to the bucket.

- standard storage class
    - default and most expensive, highest level of accessibility and durability
- standard infrequent access (IA) class
    - used where data is long lived, but not frequently accessed
- glacier storage class
    - best for data that is archived and high performance is not required
- zone-IA class
    - for data infrequently accessed and stored only a particular region
- standard reduced redundancy storage class
    - used for data that is noncritical and reproduced quickly

^^Features of S3^^

Lifecycle Management: </br>
S3 defines a certain set of rules to be followed by a particular group of objects. </br>
    - 2 types of action: transition and expiration
