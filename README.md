
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Соцсеть: неограниченные сообщения</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }
        body {
            background: #f0f2f5;
            color: #1c1e21;
        }
        .container {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
        }
        /* Шапка */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 0;
            border-bottom: 1px solid #e4e6eb;
            margin-bottom: 20px;
        }
        .logo {
            font-size: 24px;
            font-weight: bold;
            color: #1877f2;
        }
        .user-menu {
            display: flex;
            gap: 15px;
            align-items: center;
        }
        .user-menu a {
            color: #1877f2;
            text-decoration: none;
            font-size: 14px;
        }
        .user-menu a:hover {
            text-decoration: underline;
        }
        /* Форма поста */
        .post-form {
            background: white;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 1px 2px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        .post-form textarea {
            width: 100%;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 4px;
            resize: vertical; /* Можно менять высоту */
            min-height: 80px;
            font-size: 16px;
            margin-bottom: 10px;
            overflow-y: auto; /* Полоса прокрутки при длинном тексте */
        }
        .post-form button {
            background: #1877f2;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        .post-form button:hover {
            background: #166fe5;
        }
        /* Лента постов */
        .posts {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        .post {
            background: white;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 1px 2px rgba(0,0,0,0.1);
            word-wrap: break-word; /* Перенос длинных слов */
        }
        .post-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
            color: #65676b;
            font-size: 14px;
        }
        .post-author {
            font-weight: bold;
            color: #1c1e21;
        }
        .post-content {
            line-height: 1.5;
            white-space: pre-line; /* Сохранение переносов строк */
            max-height: 600px; /* Ограничение высоты с прокруткой */
            overflow-y: auto;
        }
        .post-timestamp {
            font-size: 12px;
        }
        /* Футер */
        .footer {
            text-align: center;
            margin-top: 30px;
            color: #65676b;
            font-size: 12px;
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Шапка -->
        <div class="header">
            <div class="logo">Unlimited Social</div>
            <div class="user-menu">
                <a href="#" onclick="showLogin()">Войти</a>
                <a href="#" onclick="showRegister()">Регистрация</a>
            </div>
        </div>

        <!-- Форма создания поста -->
        <div class="post-form">
            <textarea 
                id="postContent"
                placeholder="Напишите что угодно... (неограниченный объём)"
            ></textarea>
            <button onclick="createPost()">Опубликовать</button>
        </div>

        <!-- Лента постов -->
        <div class="posts" id="postsList"></div>
    </div>

    <footer class="footer">
        Все сообщения сохраняются локально. Максимум ~5 МБ (зависит от браузера).
    </footer>

    <script>
        // Массив для хранения постов
        let posts = [];

        // Текущий пользователь (демо)
        let currentUser = 'Гость';

        // Загрузка сохранённых постов при открытии страницы
        document.addEventListener('DOMContentLoaded', () => {
            const savedPosts = localStorage.getItem('unlimitedPosts');
            if (savedPosts) {
                posts = JSON.parse(savedPosts);
            }
            renderPosts();
        });

        // Сохранение постов перед закрытием страницы
        window.addEventListener('beforeunload', () => {
            localStorage.setItem('unlimitedPosts', JSON.stringify(posts));
        });

        // Функция создания поста
        function createPost() {
            const content = document.getElementById('postContent').value.trim();
            if (!content) {
                alert('Введите текст поста!');
                return;
            }

            const post = {
                id: Date.now() + Math.random(), // Уникальный ID
                author: currentUser,
                content: content,
                timestamp: new Date().toLocaleString('ru-RU', {
                    day: '2-digit',
                    month: '2-digit',
                    year: 'numeric',
                    hour: '2-digit',
                    minute: '2-digit'
                })
            };

            posts.push(post);
            document.getElementById('postContent').value = ''; // Очищаем поле

            renderPosts(); // Обновляем ленту
        }

        // Функция отрисовки постов
        function renderPosts() {
            const postsList = document.getElementById('postsList');
            postsList.innerHTML = '';

            if (posts.length === 0) {
                postsList.innerHTML = '<p style="text-align:center;color:#65676b;">Пока нет сообщений.</p>';
                return;
            }

            // Сортируем от новых к старым
            posts
                .sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp))
                .forEach(post => {
                    const postElement = document.createElement('div');
                    postElement.className = 'post';
                    postElement.innerHTML = `
                        <div class="post-header">
                            <span class="post-author">${post.author}</span>
                            <span class="post-timestamp">${post.timestamp}</span>
                        </div>
                        <div class="post-content">${post.content}</div>
                    `;
                    postsList.appendChild(postElement);
                });
        }

        // Демо-функции авторизации
        function showLogin() {
            alert('Демо-версия: вход не реализован. Используйте "Гость".');
        }

        function showRegister() {
            alert('Демо-версия: регистрация не реализована. Используйте "Гость".');
        }
    </script>
</body>
</html>
