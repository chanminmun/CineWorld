CineWorld - Movie Review Web Service on AWS
Overview

CineWorld is a Django-based movie review web application deployed on AWS.

The project focuses on containerizing the application with Docker and deploying it using
ECS Fargate, along with integrating ALB, RDS, S3, and CloudFront.

It also includes troubleshooting of real deployment issues encountered during the process.

Architecture

User → CloudFront → ALB → ECS Fargate → Django (Gunicorn)
                                      ↓
                                     RDS (MySQL)
                                      ↓
                                     S3 (static)
Description
CloudFront: CDN for static content delivery
ALB: Routes traffic to ECS services
ECS Fargate: Runs containerized Django application
RDS (MySQL): Stores application data
S3: Stores static files
CloudWatch: Logging and monitoring
Tech Stack
Backend: Django, Gunicorn
Container: Docker, Amazon ECR
Infrastructure: ECS Fargate, ALB, RDS, S3, CloudFront
CI/CD: GitHub Actions
Monitoring: CloudWatch
Features
Movie browsing and review functionality
User authentication (Django Auth)
Static file delivery via S3 and CloudFront
Deployment Pipeline
GitHub → GitHub Actions → ECR → ECS → ALB → CloudFront
Troubleshooting Highlights
1. ECS Task Termination (Health Check Failure)

Issue
ECS tasks repeatedly stopped after deployment.

Cause
The ALB health check was configured to /, which returned a 500 error.

Solution
Created a dedicated health check endpoint:

def health(request):
    return HttpResponse("ok")

Updated target group health check path to /health/.

2. Django Template Error

Issue
TemplateSyntaxError related to socialaccount.

Cause
{% load socialaccount %} remained in templates after removing the provider configuration.

Solution
Removed unused template tags and related code.

3. Database Table Not Found

Issue
no such table error when accessing application features.

Cause
Migrations were not executed inside the ECS container.

Solution
Added migration step to container startup:

python manage.py migrate --noinput
4. ALLOWED_HOSTS Configuration

Issue
Application returned errors when accessed via ALB.

Cause
Host header mismatch due to ALB health check behavior.

Solution
Configured ALLOWED_HOSTS using environment variables:

ALLOWED_HOSTS = os.getenv("ALLOWED_HOSTS", "").split(",")
5. Code Changes Not Reflected

Issue
Application behavior did not change after updating code.

Cause
Docker image was not rebuilt and pushed.

Solution
Rebuilt image and redeployed through ECS.

Key Learnings
ECS is a container execution platform, not a traditional server
Containers are stateless; external services like RDS are required
Initialization steps (migrations, static collection) are essential in container environments
Health check configuration directly affects service availability
Logs (CloudWatch) are critical for debugging
Deployment URLs
ALB: http://cineworld-alb-144723031.ap-northeast-2.elb.amazonaws.com
CloudFront: https://d3dasdsawdcs44.cloudfront.net
Summary

This project demonstrates deploying a Django application on AWS using ECS Fargate,
along with diagnosing and resolving real-world deployment issues in a cloud environment.
