# InnovativeSkillsTest (Django Product Management mini app)

This repository is a small Django project where we manage products in two “stages”:

- **Draft products**: products that were uploaded (from an Excel sheet) but not approved yet
- **Approved products**: products that are already approved and can be searched/filtered

The UI is made using simple **HTML templates**, and the backend logic is written in **Python (Django)**.

---

## What the project does (based on the code)

### 1) Landing page
When you open the site, you land on a simple page with two buttons:

- **View Approved Products**
- **Draft & Upload**

This comes from:
- `views.landing()` → renders `templates/landing.html`

Route:
- `/` (home)

---

### 2) Draft & Upload (Excel import + approve/edit)
This is the “work” page of the project.

You can:
- Upload an **.xlsx** file
- Django reads the rows using `openpyxl`
- Each row becomes a `Product` with status **Draft**
- If a product ID already exists, it skips it and shows a message

Then it shows a table of all **Draft** products with:
- **Edit** button (change name/category/price/quantity)
- **Approve** button (moves status from Draft → Approved)

Routes:
- `/draftupload/` → upload + list drafts
- `/approve/<product_id>/` → approve a draft product (POST)
- `/edit/<product_id>/` → edit a draft product (GET/POST)

Main logic is in:
- `practical/views.py` (`draft_upload`, `approve_product`, `edit_product`)

---

### 3) Approved Products (search + filter + pagination)
This page lists only products with status **Approved**.

It supports:
- Search by **name** or **category**
- Filter by **date range** using the product’s `last_updated`
- Choose results per page (5 / 10 / 20)
- Pagination

Route:
- `/approvedproducts/`

Main logic is in:
- `practical/views.py` (`approved_products`)

---

## Data model

There is only one model:

### `Product` (in `practical/models.py`)
Fields:
- `product_id` (AutoField primary key)
- `name` (unique)
- `category`
- `price` (Decimal)
- `quantity` (Integer)
- `last_updated` (auto updated every save)
- `status` (`Draft` or `Approved`)

---

## Project structure (important files)

- `manage.py` — Django command runner
- `practical/settings.py` — settings (SQLite database, templates folder configured)
- `practical/urls.py` — URL routes for the project
- `practical/views.py` — all app logic (upload, approve, edit, filters)
- `practical/models.py` — Product model
- `templates/` — HTML pages:
  - `landing.html`
  - `draftupload.html`
  - `approvedproducts.html`
  - `edit_product.html`

---

## How to run it locally

### 1) Create and activate a virtual environment (recommended)
```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# Mac/Linux:
source .venv/bin/activate
```

### 2) Install dependencies
This repo doesn’t include a `requirements.txt`, but based on the imports you will need at least:

- Django
- openpyxl

Install:
```bash
pip install django openpyxl
```

### 3) Run migrations
```bash
python manage.py makemigrations
python manage.py migrate
```

### 4) Start the server
```bash
python manage.py runserver
```

Then open:
- `http://127.0.0.1:8000/`

---

## Excel file format (what the upload expects)

The code reads from row 2 (so row 1 is header).

It expects the first 5 columns to be:

1. `product_id`
2. `name`
3. `category`
4. `price`
5. `quantity`

Only `.xlsx` is accepted.

---

## Notes / small limitations I noticed

- `ALLOWED_HOSTS = []` so this is meant for local development.
- The repo currently doesn’t have a dependency file (`requirements.txt`), so installation is manual.
- Approving a product is done through a POST request, which is good (it avoids approving by just visiting a link).
