# 🛡️ Express.js + Passport — Серверна аутентифікація

Повноцінний сервер на Express.js із SSR (Pug + EJS), авторизацією через Passport, сесіями, middleware, CORS, перемиканням теми та структурованими маршрутами,cookie та MongoDB Atlas.
Дані користувачів зберігаються у пам'яті (масив), тому після перезапуску сервера всі користувачі видаляються.

## ✅ Нове у цій версії

🔌 **Підключено MongoDB Atlas** (через `mongoose`) для зберігання та отримання товарів.

📦 **Читання даних з колекції `products`** з підтримкою категоризації (`categorySlug`) та динамічних slug для товарів.

🧭 **SSR-маршрути** (серверна маршрутизація Express + EJS):

- `/products/db`  
  🔹 Повний список усіх товарів без фільтрації (технічна сторінка).

- `/products/categories`  
  🔹 Список унікальних категорій товарів. Групування за `categorySlug`, зображення категорії береться з першого товару в ній.

- `/products/category/:slug`  
  🔹 Вивід усіх товарів, які належать до певної категорії (використовується `categorySlug`).

- `/products/:categorySlug/:productSlug`  
  🔹 Сторінка одного товару. SEO-дружній шлях з повною URL-структурою.

🧩 **Створено шаблони EJS**:

- `views/ejs/products/categories.ejs` — сторінка з усіма категоріями.
- `views/ejs/products/category.ejs` — вивід товарів конкретної категорії.
- `views/ejs/products/show.ejs` — детальна сторінка одного товару.
- `views/ejs/products/index.ejs` — загальний технічний список товарів.

📁 **Оновлена структура проєкту**:

- SSR та REST API використовують спільну модель `Product`.
- Публічні зображення товарів обслуговуються з `public/uploads` через:
  ```js
  app.use("/uploads", express.static(path.join(__dirname, "public/uploads")));
  ```

---

### 📦 Технології

- Node.js + Express (5.x)
- Passport (локальна стратегія) (реалізовано через server.js)
- Сесії через express-session
- Шаблонізатори: Pug (автентифікація, профілі) та EJS (товари ( в розробці))
- CORS + cookie-parser
- JWT utility (використовується для API-запитів)
- Nodemailer (відновлення пароля)
- Тема сайту: світла / темна (через cookie)
- MongoDB Atlas + Mongoose

## 📁 Структура проєкту

