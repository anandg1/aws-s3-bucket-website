# Website in EC2 with its images loading from a s3 bucket

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)]()

# Description:
Let us create a simple html website with it's images loading from a s3 bucket.
> An Amazon S3 bucket is a public cloud storage resource available in Amazon Web Services' (AWS) Simple Storage Service (S3), an object storage offering. Amazon S3 buckets, which are similar to file folders, store objects, which consist of data and its descriptive metadata.

# Pre-requisites:

- AWS Cli configured
- An EC2 instance with apache service installed and running.
 
> I have already created an EC2 instance with apache server installed using the following commands and a simple html website's files are loaded into its /var/www/html/ directory.

```sh 
yum install httpd -y
systemctl start httpd
systemctl enable httpd -y
```

![alt text](https://github.com/anandg1/aws-s3-bucket-website/blob/main/01.png)

What I'm about to do is to move the images directory in the document-root to s3 bucket without breaking the site, in such a way that the images would be served to the website from the bucket.

# Steps:

## 1) Copying contents from images directory to S3Bucket 

Copying contents of the directory 'images' in /var/www/html to the newly created bucket 'site.anandg.xyz'

![alt text](https://github.com/anandg1/aws-s3-bucket-website/blob/main/02.png)

## 2) Making the bucket access PUBLIC and adding the IAM Bucket Policy

- First of all, edit the Block-public-access option from permissions for our bucket "site.anandg.xyz" so that it would be  accessible to the public.

![alt text](https://github.com/anandg1/aws-s3-bucket-website/blob/main/03.png)

- Now, add the below Bucket policy from permissions to allow everyone one to access objects from bucket site.anandg.xyz.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::site.anandg.xyz/*"
            ]
        }
    ]
}
```
## 3)  Modifying the apache configuration file

 The apache configuration file (/etc/httpd/conf/httpd.conf) is to be added with the following lines so that it serves the image directory from our bucket.
```sh
RewriteEngine On
RewriteRule ^/images/(.*)$ https://s3.ap-south-1.amazonaws.com/site.anandg.xyz/$1 [L]
```
Now, restart apache.
```sh
sudo systemctl restart httpd.service
```
Now, upon checking the URL of the website images, we could see that they are being server from the s3 bucket 'site.anandg.xyz'.

![alt text](https://github.com/anandg1/aws-s3-bucket-website/blob/main/04.png)

![alt text](https://github.com/anandg1/aws-s3-bucket-website/blob/main/05.png)
