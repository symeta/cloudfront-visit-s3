# why a readme-update is needed
s3 bucket open to public has potential risk. This doc aims at achieving s3 bucket visitable through cloudfront with its 'Block public access' setting unchanged.

# pre-requisite
<img width="1171" height="414" alt="Screenshot 2025-08-24 at 17 05 18" src="https://github.com/user-attachments/assets/26ca49b5-e0ea-4ddf-895b-82e741eaad57" />


# q cli communication details
```sh
> how to make https://d16skucdsp586j.cloudfront.net/2025-08-15/6489925/card_faceId_6489925_005920496.jpeg browser visitable?

> To make that CloudFront URL browser visitable, you need to fix the 403 
Forbidden error. Here are the steps to resolve this:

## 1. Check S3 Bucket Permissions

First, let's identify the S3 bucket behind this CloudFront distribution:


🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: cloudfront
Operation name: list-distributions
Parameters: 
Region: us-east-1
Label: List CloudFront distributions to find the one with domain d16skucdsp586j.cloudfront.net
 ⋮ 
 ● Completed in 2.393s


> Perfect! I found the distribution. The CloudFront domain 
d16skucdsp586j.cloudfront.net corresponds to distribution E2IO1Q24R1YXM2 
with the S3 bucket custcardimg. Now let's check the S3 bucket permissions
:


🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: s3api
Operation name: get-bucket-policy
Parameters: 
- bucket: "custcardimg"
Region: us-east-1
Label: Check bucket policy for custcardimg bucket
 ⋮ 
 ● Completed in 2.833s


> The bucket policy allows public read access, so let's check if the object
exists:


🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: s3api
Operation name: head-object
Parameters: 
- key: "2025-08-15/6489925/card_faceId_6489925_005920496.jpeg"
- bucket: "custcardimg"
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Check if the specific object exists in the custcardimg bucket
 ⋮ 
 ● Completed in 1.621s


> The object exists and has public read permissions. Let's check if there 
are any bucket-level blocks on public access:


🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: s3api
Operation name: get-public-access-block
Parameters: 
- bucket: "custcardimg"
Region: us-east-1
Label: Check public access block settings for custcardimg bucket
 ⋮ 
 ● Completed in 1.613s


> Found the issue! The S3 bucket has Public Access Block enabled, which is 
preventing public access despite having a public bucket policy. Here's 
how to fix it:

## Solution: Disable Public Access Block

The bucket has all public access blocked. You need to disable the public 
access block settings:


🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: s3api
Operation name: delete-public-access-block
Parameters: 
- bucket: "custcardimg"
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> is there alternatives? delete-public-access-block will generate risk

Label: Remove public access block to allow public access to the bucket> You're absolutely right - removing public access block creates security 
risks. Here are safer alternatives:

## Option 1: Use Origin Access Control (OAC) - Recommended

This is the most secure approach. Instead of making the S3 bucket public,
restrict access to CloudFront only:

### Step 1: Create an Origin Access Control


🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: cloudfront
Operation name: create-origin-access-control
Parameters: 
- origin-access-control-config: {"name":"custcardimg-oac","description":"OAC for custcardimg bucket","signing-protocol":"sigv4","signing-behavior":"always","origin-access-control-origin-type":"s3"}
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Create Origin Access Control for secure CloudFront to S3 access
 ⋮ 
 ● Execution failed after 0.559s:

Parameter validation failed:
Missing required parameter in OriginAccessControlConfig: "Name"
Missing required parameter in OriginAccessControlConfig: "SigningProtocol"
Missing required parameter in OriginAccessControlConfig: "SigningBehavior"
Missing required parameter in OriginAccessControlConfig: "OriginAccessControlOriginType"
Unknown parameter in OriginAccessControlConfig: "name", must be one of: Name, Description, SigningProtocol, SigningBehavior, OriginAccessControlOriginType
Unknown parameter in OriginAccessControlConfig: "description", must be one of: Name, Description, SigningProtocol, SigningBehavior, OriginAccessControlOriginType
Unknown parameter in OriginAccessControlConfig: "signing-protocol", must be one of: Name, Description, SigningProtocol, SigningBehavior, OriginAccessControlOriginType
Unknown parameter in OriginAccessControlConfig: "signing-behavior", must be one of: Name, Description, SigningProtocol, SigningBehavior, OriginAccessControlOriginType
Unknown parameter in OriginAccessControlConfig: "origin-access-control-origin-type", must be one of: Name, Description, SigningProtocol, SigningBehavior, OriginAccessControlOriginType






🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: cloudfront
Operation name: create-origin-access-control
Parameters: 
- OriginAccessControlConfig: {"Name":"custcardimg-oac","SigningProtocol":"sigv4","Description":"OAC for custcardimg bucket","SigningBehavior":"always","OriginAccessControlOriginType":"s3"}
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Create Origin Access Control for secure CloudFront to S3 access
 ⋮ 
 ● Completed in 2.79s


> Great! Now let's update the CloudFront distribution to use this OAC:


🛠️  Using tool: use_aws (trusted)
 ⋮ 
 ● Running aws cli command:

Service name: cloudfront
Operation name: get-distribution-config
Parameters: 
- Id: "E2IO1Q24R1YXM2"
Region: us-east-1
Label: Get current distribution configuration
 ⋮ 
 ● Completed in 2.146s


> Now I'll create the updated configuration file and update the 
distribution:


🛠️  Using tool: fs_write
 ⋮ 
 ● Path: /Users/weishiyang/Desktop/distribution-config.json

+      1: {
+      2:     "CallerReference": "custcardimg-2025-08-20-115124",
+      3:     "Aliases": {
+      4:         "Quantity": 0
+      5:     },
+      6:     "DefaultRootObject": "",
+      7:     "Origins": {
+      8:         "Quantity": 1,
+      9:         "Items": [
+     10:             {
+     11:                 "Id": "custcardimg-s3-origin",
+     12:                 "DomainName": "custcardimg.s3.amazonaws.com",
+     13:                 "OriginPath": "",
+     14:                 "CustomHeaders": {
+     15:                     "Quantity": 0
+     16:                 },
+     17:                 "S3OriginConfig": {
+     18:                     "OriginAccessIdentity": ""
+     19:                 },
+     20:                 "ConnectionAttempts": 3,
+     21:                 "ConnectionTimeout": 10,
+     22:                 "OriginShield": {
+     23:                     "Enabled": false
+     24:                 },
+     25:                 "OriginAccessControlId": "E9QFH5MB3UV39"
+     26:             }
+     27:         ]
+     28:     },
+     29:     "OriginGroups": {
+     30:         "Quantity": 0
+     31:     },
+     32:     "DefaultCacheBehavior": {
+     33:         "TargetOriginId": "custcardimg-s3-origin",
+     34:         "TrustedSigners": {
+     35:             "Enabled": false,
+     36:             "Quantity": 0
+     37:         },
+     38:         "TrustedKeyGroups": {
+     39:             "Enabled": false,
+     40:             "Quantity": 0
+     41:         },
+     42:         "ViewerProtocolPolicy": "redirect-to-https",
+     43:         "AllowedMethods": {
+     44:             "Quantity": 2,
+     45:             "Items": [
+     46:                 "HEAD",
+     47:                 "GET"
+     48:             ],
+     49:             "CachedMethods": {
+     50:                 "Quantity": 2,
+     51:                 "Items": [
+     52:                     "HEAD",
+     53:                     "GET"
+     54:                 ]
+     55:             }
+     56:         },
+     57:         "SmoothStreaming": false,
+     58:         "Compress": false,
+     59:         "LambdaFunctionAssociations": {
+     60:             "Quantity": 0
+     61:         },
+     62:         "FunctionAssociations": {
+     63:             "Quantity": 0
+     64:         },
+     65:         "FieldLevelEncryptionId": "",
+     66:         "ForwardedValues": {
+     67:             "QueryString": false,
+     68:             "Cookies": {
+     69:                 "Forward": "none"
+     70:             },
+     71:             "Headers": {
+     72:                 "Quantity": 0
+     73:             },
+     74:             "QueryStringCacheKeys": {
+     75:                 "Quantity": 0
+     76:             }
+     77:         },
+     78:         "MinTTL": 0,
+     79:         "DefaultTTL": 86400,
+     80:         "MaxTTL": 31536000
+     81:     },
+     82:     "CacheBehaviors": {
+     83:         "Quantity": 0
+     84:     },
+     85:     "CustomErrorResponses": {
+     86:         "Quantity": 0
+     87:     },
+     88:     "Comment": "CloudFront distribution for custcardimg S3 bucket",
+     89:     "Logging": {
+     90:         "Enabled": false,
+     91:         "IncludeCookies": false,
+     92:         "Bucket": "",
+     93:         "Prefix": ""
+     94:     },
+     95:     "PriceClass": "PriceClass_All",
+     96:     "Enabled": true,
+     97:     "ViewerCertificate": {
+     98:         "CloudFrontDefaultCertificate": true,
+     99:         "SSLSupportMethod": "vip",
+    100:         "MinimumProtocolVersion": "TLSv1",
+    101:         "CertificateSource": "cloudfront"
+    102:     },
+    103:     "Restrictions": {
+    104:         "GeoRestriction": {
+    105:             "RestrictionType": "none",
+    106:             "Quantity": 0
+    107:         }
+    108:     },
+    109:     "WebACLId": "",
+    110:     "HttpVersion": "http2",
+    111:     "IsIPV6Enabled": true,
+    112:     "ContinuousDeploymentPolicyId": "",
+    113:     "Staging": false
+    114: }

 ⋮ 
 ↳ Purpose: Create updated distribution configuration with OAC

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Creating: /Users/weishiyang/Desktop/distribution-config.json

 ⋮ 
 ● Completed in 0.2s





🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: cloudfront
Operation name: update-distribution
Parameters: 
- Id: "E2IO1Q24R1YXM2"
- DistributionConfig: "file:///Users/weishiyang/Desktop/distribution-config.json"
- IfMatch: "E1XQQNK0DR28KS"
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Update CloudFront distribution to use Origin Access Control
 ⋮ 
 ● Completed in 4.442s


> Now I need to update the S3 bucket policy to allow CloudFront access via 
OAC and remove the public policy:


🛠️  Using tool: fs_write
 ⋮ 
 ● Path: /Users/weishiyang/Desktop/bucket-policy.json

+     1: {
+     2:     "Version": "2012-10-17",
+     3:     "Statement": [
+     4:         {
+     5:             "Sid": "AllowCloudFrontServicePrincipal",
+     6:             "Effect": "Allow",
+     7:             "Principal": {
+     8:                 "Service": "cloudfront.amazonaws.com"
+     9:             },
+    10:             "Action": "s3:GetObject",
+    11:             "Resource": "arn:aws:s3:::custcardimg/*",
+    12:             "Condition": {
+    13:                 "StringEquals": {
+    14:                     "AWS:SourceArn": "arn:aws:cloudfront::020509302229:distribution/E2IO1Q24R1YXM2"
+    15:                 }
+    16:             }
+    17:         }
+    18:     ]
+    19: }

 ⋮ 
 ↳ Purpose: Create S3 bucket policy for CloudFront OAC access

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Creating: /Users/weishiyang/Desktop/bucket-policy.json

 ⋮ 
 ● Completed in 0.1s





🛠️  Using tool: use_aws
 ⋮ 
 ● Running aws cli command:

Service name: s3api
Operation name: put-bucket-policy
Parameters: 
- bucket: "custcardimg"
- policy: "file:///Users/weishiyang/Desktop/bucket-policy.json"
Region: us-east-1

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y

Label: Update S3 bucket policy to allow CloudFront OAC access only
 ⋮ 
 ● Completed in 1.963s


> Perfect! Now let's clean up the temporary files:


🛠️  Using tool: execute_bash
 ⋮ 
 ● I will run the following shell command: 
rm /Users/weishiyang/Desktop/distribution-config.json /Users/weishiyang/Desktop/bucket-policy.json
 ⋮ 
 ↳ Purpose: Clean up temporary configuration files


Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> y


 ⋮ 
 ● Completed in 0.13s


> ## Summary

I've implemented the secure solution using Origin Access Control (OAC):

### What I did:
1. Created Origin Access Control (OAC) - ID: E9QFH5MB3UV39
2. Updated CloudFront distribution to use the OAC
3. Updated S3 bucket policy to only allow CloudFront access (not public 
access)
4. Kept Public Access Block enabled for security

### Benefits of this approach:
• **Secure**: S3 bucket remains private, only CloudFront can access it
• **No public access risks**: Public Access Block stays enabled
• **Better performance**: CloudFront caching and edge locations
• **Cost effective**: Reduced S3 data transfer costs

### Wait time:
The CloudFront distribution is currently deploying (Status: "InProgress")
. It typically takes 5-15 minutes for the changes to propagate globally.

### Test the URL:
After deployment completes, test the URL again:
bash
curl -I "https://d16skucdsp586j.cloudfront.net/2025-08-15/6489925/card_faceId_6489925_005920496.jpeg"


The image should now be accessible through CloudFront while maintaining 
security best practices.
```
# Achievement show case
```sh
weishiyang@loaclhost ~ % curl -I "https://d16skucdsp586j.cloudfront.net/2025-08-15/6489925/card_faceId_6489925_005920496.jpeg"
HTTP/2 200 
content-type: image/jpeg
content-length: 958184
date: Sat, 23 Aug 2025 06:35:34 GMT
last-modified: Wed, 20 Aug 2025 11:47:12 GMT
etag: "010b6f3f915c7e210c429bb5e2e7890a"
x-amz-server-side-encryption: AES256
accept-ranges: bytes
server: AmazonS3
x-cache: Miss from cloudfront
via: 1.1 b0e9d8f8b32a56e2c7dd6fb83988fcce.cloudfront.net (CloudFront)
x-amz-cf-pop: LHR50-P5
x-amz-cf-id: D8eRRq4c1KSpF2zsyXNf2eH1wTgXYdo-D6bPVJf_KZcGXlckgSPhaw==
```
