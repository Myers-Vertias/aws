---
title: "S3 Presigned URLs: Is Direct-to-S3 File Upload Secure?"
date: 2026-11-21
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# S3 Presigned URLs: Is Direct-to-S3 File Upload Secure?

A familiar situation when building a document management application: a user selects a file, the frontend sends it to the backend, and Lambda forwards it to Amazon S3. This works, but the backend has to receive and transfer the entire file.

Can a browser upload directly to S3 without giving users AWS credentials? One common option is a **presigned URL**. But what permissions does the URL grant, can it be reused, and what should we consider if it is exposed?

## 01 | WHAT PERMISSIONS DOES A PRESIGNED URL GRANT?

A presigned URL is an HTTP request to S3 signed with the credentials of an IAM principal. An application can give the URL to a client so it can perform a specific operation for a limited time, such as `PUT` to upload or `GET` to download.

The client does not need to hold AWS access keys. The request remains limited by the permissions of the principal that created the URL, along with the bucket, object key, HTTP method, and expiration that were signed. In other words, it is a way to grant temporary use of a predefined request, not a standalone user authorization system.

AWS describes presigned URLs as **bearer tokens**: anyone who has the URL can use it while it is valid. Treat it like a credential and protect it accordingly. [1]

## 02 | THE DIRECT-TO-S3 UPLOAD FLOW

For a document management application, an upload flow could work as follows:

1. The user signs in and requests to upload a document.
2. The backend authenticates the user, checks business permissions, and creates a unique object key, such as `incoming/user-123/uuid.pdf`.
3. The backend uses its IAM role to create a presigned URL for the specific bucket, object key, HTTP method, and expiration.
4. The backend returns the URL to the browser.
5. The browser sends the file directly to S3 with a `PUT` request.
6. After S3 receives the object, a follow-up workflow can validate the file and update the document status.

In this flow, Lambda authenticates and grants temporary access, while the file contents travel directly from the browser to S3. The bucket can remain private; the presigned URL only allows the specified signed request to access the designated object during its validity period. [1][2]

## 03 | A URL IS NOT AUTOMATICALLY SINGLE-USE

An easy-to-miss detail: a presigned URL can be used multiple times until it expires. If an object with the same key already exists, another `PUT` can replace that object. [1][2]

For that reason, do not let the client choose a shared key such as `uploads/report.pdf`. The backend should create a unique, hard-to-guess key for each upload, associate it with the user or business record, and sign only the required operation.

If you need to constrain uploads to a specific size range, consider a **presigned POST** with a `content-length-range` policy condition. Do not assume that a presigned `PUT` automatically meets every application-level file-size requirement. [3]

## 04 | URL LIFETIME ALSO DEPENDS ON THE SIGNING CREDENTIALS

A presigned URL has a configured expiration, but it cannot remain valid longer than the credentials used to sign it. If the URL is signed with temporary IAM role credentials, it may stop working when that session ends, even if the configured URL expiration is later.

The AWS CLI and SDKs allow expiration settings of up to seven days under the applicable conditions. That is a maximum, not a sensible default for every use case. Choose a duration that is long enough for the operation and consider whether users may need to retry. The longer a URL lives, the longer the window for someone to use it if it is exposed. [1]

## 05 | CORS LETS THE BROWSER CALL S3, BUT DOES NOT AUTHENTICATE USERS

When the frontend and bucket are on different origins, the browser may send a preflight request. The bucket's CORS configuration must allow the correct origin, methods such as `PUT`, and the request headers the application actually sends.

However, CORS only controls how browsers allow JavaScript to call a resource on another origin. It does not authenticate users, replace IAM or bucket policies, or prevent another HTTP client from using an exposed URL. S3 permissions and policies still apply. [4]

A common error is `SignatureDoesNotMatch`. The upload request must match what was signed, including the HTTP method and relevant headers. For example, if `Content-Type` was included when the URL was created, the client must send the same value with the upload. If this error occurs, check that the URL was not changed, has not expired, uses the correct region, and that the headers match. [2]

## 06 | A SUCCESSFUL UPLOAD DOES NOT MEAN THE FILE IS SAFE

A presigned URL transfers a file to S3, but it does not confirm that the file has the expected business format, is safe, or may later be viewed by that user.

One possible design is to upload into a prefix such as `incoming/`, then run validation and update the status to `PENDING`, `READY`, or `REJECTED`. For downloads, the backend should still re-check the user's permissions and the document's status before issuing an appropriate URL.

If S3 Event Notifications start processing after an upload, account for their **at-least-once** delivery model: notifications can be delayed, duplicated, and are not guaranteed to arrive in the order the events occurred. Consumers should tolerate retries and process duplicates safely, for example by using the object key together with a version or a stored processing identifier. Also avoid having a Lambda write objects into the same prefix that triggers it unless the design prevents recursive invocation. [5]

For data that needs transfer-integrity checks, SigV4 presigned uploads support checksum headers that let S3 compare the checksum with the received content. A checksum can detect a mismatch in the transferred data, but it does not replace content validation or malware scanning. [1][6]

## 07 | PRODUCTION READINESS CHECKLIST

① Does the backend authenticate the user and check permissions before issuing a URL?

② Does the backend create a unique object key scoped to the intended storage area?

③ Is the bucket still private, does the IAM role have only the required permissions, and is the URL expiration appropriate?

④ Have you considered URL exposure, repeated uploads to the same key, CORS, signed headers, and checksums?

⑤ Is the file validated after upload, and can the worker safely handle duplicate or out-of-order events?

## CONCLUSION

Presigned URLs let a browser upload directly to S3 without holding AWS credentials or sending the entire file through Lambda. In exchange, the URL must be treated as temporary access: whoever possesses it can make the signed request until the URL or its underlying credentials expire.

A secure flow therefore involves more than “creating a URL.” The backend must decide who can upload, which object key may be used, and how long the URL remains valid. The system must also validate files after receipt and process events in a retry-safe way.

**A presigned URL grants a limited request. The application must still enforce business permissions.**

#AWS #AmazonWebServices #CloudComputing #CloudArchitecture #AmazonS3 #AWSLambda #Serverless #CloudSecurity #PresignedURL #AWSSecurity #AWSStudyGroupVN

### References

1. [Download and upload objects with presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-url.html)
2. [Uploading objects with presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/PresignedUrlUploadObject.html)
3. [POST Policy](https://docs.aws.amazon.com/AmazonS3/latest/developerguide/sigv4-HTTPPOSTConstructPolicy.html)
4. [Configuring cross-origin resource sharing (CORS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cors.html)
5. [Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EventNotifications.html)
6. [Checking object integrity in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/checking-object-integrity-upload.html)

### Publication information

https://www.facebook.com/groups/awsstudygroupfcj/permalink/2345064529591839/?rdid=KmHzDedRJD6NcuJ7#
