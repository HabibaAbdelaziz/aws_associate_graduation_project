# Car Inventory Processing Pipeline (Serverless Graduation Project)

A serverless data processing pipeline built with AWS to process car brand/model CSV files. It includes validation, cleaning, and querying capabilities with email alerts and analytics-ready architecture.

### Demo Video


https://github.com/user-attachments/assets/9581f108-64fd-4e1c-b332-e5c47deb350b



### 📌 Architecture Diagram
<img width="2276" height="2409" alt="solution_architecture_diagram" src="https://github.com/user-attachments/assets/82d4cb6f-0bdc-473c-9919-1e22aa1d25fa" />




---
### 📐 Architecture Overview

1. A user uploads a CSV file (e.g., `cars_new.csv`) to the `car-inventory-uploads-habiba` S3 bucket.
2. This triggers the `trigger_step_function` Lambda function.
3. The Lambda function starts the AWS Step Function with the uploaded file's S3 location.
4. The Step Function coordinates a pipeline of Lambda functions:
   - `parse_csv_from_s3`: Parses the CSV into rows.
   - `filter_valid_brands`: Filters rows by brand starting with 'A' and validates.
   - `save_filtered_to_dynamodb`: Saves accepted cars to DynamoDB.
   - `generate_report` (optional): Generates summary of processed records.
   - `notify_processing_status` (optional): Sends notification (e.g., via SNS or SES).



## ✅ Features

- Upload tab-delimited car CSVs to S3
- Lambda parses and validates the data
- Step Functions orchestrate the workflow
- Clean entries saved to DynamoDB
- Summary report uploaded to S3
- Email notification via SNS
- API Gateway endpoint to query by brand

---





---

## 🗂️ Project Structure

```
.
├── lambdas/
│   ├── trigger_step_funtion/
│   │   └── lambda_function.py
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
├── step_function_execution.json
├── architecture-diagram.png
├── README.md
├── LICENSE
├── live_endpoint_Invoke_URL.txt
├── solution_architecture_diagram.png
└── .gitignore
```

---

## 🚀 How It Works

### 1. Upload CSV

A user uploads a comma-delimited CSV file to an S3 bucket (`car-inventory-uploads-habiba`). 

###  2. Lambda: `trigger_step_function`

- **Trigger**: S3 upload to `car-inventory-uploads-habiba`
- **Purpose**: Extracts S3 bucket/key and starts the Step Function with that input.
- **Environment Variable**:
  - `STEP_FUNCTION_ARN`: ARN of the state machine to execute.


### 3. Parse CSV → Validate → Save to DB

- **parse\_csv\_from\_s3** reads and splits rows.
- **filter\_valid\_brands** strips, formats, and filters valid brands.
- **save\_filtered\_to\_dynamodb** stores clean data in DynamoDB (`FilteredCars`).

### 4. Reporting

- A summary report is uploaded to a second S3 bucket (`car-processing-reports-habiba`).
- SNS sends an email notification with counts.

### 5. Querying

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

Developed by [Habiba Abdelaziz] for AWS Solution Architect Associate Manara Graduation Project – 2025

