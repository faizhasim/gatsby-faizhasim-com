= Infrastructure as Code workflow with HashiCorp Atlas and Terraform

:hp-tags: docker, terraform, atlas, devops


Recently, I have been doing some spikes on evaluating https://atlas.hashicorp.com/[HashiCorp Atlas] with https://terraform.io/[Terraform]. I'd like to share with you on recent spike that I did on evaluating the platforms/tools/ecosystem for 

== Infrastructure as Code with Terraform

Terraform is a tool to provide common configuration for your infrastructure. While things like Puppet/Chef/Ansible manages your application configuration state, you can think of it like Puppet for infrastructure. 

```
resource "digitalocean_droplet" "web" {
    name = "tf-web"
    size = "512mb"
    image = "centos-5-8-x32"
    region = "sfo1"
}
  
resource "dnsimple_record" "hello" {
    domain = "example.com"
    name = "test"
    value = "${digitalocean_droplet.web.ipv4_address}"
    type = "A"
}
```

Previously, I was writing some simple infrastructure using AWS CloudFormation. It doesn't take me long to realize how painful to work in one big fat JSON to define your infrastructure. I actually went to our local AWS Summit

YC Lian who attended local AWS user group, introduced me to Terraform which is exactly what was I looking for. 

For most recent project to serve static assets for LD/LDE integration, we configured an S3 bucket using Terraform and track our infra within our Git repo. We had to configure Cloudfront distribution channel by hand as Terraform is not yet able to support that.

Here's how it looks like:

```
provider "aws" {
    access_key = "${var.access_key}"
    secret_key = "${var.secret_key}"
    region = "${var.region}"
}
 
resource "aws_s3_bucket" "bucket" {
  bucket = "${var.s3_bucket_name}"
  acl = "public-read"
 
  website {
    index_document = "index.html"
    error_document = "error.html"
  }
 
  policy = <<EOF
{
  "Version":"2012-10-17",
  "Statement":[{
    "Sid": "PublicListBucketObjects",
    "Effect": "Allow",
    "Principal": "*",
    "Action": ["s3:ListBucket"],
    "Resource": "arn:aws:s3:::${var.s3_bucket_name}"
  },{
    "Sid":"PublicReadForGetBucketObjects",
    "Effect":"Allow",
    "Principal": "*",
    "Action":["s3:GetObject"],
    "Resource":["arn:aws:s3:::${var.s3_bucket_name}/*"]
  }]
}
EOF
}
```

> Reference: http://d25n1fi47hc88f.cloudfront.net/lde-cdn.html

Now, our infrastructure can be expressed as code.

So, here come a question, how can we integrate this whole infrastructure development process, just like you push code into production?
 
== What is Atlas

Atlas is a application delivery platform that empowers other HashiCorp solutions such as Packer, Terraform and Consul service discovery. Think of it like one of the possible continuous deployment platform with Github-like review process.
 
image::https://hashicorp.com/images/blog/atlas/how-it-works-de25bb85.png[]

Referring to previous CDN hosting example, we can configure Atlas as our infra deployment pipeline. Developer can make a proposal to the infrastructure changes by pushing the Terraform changes to Atlas. This is effectively the same as running terraform plan locally.

This will alert reviewers via HipChat and emails for any infrastructure change request. He/she can then review the changes from the Atlas UI as the following.

image::https://cloud.githubusercontent.com/assets/898384/11377940/955ebf86-9323-11e5-9f30-3bde8fc25098.png[]

Depending on the proposed changes, the reviewer can either approve or reject the changes. Next, Atlas will apply the changes to the infra as requested. It will also manage the infrastructure state for us too. Pretty neat!

== Ideas

Some ideas that I have in mind:

. For now, we may not want to implement self-healing or self-scaling Learndot. So, we can review the demand as needed via Atlas integration with Terraform.
. Similar when we have peer review process by making pull request to Git repo, this should become as part of our workflow to push changes to infrastructure. You do not want developers just pushing stuff to production. We need peer reviews.

== Notes

- Terraform is not limited to AWS. But also others, including DigitalOcean, vSphere or OpenStack.
- So, it is possible to implement hybrid approach for your architecture.