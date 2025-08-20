# cloudfront-visit-s3

## bucket creation via q (optional)
```sh
> create a s3 bucket in us-east-1 region, the name of the s3 bucket is custcardimg, there are prefixes of this bucket, the first one is 2025-08-15, under it is 6489925, please create the s3 bucket, and all of these prefixes

> I'll create the S3 bucket "custcardimg" in us-east-1 region, create the folder 
structure
```

## manual action 1
- make sure the bucket does not block any public acces
<img width="1045" height="528" alt="Screenshot 2025-08-20 at 20 17 58" src="https://github.com/user-attachments/assets/63a21d61-20f6-4d11-b8a4-033618ec325e" />

## manual action 2 (optional)
download an image and rename as card_faceId_6489925_005920496.jpeg, and upload it to s3 bucket


## cloudfront ditrubition creation via q
```sh
> create a cloudfront distribution to let the card_faceId_6489925_005920496.jpeg could be visited via internet using this cloudfront distribution

> I'll create a CloudFront distribution to serve the image from your S3 bucket 
over the internet.


🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: cloudfront
Operation name: create-distribution
Parameters: 
- distribution-config: {"CallerReference":"custcardimg-2025-08-20-115124","Comment":"CloudFront distribution for custcardimg S3 bucket","DefaultCacheBehavior":{"TargetOriginId":"custcardimg-s3-origin","ViewerProtocolPolicy":"redirect-to-https","MinTTL":0,"ForwardedValues":{"QueryString":false,"Cookies":{"Forward":"none"}},"TrustedSigners":{"Enabled":false,"Quantity":0}},"Origins":{"Quantity":1,"Items":[{"Id":"custcardimg-s3-origin","DomainName":"custcardimg.s3.amazonaws.com","S3OriginConfig":{"OriginAccessIdentity":""}}]},"Enabled":true,"PriceClass":"PriceClass_All"}
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Create CloudFront distribution for custcardimg S3 bucket
 ⋮ 
 ● Completed in 4.913s


> Great! I've successfully created a CloudFront distribution. However, there's one
issue - the S3 bucket needs to be publicly accessible for CloudFront to serve 
the content. Let me update the S3 bucket policy to allow public read access:


🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: s3api
Operation name: put-public-access-block
Parameters: 
- bucket: "custcardimg"
- public-access-block-configuration: {"BlockPublicAcls":false,"IgnorePublicAcls":false,"BlockPublicPolicy":false,"RestrictPublicBuckets":false}
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Remove public access block from S3 bucket
 ⋮ 
 ● Completed in 1.928s





🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: s3api
Operation name: put-bucket-policy
Parameters: 
- policy: "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Sid\":\"PublicReadGetObject\",\"Effect\":\"Allow\",\"Principal\":\"*\",\"Action\":\"s3:GetObject\",\"Resource\":\"arn:aws:s3:::custcardimg/*\"}]}"
- bucket: "custcardimg"
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Add public read policy to S3 bucket
 ⋮ 
 ● Completed in 1.907s


> Perfect! I've successfully created a CloudFront distribution for your S3 bucket.
Here are the details:

## CloudFront Distribution Details:
• **Distribution ID**: E2IO1Q24R1YXM2
• **Domain Name**: d16skucdsp586j.cloudfront.net
• **Status**: InProgress (deployment in progress)
• **Origin**: custcardimg.s3.amazonaws.com

## Access URLs:
Once the distribution is deployed (usually takes 10-15 minutes), you can access 
your image via:

CloudFront URL: 
https://d16skucdsp586j.cloudfront.net/2025-08-15/6489925/card_faceId_6489925_005920496.jpeg


Direct S3 URL (also available now):
https://custcardimg.s3.amazonaws.com/2025-08-15/6489925/card_faceId_6489925_005920496.jpeg


## Configuration:
• **HTTPS**: Redirects HTTP to HTTPS automatically
• **Caching**: Default TTL of 24 hours
• **Global**: Available from all CloudFront edge locations worldwide
• **Public Access**: S3 bucket policy allows public read access

The CloudFront distribution is currently deploying. You can check its status 
with:
bash
aws cloudfront get-distribution --id E2IO1Q24R1YXM2
```

# The achievement show case

<img width="1061" height="586" alt="Screenshot 2025-08-20 at 20 21 28" src="https://github.com/user-attachments/assets/08fd8837-349a-46b6-91ad-9cabf8dc47e0" />

## CloudFront URL: 
<img width="1373" height="1280" alt="Screenshot 2025-08-20 at 20 23 32" src="https://github.com/user-attachments/assets/3eb9d4ab-e8af-49dd-86b0-b1fb5f456e9e" />

<img width="1364" height="1108" alt="Screenshot 2025-08-20 at 20 24 03" src="https://github.com/user-attachments/assets/27f2ab7f-02fc-4cdb-a137-43ca97718b1d" />

## Direct S3 URL
![Screenshot 2025-08-20 at 20 25 42](https://github.com/user-attachments/assets/79a75bbe-8684-4aed-9f14-fa3292385bde)
<img width="1374" height="1086" alt="Screenshot 2025-08-20 at 20 27 37" src="https://github.com/user-attachments/assets/dc21cf1c-cede-4762-8054-1dda4764996c" />


