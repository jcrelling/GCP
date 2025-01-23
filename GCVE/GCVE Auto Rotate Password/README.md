
# GCVE Password Auto-Rotate Solution

This script provides an automated solution for rotating the passwords of built-in service accounts in Google Cloud VMware Engine (GCVE). It ensures compliance with best practices, improving security by minimizing the risk of password compromise.

## Features
- Automatically resets passwords for:
  - `CloudOwner@gve.local` (vCenter default service account)
  - `admin` (NSX-T default service account)
- Supports regular password rotation via Cloud Scheduler (default: every 30 days).
- Uses Google Cloud services like Cloud Run and Cloud Scheduler.

<<<<<<< HEAD
## 📋 Requirements

1. Access to a Google Cloud project with GCVE enabled.
2. Proper IAM permissions for managing GCVE resources.
3. A valid service account with the necessary roles.
4. The GCVE private cloud must already be set up.

## 📥 Input Parameters

The script requires the following input parameters:

| Parameter        | Description                                                                 |
|------------------|-----------------------------------------------------------------------------|
| `REGION`         | The region where the private cloud is deployed                            |
| `ZONE`           | The zone where the private cloud is deployed                            |
| `PROJECT_ID`     | The Google Cloud project ID.                                               |
| `SERVICE_ACCOUNT`| The name of the service account to execute the solution components.         |
| `PRIVATE_CLOUD_ID`| The ID of the GCVE private cloud containing the users to be reset.          |

## 🚀 Deployment Steps

1. Navigate to this folder in your local environment or where the script file is saved.

2. Set up your environment:
   - Ensure you have the Google Cloud CLI installed and authenticated.

3. Execute the script:
   ```bash
   ./gcve-auto-pwd-rotate.sh --region=REGION \
                             --zone=zone \
                             --project-id=PROJECT_ID \
                             --service-account=SERVICE_ACCOUNT \
                             --private-cloud-id=PRIVATE_CLOUD_ID
   ```

4. Verify the password rotation by logging into vCenter and NSX-T with the new credentials.

## 📝 Notes

- **Password Rotation Frequency**: It is recommended to rotate passwords every 60 to 90 days.
- **Break-glass Access**: The default accounts should only be used for initial configuration and emergency access.

## 📚 Reference

Follow the [official GCVE guide](https://cloud.google.com/vmware-engine/docs/vmware-platform/howto-access-management#reset-access-credentials) for detailed steps on resetting credentials manually.

## 🛡 Security Considerations

- Ensure the script is executed in a secure environment.
- Store sensitive parameters (like `SERVICE_ACCOUNT`) in a secure location such as Google Secret Manager.

## 🤝 Contributions

Contributions are welcome! Please submit a pull request or open an issue to share your suggestions.

## 📄 License

This project is licensed under the Apache License 2.0. See the LICENSE file for details.
=======
## Prerequisites
1. A Google Cloud project with the following APIs enabled:
   - Cloud Scheduler API
   - Cloud Run API
   - Cloud Build API
   - VMware Engine API
2. Sufficient IAM permissions to create service accounts, roles, and policies.
3. Docker installed locally (if building the Docker image manually).

---

## Setup Instructions

### **Automated Execution Using the Script**

1. Clone the repository and navigate to the folder containing the script.
```bash
cd <repository-folder>
```
2. Make the script executable:
```bash
chmod +x setup_gcve_auto_rotate.sh
```
3. Run the script and follow the prompts:
```bash
./setup_gcve_auto_rotate.sh
```

---

### **Manual Execution Step-by-Step**

#### 1. Set the Active Project
```bash
gcloud config set project <PROJECT_ID>
```

#### 2. Enable Required APIs
```bash
gcloud services enable cloudscheduler.googleapis.com run.googleapis.com cloudbuild.googleapis.com vmwareengine.googleapis.com
```

#### 3. Create a Service Account
```bash
gcloud iam service-accounts create <SERVICE_ACCOUNT_NAME> \
  --description="Service account to execute the GCVE Password Auto Reset solution" \
  --display-name=<SERVICE_ACCOUNT_NAME>
```

#### 4. Create a Custom IAM Role
```bash
gcloud iam roles create GCVEPwdAutoRotate --project <PROJECT_ID> \
  --title "GCVE Password Auto Rotate" \
  --description "Role with permissions to auto-rotate GCVE passwords" \
  --permissions "vmwareengine.operations.get,vmwareengine.privateClouds.get,vmwareengine.privateClouds.resetNsxCredentials,vmwareengine.privateClouds.resetVcenterCredentials" \
  --stage=Alpha
```

#### 5. Assign IAM Roles to the Service Account
```bash
gcloud projects add-iam-policy-binding <PROJECT_ID> \
  --member "serviceAccount:<SERVICE_ACCOUNT_NAME>@<PROJECT_ID>.iam.gserviceaccount.com" \
  --role "roles/run.invoker"

# Add other necessary roles
```

#### 6. Grant Impersonation Access
```bash
gcloud iam service-accounts add-iam-policy-binding <SERVICE_ACCOUNT_NAME>@<PROJECT_ID>.iam.gserviceaccount.com \
  --member "user:<YOUR_EMAIL>" \
  --role "roles/iam.serviceAccountUser"
```

#### 7. Create Docker Files
1. Create a directory and navigate to it:
```bash
mkdir gcve-auto-rotate-pwd && cd gcve-auto-rotate-pwd
```
2. Create `gcve-auto-rotate-pwd.sh`:
```bash
cat << EOF > gcve-auto-rotate-pwd.sh
#!/bin/bash
gcloud vmware private-clouds vcenter credentials reset \
 --private-cloud=<PRIVATE_CLOUD_ID> \
 --project=<PROJECT_ID> \
 --location=<ZONE>

gcloud vmware private-clouds nsx credentials reset \
 --private-cloud=<PRIVATE_CLOUD_ID> \
 --project=<PROJECT_ID> \
 --location=<ZONE>
EOF
chmod +x gcve-auto-rotate-pwd.sh
```
3. Create a `Dockerfile`:
```bash
cat << EOF > Dockerfile
FROM google/cloud-sdk:latest
WORKDIR /workspace
COPY gcve-auto-rotate-pwd.sh .
RUN chmod +x ./gcve-auto-rotate-pwd.sh
CMD [ "./gcve-auto-rotate-pwd.sh" ]
EOF
```

#### 8. Build and Push the Docker Image
```bash
gcloud builds submit -t gcr.io/<PROJECT_ID>/gcve-auto-rotate-pwd
```

#### 9. Deploy the Cloud Run Job
```bash
gcloud beta run jobs create gcve-auto-rotate-pwd \
  --image gcr.io/<PROJECT_ID>/gcve-auto-rotate-pwd \
  --max-retries 5 \
  --service-account <SERVICE_ACCOUNT_NAME>@<PROJECT_ID>.iam.gserviceaccount.com \
  --region <REGION>
```

#### 10. Schedule the Job with Cloud Scheduler
```bash
gcloud scheduler jobs create http gcve-auto-rotate-pwd \
  --location <REGION> \
  --schedule="0 0 */30 * *" \
  --uri="https://<REGION>-run.googleapis.com/apis/run.googleapis.com/v1/namespaces/<PROJECT_ID>/jobs/gcve-auto-rotate-pwd:run" \
  --http-method POST \
  --oauth-service-account-email <SERVICE_ACCOUNT_NAME>@<PROJECT_ID>.iam.gserviceaccount.com
```

---

## License
This project is licensed under the Apache 2.0 License. See the [LICENSE](LICENSE) file for details.
>>>>>>> 563458a (Update all README file)
