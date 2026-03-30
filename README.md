# CineWorld

## Overview

Django-based movie review web service deployed on AWS using ECS Fargate.

---

## Architecture

User → CloudFront → ALB → ECS → Django  
↓  
RDS  
↓  
S3  

---

## Tech Stack

### Backend
- Django  
- Gunicorn  

### Infrastructure
- ECS Fargate  
- ALB  
- RDS  
- S3  
- CloudFront  

### DevOps
- Docker  
- ECR  
- GitHub Actions  

### Monitoring
- CloudWatch  

---

## Features

- Movie review system  
- User authentication  
- Static file delivery via CDN  

---

## Deployment

GitHub → Actions → ECR → ECS → ALB → CloudFront  

---

## Troubleshooting

### Health Check Failure
- Cause: ALB checked `/` which returned 500  
- Solution: `/health/` endpoint created  

### Database Error
- Cause: migrations not executed  
- Solution: run migrate in container  

### ALLOWED_HOSTS Issue
- Cause: host mismatch  
- Solution: configure via environment variable  

---

## Key Learnings

- ECS is stateless  
- Initialization is required  
- Health checks are critical  
