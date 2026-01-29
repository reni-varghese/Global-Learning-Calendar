# Global Learning Calendar - AWS App Runner Deployment

## Prerequisites
- AWS Account with App Runner access
- RDS MySQL database instance
- GitHub repository with the code

## Environment Variables to Configure in AWS App Runner

Before deploying, you need to set these environment variables in the App Runner console:

### Required Environment Variables:
```
DB_URL=jdbc:mysql://your-rds-endpoint.rds.amazonaws.com:3306/learning_db?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
DB_USERNAME=admin
DB_PASSWORD=your-secure-password
SERVER_PORT=8080
SPRING_PROFILES_ACTIVE=prod
```

## Deployment Steps

### 1. Setup RDS MySQL Database
```bash
# Create RDS MySQL instance in AWS Console
# Note down the endpoint URL
# Create database: learning_db
# Configure security group to allow App Runner access
```

### 2. Deploy to App Runner

1. **Go to AWS App Runner Console**
   - https://console.aws.amazon.com/apprunner/

2. **Create New Service**
   - Click "Create service"

3. **Source Configuration**
   - Repository type: Source code repository
   - Connect to GitHub
   - Select repository: `reni-varghese/Global-Learning-Calendar`
   - Branch: `main`
   - Deployment trigger: Automatic

4. **Build Configuration**
   - Configuration file: Use configuration file (apprunner.yaml)
   
5. **Service Configuration**
   - Service name: `global-learning-calendar`
   - Virtual CPU: 1 vCPU
   - Memory: 2 GB
   - Port: 8080

6. **Environment Variables**
   Add all the required environment variables listed above

7. **Auto Scaling**
   - Min instances: 1
   - Max instances: 5
   - Max concurrency: 100

8. **Health Check**
   - Protocol: HTTP
   - Path: `/actuator/health`
   - Interval: 10 seconds
   - Timeout: 5 seconds
   - Healthy threshold: 1
   - Unhealthy threshold: 5

9. **Review and Create**

### 3. Post-Deployment

After deployment, App Runner will provide you with a URL like:
```
https://xxxxxxxxxx.us-east-1.awsapprunner.com
```

Test the deployment:
```bash
curl https://your-app-runner-url.awsapprunner.com/actuator/health
```

### 4. Update Frontend

Update your frontend `.env` file with the App Runner URL:
```
VITE_API_BASE_URL=https://your-app-runner-url.awsapprunner.com
```

Then rebuild and redeploy your frontend.

## Monitoring

- **Logs**: Available in App Runner console under "Logs" tab
- **Metrics**: CloudWatch metrics for CPU, Memory, Requests
- **Health**: Monitor via `/actuator/health` endpoint

## Troubleshooting

### Database Connection Issues
- Check RDS security group allows App Runner
- Verify DB_URL, DB_USERNAME, DB_PASSWORD are correct
- Check RDS is publicly accessible or in same VPC

### Build Failures
- Check Maven build logs in App Runner
- Verify Java 17 compatibility
- Check all dependencies are available

### Memory Issues
- Increase memory allocation in service settings
- Adjust JAVA_OPTS in apprunner.yaml

## Cost Optimization

- Use auto-scaling to scale down during low traffic
- Use RDS instance scheduling for non-production environments
- Monitor CloudWatch costs

## Security Best Practices

- Use AWS Secrets Manager for database credentials
- Enable HTTPS only
- Configure CORS properly
- Use IAM roles for AWS service access
- Enable CloudWatch logging

## Useful Commands

```bash
# Test local build
mvn clean package -DskipTests

# Test JAR locally
java -jar target/global-learning-calendar-1.0.0.jar

# Check JAR size
ls -lh target/*.jar
```

## Support

For issues, check:
- App Runner logs in AWS Console
- CloudWatch logs
- Application health endpoint: `/actuator/health`
