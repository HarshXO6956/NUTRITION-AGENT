# NutriMate AI - The Smartest AI Nutrition Assistant

**Project:** IBM SkillsBuild University Education: Edunet Foundation Internship  
**Problem Statement No. 8:** Nutrition Agent  
**Mandated Technologies:** IBM watsonx Orchestrate, IBM Granite Foundation Models, IBM Cloud Lite Services, (Optional: IBM Docling)

---

## 🌟 Executive Summary

**NutriMate AI** is an intelligent, culturally-aware, explainable AI nutrition assistant and interactive health dashboard. Unlike traditional diet apps that enforce rigid, generic Western meal plans, NutriMate AI combines:
1. **IBM watsonx Orchestrate + IBM Granite:** Autonomous multi-turn conversational agent with ReAct reasoning, structured output, and explainable "Why" rationales.
2. **Culturally Grounded RAG:** Knowledge grounding built upon ICMR-NIN dietary guidelines, Indian Food Composition Tables (IFCT), and regional staples (North, South, East, West, Central India).
3. **100% Client-Side Health Dashboard:** Zero-backend, zero-database architecture calculating BMI, Mifflin-St Jeor Basal Metabolic Rate (BMR), Daily Energy Expenditure (TDEE), and macronutrient splits with absolute user privacy.
4. **Clinical Safety Guardrails:** Enforces physiological calorie floors (1,200 kcal for females, 1,500 kcal for males), strict non-diagnostic boundaries, pediatric doctor escalation, and mandatory medical disclaimers on every plan.

---

## 📁 Repository Structure

```
nutrition-agent/
├── index.html                  # Accessible, responsive single-page web application
├── css/
│   └── styles.css              # Custom design system (WCAG AA compliant, responsive)
├── js/
│   ├── calculators.js          # Pure client-side metabolic and macro formulas
│   └── ui.js                   # Interactive UI handling, clipboard sync, theme toggle
├── assets/
│   ├── logo.svg                # Vector brand identity
│   └── architecture.svg        # High-resolution system architecture diagram
├── docs/
│   ├── PRD_Nutrition_Agent.md  # Original Product Requirements Document
│   ├── regional_foods.md       # Knowledge base: Indian regional staples & budget swaps
│   └── condition_guidelines.md # Knowledge base: Conservative health condition rules
└── README.md                   # Complete implementation and presentation guide
```

---

## 🚀 Quick Start: Running the Frontend Locally

NutriMate AI uses a **pure static frontend (zero backend, zero build step)**.

### Option 1: VS Code Live Server (Recommended)
1. Open this repository folder in VS Code or Antigravity IDE.
2. Right-click `index.html` and click **"Open with Live Server"**.
3. Access at `http://localhost:5500`.

### Option 2: Python Static Server
Run in PowerShell / Terminal:
```bash
python -m http.server 8000
```
Open your browser at `http://localhost:8000`.

### Option 3: Node `npx serve`
```bash
npx serve .
```

> **Important Note:** Running through an `http://localhost` origin (rather than `file://`) is recommended so the IBM watsonx Orchestrate webchat widget can initialize without CORS restrictions.

---

## 🤖 Step-by-Step IBM watsonx Orchestrate Setup (Manual)

Follow these exact steps inside your IBM Cloud account to configure the Nutrition Agent:

