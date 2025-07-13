# Car Inventory Processing Pipeline (Serverless Graduation Project)

A serverless data processing pipeline built with AWS to process car brand/model CSV files. It includes validation, cleaning, and querying capabilities with email alerts and analytics-ready architecture.

---

## ✅ Features

- Upload tab-delimited car CSVs to S3
- Lambda parses and validates the data
- Step Functions orchestrate the workflow
- Clean entries saved to DynamoDB
- Summary report uploaded to S3
- Email notification via SNS
- API Gateway endpoint to query by brand

---

## 📌 Architecture Diagram



---

## 🗂️ Project Structure

```
.
├── lambdas/
│   ├── parse_csv_from_s3/
│   │   └── lambda_function.py
│   ├── filter_valid_brands/
│   │   └── lambda_function.py
│   ├── save_filtered_to_dynamodb/
│   │   └── lambda_function.py
│   ├── notify_processing_status/
│   │   └── lambda_function.py
│   └── get_cars_by_brand/
│       └── lambda_function.py
├── step_function_definition.json
├── architecture-diagram.png
├── README.md
└── .gitignore
```

---

## 🚀 How It Works

### 1. Upload CSV

A user uploads a tab-delimited CSV file to an S3 bucket (`car-inventory-uploads-habiba`). This triggers the Step Function.

### 2. Parse CSV → Validate → Save to DB

- **parse\_csv\_from\_s3** reads and splits rows.
- **filter\_valid\_brands** strips, formats, and filters valid brands.
- **save\_filtered\_to\_dynamodb** stores clean data in DynamoDB (`FilteredCars`).

### 3. Reporting

- A summary report is uploaded to a second S3 bucket (`car-processing-reports-habiba`).
- SNS sends an email notification with counts.

### 4. Querying

- API Gateway `/cars?brand=Acura` invokes **get\_cars\_by\_brand** Lambda to fetch cars from DynamoDB.

---

## 🧪 Sample CSV Format

```
Brand	Model
Acura	MDX
Porsche	Boxster
Ford	Capri
```

---

## 🔐 Security: What NOT to Commit

Add this to your `.gitignore`:

```
.env
*.zip
__pycache__/
*.pyc
```

**Do NOT commit:**

- `.env` files with AWS credentials or secrets
- IAM role ARNs tied to your account
- Real email addresses or SNS topic ARNs

### 🔄 Replace sensitive code like this:

❌ **Avoid hardcoding**

✅ **Use environment variables**

```python
import os
TOPIC_ARN = os.environ["SNS_TOPIC"]
sns.publish(
  TopicArn=TOPIC_ARN,
  Message="..."
)
```

Then define `SNS_TOPIC` in the Lambda console under **Environment variables**.

---

## 🛠️ Deployment

1. Deploy each Lambda function from its folder as a zip
2. Use AWS Console to:
   - Create S3 buckets
   - Create DynamoDB table `FilteredCars`
   - Set up the Step Function using `step_function_definition.json`
   - Create API Gateway method (GET `/cars`) integrated with `get_cars_by_brand`
   - Create SNS topic `car-processing-status` and subscribe your email

---

## 📈 Sample API Response

**GET** `https://<api-id>.execute-api.<region>.amazonaws.com/prod/cars?brand=Acura`

```json
[
  {"Brand": "Acura", "Model": "MDX", "timestamp": "2025-07-13T17:21:00Z"},
  {"Brand": "Acura", "Model": "Integra", "timestamp": "2025-07-13T17:21:01Z"}
]
```

---

## 📬 Example Email Notification

```
File processed at 2025-07-13T18:11Z

Accepted: 53
Rejected: 5
```

---

## 🧾 Credits

Developed by [Habiba Abdelaziz](mailto\:habibahosameldin@hotmail.com) for AWS Graduation Project – 2025

