# Automatic Password Rotation for Built-in GCVE Accounts

This folder contains a script to automate the rotation of built-in user passwords in Google Cloud VMware Engine (GCVE). The solution is designed to improve security by ensuring that sensitive credentials are updated regularly.

## 🛠 Features

- Rotates passwords for:
  - Default vCenter service account: `CloudOwner@gve.local`
  - Default NSX-T service account: `admin`
- Follows the recommended best practices outlined by Google Cloud.

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

---

**Happy automating!**