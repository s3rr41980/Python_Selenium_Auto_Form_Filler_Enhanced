Update: 
I have completed the overview page for quick assessment of data quality issues and reconciliation gaps for all 150+ columns (excluding missing PKs from CURRENT) and validated the results.

It provides per column metrics:
- row counts
- null counts
- distinct counts
- count differences
- count of data missing in NEW
- count of data missing in CURRENT
- count of data mismatches
- count of data with leading/trailing white spaces

This should allow teams to quickly identify affected areas and use filters on the data source page or utilise new tabs for further investigation.

I’ll also be able to reuse this template by replacing source data for the remaining 14 files and future reconciliation efforts, which should significantly reduce effort subsequently.

Question:
Identifying cell-level discrepancies row-by-row by PK for all 150 columns would require significant additional effort. In order to reduce manual and subsequent efforts, I’m looking at the possibility of creating a single sheet that produces this result which dynamically changes according to column name selected via drop-down.

Let me know if which you’d like me to prioritise:

1. a detailed PK-level breakdown for specific columns manually for 2 of the most urgently files
2. a reusable sheet that shows the same breakdown, but can adapt via selected column, and can be reused for subsequent/future files


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