````
server/
├── public/
│ ├── images/
│ │ └── picLogo.png
│ ├── scripts/
│ │ └── theme.js
│ ├── favicon.ico
│ └── style.css
├── src/
│ ├── config/
│ │ └── jwtUtils.js
│ ├── controllers/
│ │ ├── views/
│ │ │ ├── ejs/
│ │ │ │ ├── partials/
│ │ │ │ │ ├── footer.ejs
│ │ │ │ │ └── header.ejs
│ │ │ │ └── products/
│ │ │ │ ├── index.ejs
│ │ │ │ └── show.ejs
│ │ │ └── pug/
│ │ │ ├── auth/
│ │ │ │ ├── login.pug
│ │ │ │ ├── protected.pug
│ │ │ │ └── register.pug
│ │ │ ├── layouts/
│ │ │ │ └── base.pug
│ │ │ └── users/
│ │ │ ├── index.pug
│ │ │ └── show.pug
│ │ ├── productController.js
│ │ └── userController.js
│ ├── middleware/
│ │ ├── adminMiddleware.js
│ │ ├── authMiddleware.js
│ │ ├── errorHandlerMiddleware.js
│ │ ├── logRequestsMiddleware.js
│ │ └── validationUserInput.js
│ ├── models/
│ └── routes/
│ ├── productRoutes.js
│ ├── themeRoutes.js
│ └── userRoutes.js
├── .env
├── package-lock.json
├── package.json
└── server.js
```


## 🔐 Авторизація через Passport (реалізовано через server.js)

- Локальна стратегія (email + password)
- Passport session + express-session
- Сесії з cookie (httpOnly, secure)
- Серіалізація/десеріалізація:

### Серіалізація / десеріалізація

Passport зберігає ID користувача у cookie при вході (`serializeUser`), а потім відновлює об'єкт користувача з пам’яті (`deserializeUser`):

```js
passport.serializeUser((user, done) => done(null, user.id));
passport.deserializeUser((id, done) => {
  const user = dummyUsers.find(u => u.id === Number(id));
  done(null, user || false);
});
```

---

## 🧩 Реалізована функціональність

### 🔐 Авторизація та автентифікація

- SSR-реєстрація, вхід, захищені сторінки
- API-реєстрація, вхід, захищені запити через JWT
- Сесії через cookie та Passport
- Logout (очищення токенів або сесій)

### 👤 Користувачі

- SSR: список, перегляд, захищена сторінка
- API:
  - `GET /api/profile` — отримання поточного профілю
  - `PUT /api/profile` — оновлення профілю користувача (імʼя, email, пароль)
  - `POST /api/forgot-password` — генерація нового пароля та надсилання на email
- Middleware:
  - `protect` — перевірка автентифікації через сесію
  - `jwtProtect` — перевірка JWT токена (для API-запитів)

### 🛒 Товари

- SSR: `/products`, `/products/:id`
- API:
  - `GET /products/api`
  - `GET /products/api/:id`
  - `POST /products/api` (admin only)
  - `PUT /products/api/:id` (admin only)
  - `DELETE /products/api/:id` (admin only)

### 🛡️ Адмін доступ

- Middleware `adminOnly` обмежує CRUD по товарах
- Користувач позначається як `isAdmin: true`

### 🧪 Відновлення пароля

- `POST /api/forgot-password`
- Новий пароль надсилається на email
- Вся SMTP-конфігурація — через `.env`

### 🎨 Теми

- Збереження теми (light/dark) у cookie
- Рендеринг SSR з темою через `res.locals.theme`

### 🧾 Middleware

- `logRequestsMiddleware` — логування запитів
- `errorHandlerMiddleware` — глобальний обробник помилок
- `validationUserInput.js` — базова валідація введення

---

# 📧 Email Configuration (Nodemailer)

Функція `/api/forgot-password` використовує Nodemailer для надсилання листів із новими паролями. Для цього потрібно вказати SMTP-налаштування у `.env`:

### 🔐 Приклад server/.env

```
EMAIL_SERVICE=gmail
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_app_password
NODE_ENV=development
```

### 📌 Пояснення змінних

| Змінна          | Опис                                                              |
|------------------|-------------------------------------------------------------------|
| `EMAIL_SERVICE`  | Поштова служба (`gmail`, `yahoo`, або SMTP-хост)                 |
| `EMAIL_USER`     | Email, з якого будуть надсилатися листи                          |
| `EMAIL_PASS`     | App Password або SMTP пароль (не звичайний пароль!)              |
| `NODE_ENV`       | Має бути `production`, щоб активувати secure cookie              |

### ⚠️ Gmail users

1. Активуйте [2FA](https://myaccount.google.com/security).
2. Створіть [App Password](https://myaccount.google.com/apppasswords).
3. Вставте в `.env` у вигляді `EMAIL_PASS=...`.

### 🧪 Тестування

```http
POST /api/forgot-password
Content-Type: application/json

{
  "email": "user@example.com"
}
```

Користувач отримає новий тимчасовий пароль на пошту.

## 🌐 MongoDB Atlas Integration

Сервер підключено до MongoDB Atlas через бібліотеку Mongoose. Дані зберігаються у колекції `products`.
MONGO_URI=mongodb+srv://USERNAME:PASSWORD@cluster.mongodb.net/dbname



### 🔌 Підключення

Створіть `.env` файл і додайте:


## ⚙️ Запуск

### Встановіть залежності:

```bash
cd server
npm install
````

### Запустіть сервер:

```bash
npm start        # або
npm run dev      # з nodemon
```

## Відкрийте у браузері:

🧪 Тестові посилання
// ==============================================
// 📦 Опис маршрутизації для сторінок продуктів
// ==============================================

// http://localhost:5000/products/db
// 🔹 Вивід усіх товарів напряму з MongoDB без фільтрації (адмінська/технічна сторінка)
router.get('/db', renderAllProductsFromDB);

// http://localhost:5000/products/categories
// 🔹 Сторінка з оглядом усіх категорій товарів (згруповано за categorySlug)
router.get('/categories', renderCategoryList);

// http://localhost:5000/products/category/:slug
// 🔹 Вивід товарів, які належать до певної категорії (за slug категорії)
// Наприклад: [/products/category/3d-pryntery-fdm](http://localhost:5000/products/category/3d-pryntery-fdm)
router.get('/category/:slug', renderProductsByCategory);

// http://localhost:5000/products/:categorySlug/:productSlug
// 🔹 SEO-дружній маршрут: сторінка конкретного товару за категорією та slug
// Наприклад:[ /products/3d-pryntery-fdm/3d-prynter-klema-180](http://localhost:5000/products/3d-pryntery-fdm/3d-prynter-klema-180)
router.get('/:categorySlug/:productSlug', renderProductBySlug);
