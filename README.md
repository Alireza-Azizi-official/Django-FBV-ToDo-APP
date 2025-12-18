## Todo – Django Function‑Based View App

Minimal, login‑protected todo list built with **Django function‑based views (FBVs)**.
Users can register, log in, and manage a personal list of tasks through a clean, distraction‑free UI.

![Image](C:\Users\Administrator\Desktop\Django-FBV-ToDo-APP\app demo\Screenshot 2025-12-18 175109.png)

---

## Features

- **User accounts**
  - Register, log in, log out.
  - Each user only sees **their own** tasks.
- **Task management**
  - Create new tasks from the main list view.
  - Update existing tasks on a dedicated edit screen.
  - Delete tasks with a single click (with confirmation).
- **Minimal, modern UI**
  - Centered card layout with a small header bar.
  - Simple, compact task list with “Edit” and “Delete” actions.
  - Mobile‑friendly responsive styles (no JS framework needed).

---

## Tech stack

- **Backend**: Django (Function‑Based Views)
- **Database**: SQLite (default Django DB, good for local/dev)
- **Frontend**: Django templates + custom CSS (in `static/style.css`)

---

## Project structure (relevant parts)

```text
core/
  core/
    settings.py        # Django settings (installed apps, templates, static, DB)
    urls.py            # Root URL configuration
  tasks/
    models.py          # Task model
    forms.py           # RegisterForm and TaskForm
    views.py           # All function-based views (auth + tasks)
    urls.py            # App URLs (task list, CRUD, auth)
  templates/
    base.html          # Global layout shell
    login.html         # Login page
    register.html      # Registration page
    task_list.html     # Main task list + create form
    task_update.html   # Edit existing task
  static/
    style.css          # Minimal, modern styling for the whole app
manage.py
README.md
```

---

## Getting started

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd Django-FBV-ToDo-APP/core
```

### 2. Create and activate a virtual environment (optional but recommended)

If you don’t already have one:

```bash
python -m venv venv
venv\Scripts\activate  # On Windows
# source venv/bin/activate  # On macOS / Linux
```

### 3. Install dependencies

If you have a `requirements.txt`, run:

```bash
pip install -r requirements.txt
```

Otherwise, the essentials are:

```bash
pip install "django>=5"
```

### 4. Apply migrations

From the `core` directory (where `manage.py` lives):

```bash
python manage.py migrate
```

### 5. Run the development server

```bash
python manage.py runserver
```

Then open `http://127.0.0.1:8000/` in your browser.

---

## How the app works

### URLs and views

- Root URL configuration (`core/core/urls.py`)
  - Includes `tasks.urls` at the site root:
    - `/` → task list (requires login)

- App URLs (`core/tasks/urls.py`)
  - `/` → `task_list` – show current user’s tasks + inline “add task” form.
  - `/create/` → `task_create` – handle new task POSTs from the list.
  - `/update/<int:pk>/` → `task_update` – edit an existing task.
  - `/delete/<int:pk>/` → `task_delete` – delete an existing task.
  - `/register/` → `register_view` – sign‑up page.
  - `/login/` → `login_view` – sign‑in page.
  - `/logout/` → `logout_view` – POST‑based logout from the header.

All task views are decorated with `@login_required`, so anonymous users are redirected to login.

### Authentication flow

- **Register**
  - `register_view` uses `RegisterForm` (Django `UserCreationForm`‑style).
  - After successful registration, the user is logged in and redirected to the task list.

- **Login**
  - `login_view` authenticates with username + password.
  - If a `?next=/some/url/` param is present, redirects there; otherwise back to the task list.
  - Invalid credentials show a clear error message on the login page.

- **Logout**
  - Triggered via a small form button in the header that posts to `/logout/`.
  - Calls `logout_view`, logs the user out, and redirects to login.

### Task lifecycle

- **Model**
  - The `Task` model (in `tasks/models.py`) is associated with `user`, and includes at least a `title` field.
  - All queries filter by `user=request.user`, ensuring isolation between users.

- **List & create**
  - `task_list`:
    - Fetches tasks owned by the current user.
    - Instantiates a blank `TaskForm` (typically just `title`).
    - Renders `task_list.html`.
  - `task_create`:
    - Accepts POSTs from the inline form.
    - Binds the task to `request.user` and saves.
    - Redirects back to `task_list`.

- **Update**
  - `task_update`:
    - Uses `get_object_or_404(Task, pk=pk, user=request.user)` to ensure users can only edit their own tasks.
    - On GET: shows `task_update.html` with the `TaskForm` bound to the instance.
    - On POST: validates and saves, then redirects to `task_list`.

- **Delete**
  - `task_delete`:
    - Similar ownership check with `get_object_or_404`.
    - Deletes the task and redirects to `task_list`.
    - The UI adds a small JavaScript `confirm()` prompt.

---

## Frontend & styling

- **Base layout (`base.html`)**
  - Minimal header with:
    - App name (“Todo”) and small brand mark.
    - Auth‑aware nav:
      - When logged in: “Hi, username”, “Tasks”, and a small logout button.
      - When logged out: “Login” and “Register” buttons.
  - Centered `<main>` card that holds whatever content each page provides.

- **Templates**
  - `login.html`:
    - Compact login form with username and password fields.
    - Inline error messages using Django’s messages framework.
  - `register.html`:
    - Loops over form fields with labels and inline field errors.
    - Shows a link back to the login page.
  - `task_list.html`:
    - Top section shows “Today” and the number of tasks.
    - Inline “add task” row: text input + “Add” button.
    - List of pill‑shaped task items each with “Edit” and “Delete” buttons.
  - `task_update.html`:
    - Simple “Edit task” page with a single text field and Save button.

- **CSS (`static/style.css`)**
  - Defines a small design system: colors, radii, shadows, buttons, and layout helpers.
  - Focuses on a minimal, dark UI with good spacing and readable text.
  - Includes responsive tweaks for smaller screens.

---

## Running tests (if/when added)

From the `core` directory:

```bash
python manage.py test
```

---

## Customization ideas

- Add completion status (checkbox to mark done / not done).
- Add due dates or tags and filter tasks by those.
- Add pagination or sections like “Today”, “Upcoming”, etc.
- Switch to a different database (PostgreSQL, MySQL) for production use.

---

## License

See `LICENSE` in the project root.

