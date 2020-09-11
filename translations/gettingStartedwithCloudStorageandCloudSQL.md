# Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL.

## Objectives

In this lab, you will learn how to perform the following tasks:

- Deploy a web server VM instance.

- Create a Cloud Storage bucket using the gsutil command line.

- Create the Cloud SQL instance.

## Steps

1.  Deploy a web server VM instance.

    - create compute engine virtual machine instance

      `gcloud compute instances create bloghost --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default" --tags http`

    - create a firewall rule

      `gcloud compute firewall-rules create bloghost-allow-egress-tcp-port80-to-vm1 --network NETWORK-NAME --action allow --direction egress --rules tcp:80 --destination-ranges 192.168.1.2/32 --priority 60`

    - create a startup script in bloghost instance

      `gcloud compute instances add-metadata bloghost --metadata-from-file startup-script=/local/path/to/script/startup`

    - Enter the following metadata as the values for the Startup script:
      `apt-get update`

      `apt-get install apache2 php php-mysql -y`

      `service apache2 restart`

2.  Create a Cloud Storage bucket using the gsutil command line.

    - Enter your chosen location into an environment variable called LOCATION

      ` export LOCATION= "US"`

    - In Cloud Shell, the DEVSHELL_PROJECT_ID environment variable contains your project ID. Enter this command to make a bucket named after your project ID:

      `gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID`

    - Retrieve a banner image from a publicly accessible Cloud Storage location:

      `gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png`

    - Copy the banner image to your newly created Cloud Storage bucket:

      `gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png`

    - Modify the Access Control List of the object you just created so that it is readable by everyone:

    - `gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png`

3.  Create the Cloud SQL instance.

    `gcloud sql instances create blog-db --password="admin" --sql-version=MYSQL_5_7" --zone=us-central1-a`

    - Create the database instance

      `gcloud sql databases create host-db --instance="blog-db"`

    - Create user

      `gcloud sql users create blogdbuser --instance=blog-db -i blog-db --host='%' --password='password'`

    - Execute the following command to get the external IP address of your sql instance

      `gcloud compute instances list`

    - Add network and authorize the ip address

      `gcloud compute networks create web front end`

      `gcloud sql instances patch blog-db --authorized-networks="35.192.208.2/32"`
