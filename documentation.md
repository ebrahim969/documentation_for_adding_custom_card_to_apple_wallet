## 📑 Documentation: Adding a Custom Medical Card to Apple Wallet

This guide helps you to handle adding custom card from your Flutter App to Apple Wallet

---

### 1️⃣ Backend Developer Guide (Generate & Sign Passes)

🔹 Prerequisites

Apple Developer Account ($99/year).

A Pass Type ID created in the Apple Developer portal "https://developer.apple.com/account/resources/identifiers/passTypeId"
.

Certificates:

Pass Type Certificate (for signing passes).

Apple WWDR certificate.

You’ll need both installed on your server.

---

### 🔹 Pass Structure

A pass (.pkpass) is a ZIP file containing:

```shell
medicalcard.pkpass
 ├── pass.json
 ├── icon.png
 ├── logo.png
 ├── manifest.json
 ├── signature
```

### Example pass.json (Medical Card)

```json
{
  "formatVersion": 1,
  "passTypeIdentifier": "pass.com.yourcompany.medicalcard",
  "serialNumber": "123456",
  "teamIdentifier": "ABCD1234EF",
  "organizationName": "Your Clinic",
  "description": "Medical ID Card",
  "logoText": "Medical Card",
  "foregroundColor": "rgb(255,255,255)",
  "backgroundColor": "rgb(0,122,255)",
  "generic": {
    "primaryFields": [
      {
        "key": "name",
        "label": "Name",
        "value": "John Doe"
      }
    ],
    "secondaryFields": [
      {
        "key": "blood",
        "label": "Blood Type",
        "value": "O+"
      }
    ],
    "auxiliaryFields": [
      {
        "key": "emergency",
        "label": "Emergency Contact",
        "value": "+1 555 123 4567"
      }
    ]
  }
}
```

---

## 🔹 Steps to Generate Pass (Server-side)

1. Prepare assets → pass.json, icon.png, logo.png.

2. Create manifest.json → SHA1 hash of each file. Example:

```json
{
  "pass.json": "3b1c2d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c",
  "icon.png": "abcd1234..."
}

```
3. Sign the manifest.json with your Pass certificate and WWDR cert → produces signature.
Example (OpenSSL):

```bash
openssl smime -binary -sign \
  -certfile WWDR.pem \
  -signer PassCertificate.pem \
  -inkey PassCertificate.key \
  -in manifest.json \
  -out signature \
  -outform DER

```
4. Zip everything into medicalcard.pkpass.
5. Host the file (e.g. https://api.yourserver.com/passes/medicalcard.pkpass).

---

## 🔹 Backend Endpoint Example

1. Endpoint: GET /api/pass/medical-card/:userId
2. Response: returns a signed .pkpass file with correct MIME type:
   
```bash
  Content-Type: application/vnd.apple.pkpass
```




