# 📝 Auto Form Filler (Enhanced)

Week 3 Project for **Python 2 (Intermediate Module)**  

The original requirement was to **add two lines of information** into a Google Form using Selenium.  
Built an **enhanced end-to-end pipeline** that **generates synthetic records**, serves them via a **local REST API**, **validates & cleans** the data, **submits full forms** with Selenium, and **optionally stores** cleaned results in **PostgreSQL**.

---

## 📌 Overview
**Auto Form Filler (Enhanced)** is a Python workflow that:
1) creates realistic synthetic user records (including NRIC),  
2) exposes them via a local **Flask** API and simple HTML table,  
3) **validates** (NRIC / email / phone) and **cleans** the records,  
4) **submits** cleaned fields to a **Google Form** using **Selenium WebDriver**, and  
5) **persists** cleaned outputs to **PostgreSQL**. Also measures total run time for performance insight.

🎯 **Goal:** Move beyond a two-field demo into a **repeatable, automated, and validated** web-form submission flow with realistic data.

---

## 🛠 Tech Stack
- **Python:** Selenium, Requests, Faker, NumPy  
- **Web/API:** Flask (HTML view + `/api/data` JSON endpoint)  
- **Automation:** Chrome WebDriver (Selenium)  
- **Data Quality:** Custom validators (NRIC, email, phone) + cleaning functions  
- **Database (Optional):** PostgreSQL + `psycopg2`  
- **Runtime:** Jupyter Notebook (IPython), `threading`, `nest_asyncio`

---

## 🔄 Workflow
1. **Generate synthetic records**  
   - `generate_jde_records(n)` builds rows: `[NRIC, Name, Email, Address, Phone, Comments]` using **Faker** (+ custom NRIC generation).
2. **Serve data locally**  
   - A **Flask** app renders a table at `http://127.0.0.1:5001/` and a JSON API at `http://127.0.0.1:5001/api/data`.
3. **Extract**  
   - `requests.get("/api/data")` pulls records into `extracted_data` (list of lists).
4. **Validate & clean**  
   - `validate_nric`, `validate_email`, `validate_phone` + `clean_data` → returns `(valid_clean_data, invalid_unclean_data)`.  
   - A privacy-conscious list `valid_data_without_nric` is derived for form submission.
5. **Automate Google Form**  
   - `fill_form_and_submit(record)` navigates to your form URL, matches **field labels** (e.g., “Name”, “Email”, “Address”, “Phone number”, “Comments”) and fills values.  
   - Loops all valid records and submits each form; prints progress and total **elapsed time**.
6. **Persist (optional)**  
   - Creates table `jde_users (nric, name, email, address, phone, comment)` and inserts **cleaned** rows with `psycopg2`.

---

## 🌟 Key Features
- **From two lines ➝ full form:** Automatically fills multiple fields per record.  
- **Local API for repeatability:** Consistent data source for extraction and testing.  
- **Validation & cleaning:** NRIC checksum rules, email format check, numeric phone length, trimming, case normalisation, comment length cap.  
- **Privacy step:** Removes NRIC before submission (`valid_data_without_nric`) whilst still allowing database storage of the cleaned full record.  
- **Database integration:** Optional PostgreSQL storage for downstream analytics or audit.  
- **Resilient selectors:** Locates inputs by question **labels** (works across text/email/tel/textarea widgets).

Evolving Mapping Sheet
1. Unnotified ad-hoc changes - Mapping requirements that impact 50 over tables remain fluid even when close to going live, leading to significant "back-and-forth" cycles, rework and repeated validation cycles. The uncertainty on core business logic creates a dependency bottleneck, preventing WS3 from reaching a "Definition of Done" and increasing downstream logic risks.
2. Absence of Change Governance & Version Control - For most part, not sure what was changed, when was it changed. This forces WS3 into time-consuming investigative work just to determine if an issue is a genuine code defect or a silent requirement change.
3. Cross-Document Contradictions - Contradictions between mapping sheets, summary sheets, and expected business logic created uncertainty on which implementation path should be followed, forcing developers to pause and guess the correct implementation path.

Requirement Integrity and Scope Creep
1. "Day 2" Requests logged as Defects - Observed JIRA ticket being raised as "Defects" that, upon investigation, were Day 2 changes, leading unnecessary investigation time
2. Scope Creep in Disguise  - Several issues filed as defects are often change requests or clarifications. This shifts the burden of requirement discovery and triage onto WS3, consuming critical engineering bandwidth


Ambiguity Tax and Cognitive Load
1. Under-Specified Requirements - Incomplete, fragmented statements and information mismatches result in a high cognitive load. Significant development time is spent investigating, validating, and clarifying across reports to compensate for these upstream gaps
2. Complex Dependency Tracing (SNG, GFT) - Resolving a single JIRA frequently requires tracing logic across multiple dependency tables and disparate environments, substantially increasing cognitive load and implementation/review effort.


Potential Improvement Areas:
1. Stronger mapping version control / change tracking
2. Clearer ownership and sign-off on contested business logic before tickets are assigned to development
3. More standardised Jira requirement formatting template
4. Use clearer verbiage, eliminate ambiguous phrasing to reduce guesswork which in return lower developer cognitive load, accelerating execution velocity
5. Implement cut-off date for new requirements to allow adequate time for stabilisation
