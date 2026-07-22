# EyeScan ~ AI-Powered Cataract Screening Platform

> Early detection. Accessible screening. Powered by ensemble deep learning.

---

## The Problem

Cataract is the leading cause of preventable blindness across the globe and is estimated to afflict up to 94 million individuals. While cataract is a readily curable disease, about half of all patients needing cataract surgery do not have access to the treatment due to the absence of specialists and equipment in deprived regions.

The current technique of diagnosing cataract includes slit lamp biomicroscopy performed by a specially trained expert. This type of test makes cataract testing too expensive, inconvenient, and laborious for many people. As a result, patients only seek medical help once their vision gets considerably damaged.

## The Solution 
**EyeScan** is a convenient solution that uses an innovative web-based artificial intelligence platform to screen for cataracts using just an anterior eye photo. EyeScan is developed as an assistance tool for medical professionals and is aimed at educational and preliminary cataract screening.

---

## How It Works

### Application Workflow

```
User registers / logs in
        ↓
Uploads an anterior eye image
        ↓
Image is validated (Gemini Vision API checks it's a real eye image)
        ↓
Image is preprocessed (resized to 224×224, RGB normalised)
        ↓
Passed to both VGG19 and MobileNetV2 models independently
        ↓
Soft Voting Ensemble averages both probability outputs
        ↓
Prediction (Cataract / Normal) + confidence score produced
        ↓
User reports symptoms (blurry vision, halos, glare sensitivity, etc.)
        ↓
Gemini API generates a personalised screening report
        ↓
User interacts with AI chatbot for follow-up questions
        ↓
Session saved — user can revisit previous screening history
```

### How Our Solution Works

EyeScan works as a web-based screening assistant that guides the user from image upload to a full AI-generated report in a few simple steps.

**Step 1 : User Authentication**

The user creates an account or logs in through the EyeScan web interface. Authentication is handled using JWT tokens, which are stored on the client and sent with every subsequent request. This ensures all screening data is private and tied to the individual user.

**Step 2 : Image Upload**

Once logged in, the user uploads an anterior eye image through the chat interface. The system accepts JPEG, PNG, and WebP formats up to 8MB. The image is stored securely on the server and assigned a unique ID.

**Step 3 : Image Validation**

Before any prediction is made, the uploaded image is passed to the Gemini Vision API to verify that it is a valid anterior eye photograph. If the image is not an eye image — for example, a random photo or an unrelated medical image — the system rejects it and asks the user to upload a correct image. This prevents the model from producing meaningless predictions.

**Step 4 : Preprocessing**

The validated image is resized to 224×224 pixels, converted to RGB, and normalised. This standardises the input regardless of the original image size, brightness, or quality, and matches the format the CNN models were trained on.

**Step 5 : Ensemble Prediction**

The preprocessed image is passed simultaneously to two deep learning models — VGG19 and MobileNetV2 — both trained on a balanced dataset of 2,292 anterior eye images. Each model independently produces a probability score for the two classes: Cataract and Normal. These probability scores are averaged using soft voting to produce a single ensemble prediction. The final output is the predicted class along with a confidence percentage.

**Step 6 : Symptom Collection**

Alongside the image result, the user is asked about five common cataract symptoms: blurry vision, difficulty seeing at night, halos around lights, faded colours, and light sensitivity. These responses are collected through the chat interface and used to personalise the report.

**Step 7 : Report Generation**

The prediction result, confidence score, and symptom responses are sent to the Gemini API, which generates a structured screening report. The report includes a prediction summary, symptom analysis, educational guidance about cataracts, and a disclaimer reminding the user that the platform is for screening purposes only and not a substitute for clinical consultation.

**Step 8 : Chatbot Interaction**

After the report is delivered, the user can continue asking questions in the chat — about their result, what cataracts are, what to do next, or anything related to eye health. The chatbot is powered by Gemini and is given the prediction context so its answers are relevant to that specific user's result. The chatbot is designed to educate and guide, not to diagnose or prescribe.

**Step 9 : Session Storage**

Every screening session — including the image, prediction, confidence score, symptoms, and report — is saved to a PostgreSQL database linked to the user's account. Users can return to the platform and review their previous sessions at any time.

### Architecture Overview

```
┌─────────────────────┐        HTTP / REST        ┌──────────────────────────┐
│   React Frontend    │ ◄────────────────────────► │     Flask Backend         │
│   (Vite, Vercel)    │   JWT-authenticated calls  │   (AWS EC2, PostgreSQL)  │
└─────────────────────┘                            └────────────┬─────────────┘
                                                                │
                              ┌─────────────────────────────────┼──────────────────────┐
                              │                                 │                      │
                    ┌─────────▼────────┐           ┌───────────▼──────────┐  ┌────────▼────────┐
                    │  Ensemble Model   │           │    Gemini Vision API  │  │   PostgreSQL DB  │
                    │  VGG19 +          │           │  Image validation +   │  │  Users, sessions,│
                    │  MobileNetV2      │           │  Report + Chatbot     │  │  predictions     │
                    └──────────────────┘           └──────────────────────┘  └─────────────────┘
```

