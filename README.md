# AWS S3 Static Website Hosting with Root Domain Configuration

Hosting my portfolio website on **Amazon S3** as a static website and preparing it to be served from the root domain **chakuli.shop** using **Amazon Route 53**.

**Author:** Sakshi Somvanshi

## Project overview

The goal of this project is to host a static website (HTML, CSS and images) on AWS without any server, and connect it to a custom root domain.

## Services used

| Service | Purpose |
|---|---|
| Amazon S3 | Stores the website files and serves them as a static website |
| Amazon Route 53 | DNS management for the domain (public hosted zone) |
| S3 bucket policy and ACLs | Makes the website files publicly readable |

## Architecture

```
User  ->  chakuli.shop  ->  Route 53 (Alias A record)  ->  S3 website endpoint  ->  index.html
```

![Architecture diagram](screenshots/architecture-diagram.png)

## Steps

1. **Create the S3 bucket**: bucket name `chakuli.shop` (same as the domain, lowercase only) in `ap-south-1` (Mumbai).
2. **Enable ACLs**: Permissions > Object Ownership > ACLs enabled.
3. **Enable static website hosting**: Properties > Static website hosting > Enable, index document `index.html`.
4. **Upload the website files**: `index.html`, `style.css` and images to the bucket root.
5. **Make the files public**: turn off Block Public Access, use Actions > Make public using ACL, and add the bucket policy below.
6. **Test the S3 website endpoint** in the browser.
7. **Create a public hosted zone** for `chakuli.shop` in Route 53.
8. **Create an A record (Alias)** for the root domain that points to the S3 website endpoint in `ap-south-1`.
9. **Update the name servers** at the domain registrar with the 4 NS values from the Route 53 hosted zone.

### Bucket policy

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::chakuli.shop/*"
  }]
}
```

## Screenshots

**S3 bucket created**

![S3 bucket created](screenshots/01-s3-bucket-created.png)

**Object Ownership with ACLs enabled**

![Object Ownership](screenshots/02-object-ownership-acl.png)

**Static website hosting enabled**

![Static website hosting](screenshots/03-static-website-hosting.png)

**Website live on the S3 website endpoint**

![Website live](screenshots/04-website-live-s3-endpoint.png)

**Route 53 public hosted zone**

![Hosted zone](screenshots/05-route53-hosted-zone.png)

**Route 53 NS records**

![NS records](screenshots/06-route53-ns-records.png)

## Troubleshooting

| Problem | Cause and fix |
|---|---|
| Bucket name must not contain uppercase characters | Bucket names must be lowercase. Use `chakuli.shop`, not `Chakuli.shop`. |
| `403 Forbidden` on the website endpoint | Files are not public. Turn off Block Public Access, add the bucket policy or make the objects public with ACL. |
| Browser shows "Not secure" | The S3 website endpoint supports HTTP only. Use CloudFront with an ACM certificate for HTTPS. |
| Root domain cannot use CNAME | Use an Alias A record in Route 53. |

## Key learnings

- The bucket name must match the domain name.
- Root domains need an Alias A record, not a CNAME.
- S3 website hosting works over HTTP. HTTPS needs CloudFront and ACM.
- DNS changes can take up to 48 hours to propagate.

## Repository structure

```
.
├── README.md
├── docs/
│   └── Sakshi_Somvanshi_AWS_S3_Static_Website_Hosting.pdf
├── screenshots/    (including architecture-diagram.png)
└── website/        (index.html, style.css, images)
```

## Documentation

Full step-by-step documentation with screenshots: [docs/Sakshi_Somvanshi_AWS_S3_Static_Website_Hosting.pdf](docs/Sakshi_Somvanshi_AWS_S3_Static_Website_Hosting.pdf)
