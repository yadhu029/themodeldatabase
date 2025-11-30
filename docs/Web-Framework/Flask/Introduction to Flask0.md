## **What Is a Web Framework?**

A **web framework** is a software architecture that provides tools and libraries needed to build a web application. It simplifies tasks such as handling requests, routing URLs, rendering templates, and managing data.

## **What Is Flask?**

**Flask** is a _micro-framework_ for **Python**. It is called a micro-framework because, unlike full-fledged frameworks such as **Django**, it does **not** include built-in features like authentication, admin panels, or ORM.  
Instead, Flask keeps things **minimal, flexible, and modular**, allowing developers to add only the components they need.

Flask is built on:

- **Werkzeug** – A WSGI toolkit that handles request and response objects.
    
- **Jinja2** – A templating engine used for rendering HTML with Python variables.
    

## **Setting Up the Flask Environment**

Before starting, ensure **Python 3** is installed.

### **1. Create a virtual environment**

```bash
python3 -m venv env
```

### **2. Activate the virtual environment**

```bash
source env/bin/activate
```

### **3. Install Flask**

```bash
pip install flask
```

That’s all you need to create your first Flask application.

---

## **Your First Flask App**

Create a new Python file and add:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def index():
    return "hello, world"
```

### **Run the application**

```bash
flask run
```

---

# **Understanding the Code**

### **`from flask import Flask`**

You import the **Flask class** from the `flask` package. This gives your script access to the essential components needed to create a web application.

---

### **`app = Flask(__name__)`**

- `__name__` is a _special built-in Python variable_.
    
- It is also called a **dunder** (double-underscore) variable.
    
- Flask uses `__name__` to determine:
    
    - the root path of your application
        
    - where to find templates and static files
        
    - the execution context for routing and configuration
        

This line creates the **Flask application instance**, stored in the variable `app`.

---

### **`@app.route("/")` – The Route Decorator**

`app.route()` is a **decorator** that maps a URL path to a function.  
Here, the root URL `/` is associated with the `index()` function.

Whenever someone visits your home page (`http://127.0.0.1:5000/`), Flask runs the function below the decorator.

---

### **`def index():`**

Defines the function to run when the `/` route is accessed.

---

### **`return "hello, world"`**

Whatever this function returns becomes the **response** shown in the browser.

In this case, the text:

```
hello, world
```

---

## **What Happens When You Run the App?**

1. `flask run` starts the built-in Flask development server.
    
2. The default URL is:  
    `http://127.0.0.1:5000/`
    
3. When you visit `/`, Flask:
    
    - matches the route
        
    - calls the `index()` function
        
    - displays **hello, world** in the browser
        