---

## Key Use Cases

**1. Individual Self-Screening**
A user in a rural area photographs their eye using a smartphone and uploads it to EyeScan to get a preliminary cataract risk assessment before deciding to travel to a clinic.

**2. Community Health Workers**
A health worker uses EyeScan during a camp to quickly screen multiple individuals, flagging those who need a referral for clinical evaluation.

**3. Patient Education**
After receiving a screening result, the user interacts with the AI chatbot to understand what cataracts are, what the confidence score means, and when to see a doctor.

**4. Telemedicine Support**
A remote clinic integrates EyeScan as a first-pass screening tool before scheduling ophthalmology consultations.

---

## Features

| Feature | Description |
|---|---|
| Secure Authentication | JWT-based login and registration |
| Eye Image Upload | Accepts JPEG, PNG, WebP up to 8MB |
| Automated Image Validation | Gemini Vision rejects non-eye images before processing |
| Dual-Model Ensemble Prediction | VGG19 + MobileNetV2 soft voting for robust classification |
| Confidence Score | Percentage confidence displayed alongside prediction |
| Symptom Reporting | Users log five common cataract symptoms for a personalised report |
| AI-Generated Report | Gemini API produces a structured screening report per session |
| Conversational Chatbot | Ask follow-up questions about results, symptoms, or eye health |
| Session History | All past screenings stored and accessible per user |
| Responsive Chat Interface | Clean, accessible UI optimised for both desktop and mobile |

---

## Model Performance

Five transfer learning architectures were trained and evaluated under identical conditions on a balanced dataset of 2,292 anterior eye images.

| Model | Accuracy | Precision | Recall | F1-Score | ROC-AUC |
|---|---|---|---|---|---|
| ResNet50 | 97.09% | 97.10% | 97.09% | 97.09% | 99.63% |
| VGG16 | 97.67% | 97.70% | 97.67% | 97.67% | 99.71% |
| VGG19 | 97.97% | 98.01% | 97.97% | 97.96% | 99.71% |
| MobileNetV2 | 98.26% | 98.26% | 98.26% | 98.26% | 99.52% |
| InceptionV3 | 84.01% | 87.02% | 84.01% | 83.68% | 97.59% |
| **Ensemble (VGG19 + MobileNetV2)** | **98.84%** | **98.86%** | **98.84%** | **98.84%** | **99.66%** |

The soft-voting ensemble had **no false negatives** on the test data, meaning there were no cataract cases classified as normal, which is the essential error to avoid in a screening process.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React.js (Vite) |
| Backend | Flask (Python) |
| Database | PostgreSQL |
| ML Framework | TensorFlow / Keras |
| AI / NLP | Google Gemini API |
| Authentication | JWT (PyJWT + bcrypt) |
| Frontend Deployment | Vercel |
| Backend Deployment | AWS EC2 |

---

## Assumptions and Limitations

- **Image type:** The system is designed for anterior segment eye images only. Non-eye images are rejected by the Gemini Vision validation layer, but image quality (blur, lighting, angle) still affects prediction accuracy.
- **Binary classification:** The current model classifies images as either Cataract or Normal. Severity grading (mild, moderate, advanced) is not yet supported.
- **Screening only:** EyeScan is a screening and educational tool. It does not provide a clinical diagnosis and explicitly advises users to consult a qualified ophthalmologist.
- **In-memory session cache:** The `ANALYSES` dictionary is in-memory and resets on server restart. A production system would persist this in the database.
- **Dataset scope:** The model was trained on publicly available internet-sourced anterior eye images. Performance on clinical-grade slit-lamp images or images from specific populations may vary.

---

## Future Improvements

- **GradCAM visualisation** :- Highlight the regions of the eye image that influenced the prediction, improving explainability and user trust.
- **Severity grading** :- Extend classification to Normal / Immature Cataract / Mature Cataract.
- **Multi-disease detection** :- Expand to glaucoma, diabetic retinopathy, and age-related macular degeneration.
- **Mobile app** :- Native iOS and Android apps for field use by community health workers.
- **Clinical validation** :- Partner with ophthalmologists to validate the system on real clinical data.
- **Multilingual support** :- Extend the chatbot to support regional languages for wider accessibility.

---

## Disclaimer

EyeScan is intended for screening and educational purposes only. It is not a substitute for a professional ophthalmic examination. Always consult a qualified ophthalmologist for diagnosis and treatment.
