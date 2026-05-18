# 📝 Django Auth CRUD — Task Manager

A full-stack web application for task management built with **Django 5.2.5**. It includes user authentication (sign up, sign in, sign out) and complete CRUD operations for personal tasks. Designed for deployment on **Render** with **PostgreSQL** and static file serving via **WhiteNoise**.

🌍 **Live Demo:** [https://django-auth-crud-1nsi.onrender.com](https://django-auth-crud-1nsi.onrender.com)

---

## 🚀 Features

- 🔐 User registration and authentication (sign up / sign in / sign out)
- ✅ Create, view, edit, and delete personal tasks
- 📌 Mark tasks as important
- 🏁 Mark tasks as completed (with timestamp)
- 📋 View pending and completed tasks separately
- 🔒 All task views protected with `@login_required`
- ☁️ Ready for production deployment on Render

---

## 🛠️ Tech Stack

| Layer       | Technology                        |
|-------------|-----------------------------------|
| Backend     | Django 5.2.5                      |
| Database    | PostgreSQL (via `dj-database-url`) |
| Auth        | Django built-in auth system       |
| Static files| WhiteNoise 6.9                    |
| Server      | Gunicorn 23.0                     |
| Deployment  | Render                            |

---

## 📁 Project Structure

```
djangocrud/
├── djangocrud/
│   ├── settings.py       # Project settings (env-aware)
│   ├── urls.py           # Root URL configuration
│   └── wsgi.py
├── tasks/
│   ├── models.py         # Task model
│   ├── views.py          # All views (auth + CRUD)
│   ├── forms.py          # TaskForm
│   └── templates/        # HTML templates
│       ├── home.html
│       ├── signup.html
│       ├── signin.html
│       ├── tasks.html
│       ├── create_task.html
│       └── task_detail.html
├── templates/            # Global templates directory
├── requirements.txt
└── manage.py
```

---

## ⚙️ Local Setup

### 1. Clone the repository

```bash
git clone https://github.com/Johmmer/django-auth-crud.git
cd django-auth-crud
```

### 2. Create and activate a virtual environment

```bash
# Linux / macOS
python -m venv venv
source venv/bin/activate

# Windows
python -m venv venv
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file in the root directory (or set variables in your shell):

```env
SECRET_KEY=your-secret-key-here
DATABASE_URL=postgres://user:password@localhost:5432/your_db_name
```

> **Note:** If `RENDER` is not set in the environment, `DEBUG` will be `True` automatically (local development mode).

### 5. Apply migrations

```bash
python manage.py migrate
```

### 6. Create a superuser (optional)

```bash
python manage.py createsuperuser
```

### 7. Run the development server

```bash
python manage.py runserver
```

Visit: [http://localhost:8000](http://localhost:8000)

---

## 🌐 URL Routes

| URL                              | View               | Description                    |
|----------------------------------|--------------------|--------------------------------|
| `/`                              | `home`             | Landing page                   |
| `/signup/`                       | `signup`           | User registration               |
| `/signin/`                       | `signin`           | User login                     |
| `/logout/`                       | `signout`          | User logout                    |
| `/tasks/`                        | `tasks`            | List of pending tasks          |
| `/task/completed`                | `completed_tasks`  | List of completed tasks        |
| `/tasks/create`                  | `create_task`      | Create a new task              |
| `/task/<id>`                     | `task_detail`      | View / edit a task             |
| `/tasks/<id>/complete`           | `complete_task`    | Mark a task as complete        |
| `/tasks/<id>/delete`             | `delete_task`      | Delete a task                  |
| `/admin/`                        | Django Admin       | Admin panel                    |

---

## 🗄️ Data Model

### `Task`

| Field           | Type            | Description                          |
|----------------|-----------------|--------------------------------------|
| `title`         | CharField(50)   | Task title (required)               |
| `description`   | TextField       | Task description (optional)         |
| `created`       | DateTimeField   | Auto-set on creation                |
| `datecompleted` | DateTimeField   | Set when task is marked complete    |
| `important`     | BooleanField    | Flag for important tasks            |
| `user`          | ForeignKey(User)| Owner of the task (cascades on delete) |

---

## ☁️ Deployment on Render

### Environment variables required on Render:

| Variable                | Description                              |
|-------------------------|------------------------------------------|
| `SECRET_KEY`            | Django secret key                        |
| `DATABASE_URL`          | PostgreSQL connection string from Render |
| `RENDER`                | Set to any value to disable DEBUG        |
| `RENDER_EXTERNAL_HOSTNAME` | Auto-set by Render for `ALLOWED_HOSTS` |

### Build command:

```bash
pip install -r requirements.txt && python manage.py migrate
```

### Start command:

```bash
gunicorn djangocrud.wsgi:application
```

---

## ⚠️ Known Issues & Recommendations

### Security
- **`SECRET_KEY` fallback:** The default `'your secret key'` in `settings.py` should never reach production. Ensure the `SECRET_KEY` environment variable is always set.
- **`task_detail` (POST):** The update view does not verify `user=request.user` when fetching the task, meaning a logged-in user could potentially edit another user's task. Consider changing to:
  ```python
  task = get_object_or_404(Task, pk=task_id, user=request.user)
  ```
- **Bare `except` in `task_detail`:** Replace with specific exception types (e.g., `except ValueError`) to avoid silently swallowing unexpected errors.

### Configuration
- **`RENDER_EXTERNAL_HOSTNAME`** is read twice in `settings.py`. The duplicate block can be removed safely.
- **`dj_database_url.parse()`** will raise an error if `DATABASE_URL` is not set (e.g., in fresh local setups). Consider adding a fallback for local SQLite:
  ```python
  DATABASES = {
      "default": dj_database_url.parse(
          os.environ.get('DATABASE_URL', 'sqlite:///db.sqlite3')
      )
  }
  ```

---

## 📦 Dependencies

```
Django==5.2.5
dj-database-url==3.0.1
gunicorn==23.0.0
psycopg2-binary==2.9.10
whitenoise==6.9.0
Brotli==1.1.0
asgiref==3.9.1
sqlparse==0.5.3
packaging==25.0
```

---

## 📄 License

This project is open source. Feel free to use it as a learning reference or starting point for your own Django projects.

---

## 👤 Author

**Johmmer**  
GitHub: [@Johmmer](https://github.com/Johmmer)
