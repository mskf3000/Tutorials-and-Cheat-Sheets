## Amazon - Amazon S3 Bucket and User Policy Examples

By Jack Szwergold, October 6, 2015

### All Amazon S3 actions for reference.

	s3:AbortMultipartUpload
	s3:CopyObject
	s3:CreateBucket
	s3:DeleteBucket
	s3:DeleteObject
	s3:DeleteObjectVersion
	s3:GetBucketAccessControlPolicy
	s3:GetBucketAcl
	s3:GetBucketLocation
	s3:GetBucketLogging
	s3:GetBucketNotification
	s3:GetBucketPolicy
	s3:GetBucketRequestPayment
	s3:GetObject
	s3:GetObjectAccessControlPolicy
	s3:GetObjectAcl
	s3:GetObjectExtended
	s3:GetObjectVersion
	s3:GetObjectVersionAcl
	s3:ListAllMyBuckets
	s3:ListBucket
	s3:ListBucketMultipartUploads
	s3:ListBucketVersions
	s3:ListMultipartUploadParts
	s3:PutBucketAcl
	s3:PutBucketNotification
	s3:PutBucketLogging
	s3:PutBucketPolicy
	s3:PutBucketRequestPayment
	s3:PutBucketVersioning
	s3:PutLifecycleConfiguration
	s3:PutObject
	s3:PutObjectAcl
	s3:PutObjectInline
	s3:PutObjectVersionAcl
	s3:SetBucketAccessControlPolicy
	s3:SetObjectAccessControlPolicy

### An example of a generic user policy.

	{
	  "Statement": [
	    {
	      "Action": [
	        "s3:ListAllMyBuckets"
	      ],
	      "Effect": "Allow",
	      "Resource": "arn:aws:s3:::*"
	    },
	    {
	      "Action": [
	        "s3:AbortMultipartUpload",
	        "s3:CopyObject",
	        "s3:DeleteObject",
	        "s3:DeleteObjectVersion",
	        "s3:GetObject",
	        "s3:GetObjectAccessControlPolicy",
	        "s3:GetObjectAcl",
	        "s3:GetObjectExtended",
	        "s3:GetObjectVersion",
	        "s3:GetObjectVersionAcl",
	        "s3:ListAllMyBuckets",
	        "s3:ListBucket",
	        "s3:ListBucketMultipartUploads",
	        "s3:ListBucketVersions",
	        "s3:ListMultipartUploadParts",
	        "s3:PutObject",
	        "s3:PutObjectAcl",
	        "s3:PutObjectInline",
	        "s3:PutObjectVersionAcl",
	        "s3:SetObjectAccessControlPolicy"
	      ],
	      "Effect": "Allow",
	      "Resource": [
	        "arn:aws:s3:::s3_bucket_name",
	        "arn:aws:s3:::s3_bucket_name/*"
	      ]
	    }
	  ]
	}


#### An example of a generic Amazon S3 bucket policy.

In this case it’s for a bucket named `s3_bucket_name`:

	{
		"Version": "2008-10-17",
		"Statement": [
			{
				"Sid": "AllowPublicRead",
				"Effect": "Allow",
				"Principal": {
					"AWS": "*"
				},
				"Action": "s3:GetObject",
				"Resource": "arn:aws:s3:::s3_bucket_name/*"
			}
		]
	}

#### An example of a generic Amazon S3 user policy.

This is for a bucket named `s3_bucket_name`. It works, but gives note that it gives the user permission to delete bucket:

	{
	  "Statement": [
	    {
	      "Action": [
	        "s3:ListAllMyBuckets"
	      ],
	      "Effect": "Allow",
	      "Resource": "arn:aws:s3:::*"
	    },
	    {
	      "Action": "s3:*",
	      "Effect": "Allow",
	      "Resource": [
	        "arn:aws:s3:::s3_bucket_name",
	        "arn:aws:s3:::s3_bucket_name/*"
	      ]
	    }
	  ]
	}

***

*Amazon - Amazon S3 Bucket and User Policy Examples (c) by Jack Szwergold. This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC-BY-NC-SA-4.0).*