### Step 1: Provisioning Orchestrate
1. Log in to [IBM Cloud](https://cloud.ibm.com).
2. Create an **IBM watsonx Orchestrate** instance in location **Dallas (us-south)** using the **Trial (Lite)** plan.
3. Launch the Orchestrate environment.

### Step 2: Creating the Agent
1. In the Orchestrate dashboard, click **Create new agent** → **Create from scratch**.
2. **Name:** `Nutrition Agent`
3. **Description:**
   > *AI Agent: a Personalized Nutrition Agent that collects user preferences such as age, food preferences, health and medical conditions, and location (city, state, country), and suggests a suitable diet plan.*
4. **Model:** Select an **IBM Granite** foundation model from the dropdown (e.g. `ibm/granite-13b-chat-v2` or `ibm/granite-3-8b-instruct`).
5. **Agent Style:** Select **ReAct**.
6. **Welcome Message (max 100 chars):**
   > `Hello! I'm your Nutrition Agent powered by IBM watsonx Orchestrate. Let's plan your diet.`
7. **Quick Start Prompts:**
   - `Provide me 1 day diet nutrition plan`
   - `My weight is 45 kg. Give weight gain diet nutrition plan`
   - `My weight is 95 kg. Give weight loss diet nutrition plan`
   - `Why is this food better for me?`

### Step 3: Knowledge Base Upload (RAG Grounding)
Upload the verified knowledge documents provided in the `docs/` folder:
1. `docs/regional_foods.md` (North, South, East, West staples and budget swaps).
2. `docs/condition_guidelines.md` (Conservative, non-diagnostic guidelines for diabetes, PCOS, etc.).
3. *(Optional)* Download ICMR-NIN *Dietary Guidelines for Indians* and IFCT 2017 summary tables.

### Step 4: Behavior Instructions
In **Behavior → Instructions**, paste the comprehensive behavior prompt from Section 6.2 of `PRD_Nutrition_Agent.md`.

### Step 5: Webchat Channel & Embed Snippet
1. Under **Channels**, choose **Webchat** (Embedded Chat).
2. **Channel Security:** For this local demo prototype, **disable security** (allow anonymous connection) to avoid needing JWT tokens on localhost.
3. Copy the generated `<script>` embed code.
4. Open `index.html` in your editor, locate lines 16–18, and paste the script directly into `<head>`:
   ```html
   <!-- ===== WATSONX ORCHESTRATE EMBEDDED CHAT: PASTE SNIPPET BELOW ===== -->
   <script>
     // Paste your snippet here
   </script>
   <!-- ===== END WATSONX ORCHESTRATE SNIPPET ===== -->
   ```
5. Save `index.html` and reload `http://localhost:5500`. The floating chat launcher will appear in the bottom-right corner!

---

## 🧮 Mathematical Engine & Formulas

All health calculations run client-side in `js/calculators.js`:

1. **Body Mass Index (BMI):**
   $$\text{BMI} = \frac{\text{Weight (kg)}}{(\text{Height (m)})^2}$$
   WHO standard categorization: Underweight (<18.5), Normal (18.5–24.9), Overweight (25–29.9), Obese (≥30).

2. **Basal Metabolic Rate (BMR via Mifflin-St Jeor):**
   - **Male:** $10 \times \text{weight} + 6.25 \times \text{height} - 5 \times \text{age} + 5$
   - **Female:** $10 \times \text{weight} + 6.25 \times \text{height} - 5 \times \text{age} - 161$

3. **Total Daily Energy Expenditure (TDEE):**
   $$\text{TDEE} = \text{BMR} \times \text{Activity Factor}$$
   *(Sedentary: 1.2, Light: 1.375, Moderate: 1.55, Active: 1.725, Very Active: 1.9)*

4. **Goal Calorie Floor Guardrail:**
   - Gain: $\text{TDEE} + 400\text{ kcal}$
   - Loss: $\text{TDEE} - 400\text{ kcal}$
   - Floor: Automatically clamps to $\ge 1,200\text{ kcal}$ (females) or $\ge 1,500\text{ kcal}$ (males) with an advisory warning.

5. **Minor Restriction (<18 years):**
   - Suppresses adult calorie deficit targets. Displays a prominent advisory recommending consultation with a pediatrician or registered dietitian.

---

## 🧪 Verification & Acceptance Test Suite

| Test ID | Test Scenario | Verified Behavior | Status |
|---|---|---|---|
| **T1** | Male 24y, 65kg, 167cm, Sedentary | BMR = 1578.8 kcal, TDEE = 1894.5 kcal, BMI = 23.3 (Normal) | ✅ Passed |
| **T2** | Female 30y, 70kg, 160cm, Light Activity | BMR = 1389 kcal, TDEE = 1910 kcal, BMI = 27.3 (Overweight) | ✅ Passed |
| **T3** | Age 15 (Minor handling) | Suppresses adult calorie target; displays pediatric advisory | ✅ Passed |
| **T4** | Extreme deficit trigger | Clamps to 1,200 / 1,500 kcal safe floor with warning message | ✅ Passed |
| **T5** | 1-Click Profile Copy | Formats full profile prompt and copies cleanly to clipboard | ✅ Passed |
| **T6** | Unit Conversion | Seamless toggle between Metric (cm) and Imperial (ft & in) | ✅ Passed |
| **T7** | Responsive Layout | Clean render without horizontal scrolling at 360px, 768px, 1280px | ✅ Passed |

---

## 📽️ Submission Deliverables

### A. 12-Slide PowerPoint Presentation Deck Outline

1. **Slide 1: Title Slide** - NutriMate AI: The Smartest AI Nutrition Assistant (Problem Statement #8: Nutrition Agent). Team Name, IBM SkillsBuild & Edunet Foundation.
2. **Slide 2: Problem Statement** - The dual crisis of lifestyle disorders (diabetes, obesity, PCOS) vs. unscalable, expensive dietary consultations and rigid, generic diet charts.
3. **Slide 3: Target Personas & Gaps** - College students, busy professionals, individuals with lifestyle conditions, and fitness beginners.
4. **Slide 4: Solution Overview** - A zero-backend, privacy-first web assistant with embedded IBM watsonx Orchestrate, IBM Granite, and client-side metabolic tools.
5. **Slide 5: Key Innovations** - Cultural regional grounding (millets, local pulses), explainable "Why" rationales, dynamic swap adaptability.
6. **Slide 6: System Architecture Diagram** - Walkthrough of `assets/architecture.svg` (Static UI $\rightarrow$ Webchat $\rightarrow$ watsonx Orchestrate ReAct $\rightarrow$ Granite LLM + RAG Grounding).
7. **Slide 7: Mandated IBM Technologies** - IBM watsonx Orchestrate, IBM Granite LLM, IBM Cloud Lite Services, (IBM Docling for document ingestion).
8. **Slide 8: Responsible & Ethical AI** - Safety, Transparency, Factual Grounding, Zero PII Privacy, Cultural Fairness, Human-in-the-loop escalation.
9. **Slide 9: Live Demo & Walkthrough** - Screenshots of the interactive dashboard, calculator evaluation, and Orchestrate conversation turns.
10. **Slide 10: Impact & Scalability** - Serving unlimited concurrent users at near-zero marginal cost; expanding regional food knowledge bases across diverse states.
11. **Slide 11: Future Scope** - Speech-to-speech voice interaction, IBM Granite Vision for meal photo analysis, and WhatsApp/Twilio integration.
12. **Slide 12: Conclusion & Credits** - Thank you, acknowledgments to IBM SkillsBuild and Edunet Foundation mentors.

---

### B. 2.5-Minute Demo Video Script

| Time | Scene | Narration / Action |
|---|---|---|
| **0:00 - 0:15** | Hero Section | *"Welcome to NutriMate AI, built for the IBM SkillsBuild internship problem statement #8. We are solving one of healthcare's biggest challenges: making culturally-aware, explainable nutrition accessible to everyone."* |
| **0:15 - 0:45** | Calculators | *Show interactive health calculator: switch between cm and ft/in, select 24y, 65kg, sedentary, vegetarian in Pune. Point out instant BMR, TDEE, and macro split.* |
| **0:45 - 1:00** | Sync Button | *Click "Send My Profile to Agent". Toast appears: "Profile prompt copied to clipboard!"* |
| **1:00 - 1:45** | Orchestrate Chat | *Open embedded watsonx chat. Paste the prompt. Agent greets warmly, acknowledges Pune vegetarian profile, and outputs a 5-meal plan with 2-3 regional alternatives (e.g., matki usal, bajra roti, sattu).* |
| **1:45 - 2:05** | Adaptation & "Why" | *Ask: "Swap lunch, I don't like paneer" $\rightarrow$ Agent swaps only lunch to soya chunks. Ask: "Why is oats better than white bread?" $\rightarrow$ Agent explains fiber and glycemic stabilization.* |
| **2:05 - 2:20** | Architecture & Safety | *Scroll to Architecture visual and Responsible AI table. Highlight zero PII data retention, safe calorie floor, and doctor disclaimers.* |
| **2:20 - 2:30** | Conclusion | *"Powered by IBM watsonx Orchestrate and IBM Granite, NutriMate AI delivers accessible, responsible nutrition intelligence. Thank you!"* |

---

## ⚖️ Legal & Medical Disclaimer

> **Mandatory Disclaimer:** "This tool gives general nutrition information and is not a substitute for professional medical advice. Please consult a doctor or certified nutritionist for medical advice."

