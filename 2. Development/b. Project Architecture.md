# b. Project Architecture

The project follows a Model-View-Controller architecture with Livewire acting both as a Frontend state management while also injecting backend services e.g., dependency injection for queries/repositories.

![[Project Architecture.png]]

In this approach, we leverage Livewire's power by reducing the writing of JS server requests. 

### 📁 Folder Structure

```
├── app
    ├── Console
    ├── Contracts
    ├── Enums
    │   ├── ErrorMessages.php
    │   ├── UserPrivilege.php
    │   └── UserRole.php
    ├── Exceptions
    ├── Exports
    ├── Helpers
    │   ├── CRUD.php
    │   └── Utils.php
    ├── Http
    │   ├── Controllers
    │   │   ├── API
    │   │   │   └── v1
    │   │   ├── Controller.php
    │   │   └── Web
    │   │   │   ├── AUTH
    │   │   │   ├── FACSYS
    │   │   │   ├── HR
    │   │   │   ├── LEAVESYS
    │   │   │   └── TIMESYS
    │   ├── Kernel.php
    │   ├── Middleware
    │   └── Requests
    ├── Livewire
    │   ├── Components
    │   ├── Pages
    └── View
		├── Components
		└── Layouts
	├── Models
	├── Providers
	└── Repositories
├── artisan
├── config
├── database
├── public
├── resources
    ├── css
    │   ├── app.css
    │   └── import
    ├── js
    └── views
    │   ├── components
    │   ├── errors
    │   ├── includes
    │       ├── scripts
    │       └── styles
    │   ├── layouts
    │   └── modules
    │       ├── auth
    │       ├── hr
    │       ├── sys
    │           ├── facsys
    │           ├── leavesys
    │           └── timesys
    │       └── test
    │           ├── playground
├── routes
```

### Backend Workflow

The following directories are the most important and frequently modified during backend feature development:

| Path                     | Purpose                                                                 |
|--------------------------|-------------------------------------------------------------------------|
| `app/Contracts`          | Define **interfaces** for repositories                                 |
| `app/Repositories`       | Implement repository **classes**                                       |
| `app/Providers`          | **Bind** interfaces to implementations                                 |
| `app/Http/Controllers`   | Define **HTTP controllers**, connected with `Requests` and `Middleware` |
| `app/Models`             | Define **entities** and implement CRUD operations                      |

### Frontend Workflow

For frontend development, we use the following structure:

| Path                      | Purpose                                      |
| ------------------------- | -------------------------------------------- |
| `routes/web.php`          | Define **application routes**                |
| `resources/css`           | Application **global CSS**                   |
| `resources/js`            | Application **global JavaScript**            |
| `resources/views/modules` | Create **Blade pages**, separated by modules |
| `app/Livewire`            | Create **Livewire PHP components**           |
| `app/View`                | Create **Laravel Blade components**          |

### Project Scoping

When working with the project, the project is designed to follow a hierarchy to clearly see which components/files are used within the directory. To maintain a clear structure we follow:

```
└── modules
    ├── auth
    │   ├── components
    │   │   └── scroll-top-btn.blade.php
    │   ├── livewire
    │   │   ├── partials
    │   │   ├── sidebar-oc-content.blade.php
    │   └── index.blade.php
    ├── hr
```

In this example we have an authentication page. We create folders for Laravel components and Livewire components. When we designate files, we read them as: 

> The `scroll-top-btn.blade.php` can be assumed that it will be called within `livewire/*` or even in `index.blade.php` **BUT** will never be used outside of `auth/`. 

If there's a component under partials then we assume that the component is only used within that folder. 

This pattern is used all throughout the project.

### Keynotes

1. Repositories act as a query fetcher. Writing of queries are written here.
2. Models implements CRUD operations and calls the packages written in PL/SQL from Oracle.

---

➡️ Next up!

[[c. Code Conventions]]