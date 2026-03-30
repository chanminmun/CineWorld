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
ECS Task Termination (Health Check Failure)
Issue: ECS tasks repeatedly stopped
Cause: ALB health check pointed to /, which returned 500
Solution: Created a dedicated health check endpoint
def health(request):
    return HttpResponse("ok")
Django Template Error
Issue: TemplateSyntaxError related to socialaccount
Cause: {% load socialaccount %} remained after removing provider config
Solution: Removed unused template code
Database Table Not Found
Issue: no such table error
Cause: Migrations not executed in container
Solution:
python manage.py migrate --noinput
ALLOWED_HOSTS Issue
Issue: Application failed behind ALB
Cause: Host header mismatch
Solution:
ALLOWED_HOSTS = os.getenv("ALLOWED_HOSTS", "").split(",")
Code Changes Not Reflected
Cause: Image not rebuilt
Solution:
docker build → push → redeploy
Key Learnings
ECS is a container execution platform, not a traditional server
Containers are stateless; external DB (RDS) is required
Initialization steps (migrate, collectstatic) are critical
Health check configuration affects service stability
CloudWatch logs are essential for debugging
Deployment URLs
ALB: http://cineworld-alb-144723031.ap-northeast-2.elb.amazonaws.com
CloudFront: https://d3dasdsawdcs44.cloudfront.net
Summary

This project demonstrates deploying a Django application on AWS using ECS Fargate
and resolving real-world deployment issues in a cloud environment.
