# 🛡️ Express.js + Passport — Серверна аутентифікація

Повноцінний сервер на Express.js із SSR (Pug + EJS), авторизацією через Passport, сесіями, middleware, CORS, перемиканням теми та структурованими маршрутами,без використання бази даних..
Дані користувачів зберігаються у пам'яті (масив), тому після перезапуску сервера всі користувачі видаляються.

## ✅ Реалізоване

### 📦 Технології

- Node.js + Express (5.x)
- Passport (локальна стратегія) (реалізовано через server.js)
- Сесії через express-session
- Шаблонізатори: Pug (автентифікація, профілі) та EJS (товари ( в розробці))
- CORS + cookie-parser
- JWT utility (не використовується у цьому флоу)

## 📁 Структура проєкту

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

## 🔐 Авторизація через Passport (реалізовано через server.js)

- Локальна стратегія (email + password)
- Passport session + express-session
- Сесії з cookie (httpOnly, secure)
- Серіалізація/десеріалізація:

## ⚙️ Запуск

### Встановіть залежності:

```bash
cd server
npm install
```

### Запустіть сервер:

```bash
npm start        # або
npm run dev      # з nodemon
```

## Відкрийте у браузері:

http://localhost:5000/users/auth/register  
http://localhost:5000/users/auth/login
http://localhost:5000/users/auth/protected
