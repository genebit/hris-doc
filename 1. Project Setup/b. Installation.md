# b. Installation

Once everything is installed from [[1. Project Setup/a. Prerequisites|a. Prerequisites]], we can now install and run the application locally.

### Clone the application

```bash
git clone https://mis-git.adnu.edu.ph/misadnu/hris.git
cd hris
```

### Install Composer packages

```bash
composer install
```

### Install Node.js packages

```bash
npm install
```

### Setup environment variables

```bash
cp .env.example .env
# paste the credentials to .env
```

### Initialize project

```bash
php artisan ide:generate
php artisan key:generate
```

### Run the application

```bash
php artisan serve --host localhost
npm run dev
```

---

➡️ Next up!

[[a. Git Conventions]]