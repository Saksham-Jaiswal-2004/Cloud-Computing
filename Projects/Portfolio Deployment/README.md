<h1 align="center">React Portfolio Deployment on AWS</h1>

<p align="center">
  <strong>Production-level deployment using S3 + CloudFront CDN</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/AWS-S3-orange?style=flat-square&logo=amazon-s3" alt="S3" />
  <img src="https://img.shields.io/badge/AWS-CloudFront-blue?style=flat-square&logo=amazon-aws" alt="CloudFront" />
  <img src="https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react" alt="React" />
  <img src="https://img.shields.io/badge/HTTPS-Enabled-green?style=flat-square&logo=letsencrypt" alt="HTTPS" />
</p>

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Deployment Steps](#deployment-steps)
- [Cloud Skills Demonstrated](#cloud-skills-demonstrated)
- [Benefits](#benefits)
- [Optional Enhancements](#optional-enhancements)
- [Troubleshooting](#troubleshooting)

---

## 📖 Project Overview

### Project Details

| Aspect | Details |
|--------|---------|
| **Project** | Personal Portfolio Website |
| **Tech Stack** | React, HTML, CSS, JavaScript |
| **Build Tool** | Vite / Create React App |
| **AWS Services** | S3, CloudFront, ACM (Certificate Manager) |
| **Live Demo** | [CloudFront URL](https://your-distribution.cloudfront.net) |

### Key Features

✅ **Global Content Delivery** - CloudFront edge locations worldwide  
✅ **HTTPS Security** - SSL/TLS encryption with ACM  
✅ **SPA Routing Support** - React Router compatibility  
✅ **Optimized Performance** - Edge caching and compression  
✅ **Cost-Effective** - Serverless architecture with minimal maintenance  
✅ **High Availability** - 99.99% uptime SLA

---

## 🏗️ Architecture

### Architecture Diagram

![Deployment Architecture](./deployment-diagram.png)

### Data Flow

```
┌─────────────┐     ┌──────────────────┐     ┌─────────────┐     ┌──────────┐
│  Developer  │────▶│  React Build     │────▶│  S3 Bucket  │────▶│ CloudFront│
│             │     │  (npm run build) │     │  (Origin)   │     │   (CDN)   │
└─────────────┘     └──────────────────┘     └─────────────┘     └─────┬─────┘
                                                                         │
                                                                         ▼
                                                              ┌────────────────────┐
                                                              │  Global Users      │
                                                              │  (HTTPS Access)    │
                                                              └────────────────────┘
```

### Component Breakdown

1. **React Application**
   - Built using `npm run build`
   - Generates optimized static files in `dist/` folder
   - Minified JS, CSS, and assets

2. **S3 Bucket (Origin)**
   - Hosts all static files
   - Configured for static website hosting
   - Index document: `index.html`
   - Error document: `index.html` (enables SPA routing)
   - Public read access via bucket policy

3. **CloudFront Distribution**
   - Fetches content from S3 origin
   - Distributes via global edge locations
   - HTTPS enforcement with ACM certificate
   - Cache optimization for performance
   - Custom error responses for SPA support

4. **End Users**
   - Access via CloudFront URL
   - Low-latency worldwide
   - Secure HTTPS connection

---

## 🛠️ Deployment Steps

### Step 1: Build React Application

```bash
# Install dependencies
npm install

# Create production build
npm run build
```

**Output:** Creates `dist/` folder with optimized static assets.

---

### Step 2: Create S3 Bucket

1. **Navigate to S3 Console**
   - Create new bucket with a unique name (e.g., `my-portfolio-site`)
   - Select appropriate region
   - Uncheck "Block all public access"

2. **Enable Static Website Hosting**
   - Go to **Properties** → **Static website hosting**
   - Enable static hosting
   - Index document: `index.html`
   - Error document: `index.html` (for React Router)

3. **Note the S3 Website Endpoint**
   - Example: `http://my-portfolio-site.s3-website-us-east-1.amazonaws.com`

---

### Step 3: Upload Build Files

1. **Upload to S3**
   ```bash
   # Using AWS CLI
   aws s3 sync dist/ s3://my-portfolio-site/
   ```

2. **Or use S3 Console**
   - Upload all contents from `dist/` folder
   - Include all subfolders: `assets/`, `static/`, etc.
   - Ensure `index.html` is at the root

---

### Step 4: Configure S3 Bucket Policy

Add this policy to allow public read access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-portfolio-site/*"
    }
  ]
}
```

**Location:** Bucket → **Permissions** → **Bucket Policy**

---

### Step 5: Create CloudFront Distribution

1. **Navigate to CloudFront Console**
   - Click **Create Distribution**

2. **Origin Settings**
   - **Origin Domain:** Select your S3 bucket website endpoint (use the website endpoint, not the bucket endpoint)
   - **Protocol:** HTTP only (S3 website endpoint doesn't support HTTPS)

3. **Default Cache Behavior**
   - **Viewer Protocol Policy:** Redirect HTTP to HTTPS
   - **Allowed HTTP Methods:** GET, HEAD
   - **Cache Policy:** CachingOptimized

4. **Settings**
   - **Price Class:** Use all edge locations (or customize)
   - **Alternate Domain Names (CNAMEs):** Add custom domain if available
   - **SSL Certificate:** Default CloudFront certificate or custom ACM certificate

5. **Custom Error Responses** (for SPA routing)
   - Add error response:
     - **HTTP Error Code:** 403
     - **Customize Error Response:** Yes
     - **Response Page Path:** `/index.html`
     - **HTTP Response Code:** 200
   - Repeat for error code 404

6. **Create Distribution**
   - Wait 5-15 minutes for deployment

---

### Step 6: Access Your Portfolio

**CloudFront URL:** `https://d1234abcd5678.cloudfront.net`

Test the deployment:
- ✅ Homepage loads correctly
- ✅ Navigation between routes works
- ✅ HTTPS is enabled
- ✅ Assets load quickly

---

## 💡 Cloud Skills Demonstrated

### AWS Services

- ✅ **S3 Static Hosting**
  - Bucket configuration
  - Website hosting setup
  - Bucket policies and permissions
  - SPA error document configuration

- ✅ **CloudFront CDN**
  - Distribution creation
  - Origin configuration
  - Cache policies and behaviors
  - Custom error responses
  - HTTPS/SSL certificate management

- ✅ **Security**
  - Public access configuration
  - HTTPS enforcement
  - Bucket policy management

- ✅ **Performance Optimization**
  - Edge caching strategies
  - Content delivery optimization
  - Asset compression

### Deployment Skills

- React build process optimization
- AWS infrastructure setup
- Static asset management
- SPA routing configuration
- Domain and SSL management

---

## 🎯 Benefits

| Feature | Benefit |
|---------|---------|
| **HTTPS (SSL)** | Secure encrypted connection for all users |
| **Global CDN** | Fast content delivery from 400+ edge locations worldwide |
| **Edge Caching** | Reduced latency and bandwidth costs |
| **SPA Support** | React Router and client-side routing work seamlessly |
| **Scalability** | Automatically handles traffic spikes |
| **High Availability** | 99.99% uptime with AWS infrastructure |
| **Cost-Effective** | Pay-as-you-go, serverless architecture |
| **Zero Maintenance** | No server management required |

---

## 🔧 Optional Enhancements

### 1. Custom Domain with Route 53

- Register or transfer domain to Route 53
- Create hosted zone
- Request ACM certificate for custom domain
- Add CNAME record pointing to CloudFront distribution

### 2. Origin Access Identity (OAI)

```json
// Restrict S3 access to CloudFront only
// Remove public bucket policy
// Use OAI for secure origin access
```

### 3. Performance Optimizations

- Enable gzip/brotli compression
- Configure cache-control headers
- Implement browser caching
- Use CloudFront functions for edge logic

### 4. Monitoring & Analytics

- Enable CloudFront access logs
- Set up CloudWatch alarms
- Monitor cache hit ratio
- Track bandwidth usage

### 5. CI/CD Pipeline

```yaml
# GitHub Actions workflow
- Build React app
- Upload to S3
- Invalidate CloudFront cache
- Automated deployment on push
```

### 6. Security Enhancements

- Add WAF (Web Application Firewall)
- Configure security headers
- Enable field-level encryption
- Implement DDoS protection

---

## 🐛 Troubleshooting

### Issue: 404 Errors on Refresh

**Solution:** Ensure custom error responses are configured:
- Error Code 403/404 → Response Page: `/index.html` → HTTP Code: 200

### Issue: Changes Not Reflecting

**Solution:** Invalidate CloudFront cache:
```bash
aws cloudfront create-invalidation --distribution-id YOUR_DIST_ID --paths "/*"
```

### Issue: Bucket Access Denied

**Solution:** Verify bucket policy and public access settings

### Issue: Slow Initial Load

**Solution:** 
- Implement code splitting
- Optimize bundle size
- Enable compression
- Use lazy loading for routes

---

## 📊 Performance Metrics

After deployment, expect:

- **Load Time:** < 1 second globally
- **Time to First Byte (TTFB):** < 100ms from edge locations
- **Cache Hit Ratio:** > 85% after warm-up
- **Availability:** 99.99%+

---

## 📚 Resources

- [AWS S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)
- [AWS CloudFront Documentation](https://docs.aws.amazon.com/cloudfront/)
- [React Deployment Guide](https://create-react-app.dev/docs/deployment/)

---

<p align="center">
  <sub>Deployed with ☁️ AWS | Secured with 🔒 HTTPS | Optimized for 🚀 Performance</sub>
</p>