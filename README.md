# stock-price-sms
Use Python SMTP, Gmail(and optionally Lambda) to schedule receiving an email with stock price of a desired company

### Note:
AWS Charges applicable only for Lambda as opposed to the SMS option where the SNS charges also apply

### Prerequisites:

 - Python 3.6
 - "company_code" refers to the Stock Exchange code: GOOG, AAPL, AMZN etc
 - "exchange_api_key" needs to be obtained from https://openexchangerates.org/account/app-ids (Sign Up required, 1000 calls free every month)
 - sender_email is assumed to be Gmail here, the usual "Less Secure" apps and security restrictions apply
 - If running on AWS Lambda, the "requests" module can be imported from botocode library, and the Environment variables can be used from Lambda(paste the contents of stock-price-email-lambda.md file into a new and completely blank Python3.6 Lambda function)
 
 
### Instructions:

For testing:

```
python stock-price-email.py
```

For scheduling in cron (8 am everyday from an EC2 instance):
```
0 8 * * * python /home/ec2-user/stock-price-email.py
```

### Further development:

- Can be improved by adding more company codes since the Yahoo Finance URL accepts comma separated codes
- Can be used with CloudFormation template that creates the Lambda function, the required roles and the CloudWatch cron schedules
