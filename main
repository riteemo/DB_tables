const express = require('express');
const mysql2 = require('mysql2/promise');

const pool = mysql2.createPool({
    port: '3307',
    host: 'localhost',
    user: 'root',
    database: 'db_vacations',
    password: '',
});

const app = express();
app.use(express.urlencoded({ extended: true }));

// Главная страница с формами и данными
app.get('/', async (req, res) => {
    try {
        const [vacations] = await pool.query('SELECT * FROM vacation_conditions');
        const [posts] = await pool.query('SELECT * FROM posts');
        const [employees] = await pool.query('SELECT * FROM employees');
        const [orders] = await pool.query('SELECT * FROM vacation_order');

        res.send(`
            <!DOCTYPE html>
            <html>
            <head>
                <meta charset="UTF-8">
                <title>Управление отпусками</title>
                <style>
                    body { font-family: Arial; margin: 20px; }
                    table { border-collapse: collapse; margin: 15px 0; }
                    td, th { border: 1px solid #ccc; padding: 5px 10px; }
                    form { margin: 15px 0; padding: 10px; background: #f5f5f5; }
                    input { margin: 5px 0; }
                </style>
            </head>
            <body>
                <h1>Управление отпусками</h1>

                <h2>Добавить данные</h2>

                <form method="post" action="/add-vacation">
                    <h3>Тип отпуска</h3>
                    Тип: <input type="text" name="type" required><br>
                    Оплачиваемый: <select name="pay"><option value="1">Да</option><option value="0">Нет</option></select><br>
                    Льготы: <input type="text" name="benefits"><br>
                    <input type="submit" value="Добавить">
                </form>

                <form method="post" action="/add-post">
                    <h3>Должность</h3>
                    Название: <input type="text" name="name" required><br>
                    Подразделение: <input type="text" name="devision"><br>
                    <input type="submit" value="Добавить">
                </form>

                <form method="post" action="/add-employee">
                    <h3>Сотрудник</h3>
                    Фамилия: <input type="text" name="surname" required><br>
                    Имя: <input type="text" name="name" required><br>
                    Отчество: <input type="text" name="patronymic"><br>
                    Код должности: <input type="number" name="post" required><br>
                    Дата приема: <input type="date" name="date" required><br>
                    <input type="submit" value="Добавить">
                </form>

                <form method="post" action="/add-order">
                    <h3>Приказ на отпуск</h3>
                    Номер: <input type="number" name="number" required><br>
                    Дата регистрации: <input type="date" name="reg_date" required><br>
                    Начало: <input type="date" name="start" required><br>
                    Конец: <input type="date" name="end" required><br>
                    Код сотрудника: <input type="number" name="emp" required><br>
                    Код отпуска: <input type="number" name="vac" required><br>
                    <input type="submit" value="Добавить">
                </form>

                <h2>Типы отпусков</h2>
                <table>
                    <tr><th>Код</th><th>Тип</th><th>Оплата</th><th>Льготы</th></tr>
                    ${vacations.map(v => `
                        <tr>
                            <td>${v.Code_vacation}</td>
                            <td>${v.Type_vacation}</td>
                            <td>${v.Vacation_pay ? 'Да' : 'Нет'}</td>
                            <td>${v.Vacation_benefits || '-'}</td>
                        </tr>
                    `).join('')}
                </table>

                <h2>Должности</h2>
                <table>
                    <tr><th>Код</th><th>Название</th><th>Подразделение</th></tr>
                    ${posts.map(p => `
                        <tr>
                            <td>${p.Code_post}</td>
                            <td>${p.Post_name}</td>
                            <td>${p.Devision || '-'}</td>
                        </tr>
                    `).join('')}
                </table>

                <h2>Сотрудники</h2>
                <table>
                    <tr><th>Код</th><th>ФИО</th><th>Должность</th><th>Дата приема</th></tr>
                    ${employees.map(e => `
                        <tr>
                            <td>${e.Code_employee}</td>
                            <td>${e.Surname_employee} ${e.Name_employee} ${e.Patronymic_employee || ''}</td>
                            <td>${e.Code_post}</td>
                            <td>${e.Date_of_employment.toISOString().split('T')[0]}</td>
                        </tr>
                    `).join('')}
                </table>

                <h2>Приказы</h2>
                <table>
                    <tr><th>Код</th><th>Номер</th><th>Дата</th><th>Начало</th><th>Конец</th><th>Сотрудник</th><th>Тип</th></tr>
                    ${orders.map(o => `
                        <tr>
                            <td>${o.Code_document}</td>
                            <td>${o.Number_document}</td>
                            <td>${o.Registration_date.toISOString().split('T')[0]}</td>
                            <td>${o.Vacation_start_date.toISOString().split('T')[0]}</td>
                            <td>${o.Vacation_end_date.toISOString().split('T')[0]}</td>
                            <td>${o.Code_employee}</td>
                            <td>${o.Code_vacation}</td>
                        </tr>
                    `).join('')}
                </table>
            </body>
            </html>
        `);
    } catch (err) {
        res.send('Ошибка: ' + err.message);
    }
});

// Обработчики форм
app.post('/add-vacation', async (req, res) => {
    try {
        await pool.query(
            'INSERT INTO vacation_conditions (Type_vacation, Vacation_pay, Vacation_benefits) VALUES (?, ?, ?)',
            [req.body.type, req.body.pay, req.body.benefits]
        );
        res.redirect('/');
    } catch (err) {
        res.send('Ошибка: ' + err.message);
    }
});

app.post('/add-post', async (req, res) => {
    try {
        await pool.query(
            'INSERT INTO posts (Post_name, Devision) VALUES (?, ?)',
            [req.body.name, req.body.devision]
        );
        res.redirect('/');
    } catch (err) {
        res.send('Ошибка: ' + err.message);
    }
});

app.post('/add-employee', async (req, res) => {
    try {
        await pool.query(
            'INSERT INTO employees (Surname_employee, Name_employee, Patronymic_employee, Code_post, Date_of_employment) VALUES (?, ?, ?, ?, ?)',
            [req.body.surname, req.body.name, req.body.patronymic, req.body.post, req.body.date]
        );
        res.redirect('/');
    } catch (err) {
        res.send('Ошибка: ' + err.message);
    }
});

app.post('/add-order', async (req, res) => {
    try {
        await pool.query(
            'INSERT INTO vacation_order (Number_document, Registration_date, Vacation_start_date, Vacation_end_date, Code_employee, Code_vacation) VALUES (?, ?, ?, ?, ?, ?)',
            [req.body.number, req.body.reg_date, req.body.start, req.body.end, req.body.emp, req.body.vac]
        );
        res.redirect('/');
    } catch (err) {
        res.send('Ошибка: ' + err.message);
    }
});

app.listen(3000, () => console.log('Сервер запущен на http://localhost:3000'));
