# Развертка MongoDB

## Наименование: Основы работы с СУБД MongoDB

## Цель: Знакомство с СУБД MongoDB

## Задания

1. Подготовить рабочее окружение в соответствии с рекомендациями (см. раздел "Рекомендации").
2. На языке MongoDB Query Language (MQL) написать схему БД.
3. Реализовать и заполнить коллекции данными.

## Рекомендации

### Установка MongoDB

Рассмотрим пошаговую установку СУБД MongoDB на примере ОС Windows.

1. Перейдите на официальный сайт MongoDB: [https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community).
2. Выберите версию для Windows и скачайте установочный файл (.msi).  
   ![Выбор версии для скачивания](screens/choice_download.png)
3. Запустите установочный файл и следуйте инструкциям мастера установки:  
   - Запускаем установочный файл в зависимости от разрядности ОС.  
     ![Запуск установочного файла](screens/install.png)  
   - Соглашаемся с лицензионным соглашением.  
     ![Лицензионное соглашение](screens/agree.png)  
   - Выбираем варианты установки: Complete или Custom. Complete устанавливает весь функционал, который есть для работы с MongoDB. Custom — это избирательная установка для продвинутых пользователей. Выбираем Complete.  
     ![Выбор типа установки](screens/choice.png)  
   - Нажимаем Next, чтобы продолжить установку.  
     ![Продолжение установки](screens/agree_complete.png)  
   - Соглашаемся на установку MongoDB Compass. Нажимаем Next, чтобы продолжить.  
     ![Установка MongoDB Compass](screens/download_compass.png)  
   - Нажимаем Install и ждем полной установки.  
     ![Процесс установки](screens/download.png)

### Проверка работоспособности СУБД

После установки необходимо проверить, что сервер MongoDB работает корректно. Есть два способа: через приложение MongoDB Compass или через командную строку. Лучше использовать приложение, но если вас интересует консоль, вот небольшая инструкция:

1. Откройте командную строку.
2. Перейдите в каталог, где установлен MongoDB (например, `C:\Program Files\MongoDB\Server\<версия>\bin`).
3. Запустите команду `mongod` для старта сервера MongoDB. Если сервер запустился успешно, вы увидите сообщения о его работе (по умолчанию используется порт 27017). Пример:  
   ```bash
   mongod --dbpath C:\data\db
   ```  
   ![Запуск сервера MongoDB](screens/consol.png)
4. Откройте еще одну командную строку и выполните команду `mongo` для подключения к серверу через MongoDB shell. В зависимости от версии MongoDB, вам может понадобиться отдельно скачать `mongo` и добавить его в системные переменные.

Если подключение прошло успешно, вы увидите приглашение MongoDB shell (например, `>`).

### Создание тестовой БД

Для удобства работы с MongoDB можно использовать графический интерфейс, такой как MongoDB Compass, который доступен при выборе установки Complete. Если вы выбрали Custom или отказались от его установки, скачайте его отдельно:

1. Скачайте и установите MongoDB Compass с сайта: [https://www.mongodb.com/products/compass](https://www.mongodb.com/products/compass).
2. Запустите MongoDB Compass.
3. Подключитесь к локальному серверу MongoDB, указав адрес `localhost` и порт `27017` (значение по умолчанию).
4. В MongoDB базы данных создаются автоматически при первом использовании. Для создания новой базы данных выполните в MongoDB shell команду `use mydatabase` или создайте её через интерфейс Compass, указав имя, например, `testdb`.

### Создание коллекций

В MongoDB данные хранятся в коллекциях, которые аналогичны таблицам в реляционных базах данных, но не имеют строгой схемы. Документы в коллекциях могут иметь разную структуру.

Рассмотрим пример создания коллекций для хранения информации о книгах, категориях и авторах.

#### Пример логической модели

- **Книга**: имеет название, год издания и принадлежит к одной категории.
- **Категория**: имеет название.
- **Автор**: имеет имя и фамилию.
- **Связь**: книга может быть написана несколькими авторами, а автор может написать несколько книг.

В MongoDB связи между данными можно моделировать с помощью вложенных документов или ссылок. Для простоты будем использовать ссылки.

#### Схема в MongoDB

- **Коллекция** `categories`:  
  ```json
  {
      "_id": ObjectId(),
      "name": "Фантастика"
  }
  ```

- **Коллекция** `authors`:  
  ```json
  {
      "_id": ObjectId(),
      "first_name": "Джон",
      "last_name": "Доу"
  }
  ```

- **Коллекция** `books`:  
  ```json
  {
      "_id": ObjectId(),
      "title": "Приключения в космосе",
      "year": 2020,
      "category_id": ObjectId("...")  // Ссылка на категорию
  }
  ```

- **Коллекция** `book_authors` (для связи "многие-ко-многим"):  
  ```json
  {
      "_id": ObjectId(),
      "book_id": ObjectId("..."),
      "author_id": ObjectId("...")
  }
  ```

Коллекции в MongoDB создаются автоматически при вставке первого документа. Однако для явного создания можно использовать команду:  
```javascript
db.createCollection("categories")
```

### Вставка данных

Пример вставки данных в коллекцию `categories` через MongoDB shell:  
```javascript
db.categories.insertOne({ name: "Фантастика" })
```

Аналогично можно вставить данные в другие коллекции:  
```javascript
db.authors.insertOne({ first_name: "Джон", last_name: "Доу" })
db.books.insertOne({ title: "Приключения в космосе", year: 2020, category_id: ObjectId("...") })
db.book_authors.insertOne({ book_id: ObjectId("..."), author_id: ObjectId("...") })
```

Можно вставить дополнительные данные вручную или сгенерировать их.

### Запросы

Пример запроса для получения всех книг:  
```javascript
db.books.find()
```

Пример запроса для получения книг определённой категории:  
```javascript
db.books.find({ category_id: ObjectId("...") })
```

### Пример заполнения тестовой базы данных

Реализация создания базы данных через консоль `mongosh` в приложении MongoDB Compass:  
1. Выберите "Open MongoDB shell", чтобы открыть консоль `mongosh`.  
   ![Открытие MongoDB shell](screens/create_db.png)  
2. Создаем и заполняем коллекцию `categories`.  
   ![Заполнение коллекции categories](screens/categories.png)  
3. Создаем и заполняем коллекцию `authors`.  
   ![Заполнение коллекции authors](screens/authors.png)  
4. Создаем и заполняем коллекцию `books`.  
   ![Заполнение коллекции books](screens/books.png)  
5. Создаем и заполняем коллекцию `book_authors`, которая является коллекцией связи.  
   ![Заполнение коллекции book_authors](screens/book_authors.png)

Ниже приведён пример скрипта для создания тестовой базы данных с 10 документами в каждой коллекции, который показан на скриншотах ранее:  
```javascript
// Переключаемся на тестовую базу данных
use testdb

// Заполнение коллекции categories
db.categories.insertMany([
    { name: "Фантастика" },
    { name: "Детектив" },
    { name: "Роман" },
    { name: "Научная литература" },
    { name: "Фэнтези" },
    { name: "История" },
    { name: "Поэзия" },
    { name: "Приключения" },
    { name: "Ужасы" },
    { name: "Комедия" }
])

// Заполнение коллекции authors
db.authors.insertMany([
    { first_name: "Джон", last_name: "Доу" },
    { first_name: "Анна", last_name: "Смит" },
    { first_name: "Иван", last_name: "Иванов" },
    { first_name: "Мария", last_name: "Петрова" },
    { first_name: "Джеймс", last_name: "Бонд" },
    { first_name: "Елена", last_name: "Кузнецова" },
    { first_name: "Алексей", last_name: "Сидоров" },
    { first_name: "Сара", last_name: "Коннор" },
    { first_name: "Питер", last_name: "Паркер" },
    { first_name: "Ольга", last_name: "Морозова" }
])

// Получение ID категорий для использования в книгах
let sciFiId = db.categories.findOne({ name: "Фантастика" })._id
let detectiveId = db.categories.findOne({ name: "Детектив" })._id

// Заполнение коллекции books
db.books.insertMany([
    { title: "Космическая одиссея", year: 1968, category_id: sciFiId },
    { title: "Убийство на улице Морг", year: 1841, category_id: detectiveId },
    { title: "Звёздный путь", year: 1979, category_id: sciFiId },
    { title: "Собака Баскервилей", year: 1902, category_id: detectiveId },
    { title: "Марсианские хроники", year: 1950, category_id: sciFiId },
    { title: "Десять негритят", year: 1939, category_id: detectiveId },
    { title: "Дюна", year: 1965, category_id: sciFiId },
    { title: "Шерлок Холмс", year: 1887, category_id: detectiveId },
    { title: "451 градус по Фаренгейту", year: 1953, category_id: sciFiId },
    { title: "Загадка Эндхауза", year: 1932, category_id: detectiveId }
])

// Получение ID книг и авторов для связи
let book1Id = db.books.findOne({ title: "Космическая одиссея" })._id
let book2Id = db.books.findOne({ title: "Убийство на улице Морг" })._id
let author1Id = db.authors.findOne({ last_name: "Доу" })._id
let author2Id = db.authors.findOne({ last_name: "Смит" })._id

// Заполнение коллекции book_authors
db.book_authors.insertMany([
    { book_id: book1Id, author_id: author1Id },
    { book_id: book1Id, author_id: author2Id },
    { book_id: book2Id, author_id: author1Id },
    { book_id: db.books.findOne({ title: "Звёздный путь" })._id, author_id: author2Id },
    { book_id: db.books.findOne({ title: "Собака Баскервилей" })._id, author_id: author1Id },
    { book_id: db.books.findOne({ title: "Марсианские хроники" })._id, author_id: author2Id },
    { book_id: db.books.findOne({ title: "Десять негритят" })._id, author_id: author1Id },
    { book_id: db.books.findOne({ title: "Дюна" })._id, author_id: author2Id },
    { book_id: db.books.findOne({ title: "Шерлок Холмс" })._id, author_id: author1Id },
    { book_id: db.books.findOne({ title: "451 градус по Фаренгейту" })._id, author_id: author2Id }
])
```

## Ссылки на документацию

1. [Установка MongoDB](https://docs.mongodb.com/manual/installation/)
2. [Основы работы с MongoDB](https://docs.mongodb.com/manual/crud/)
3. [Моделирование данных](https://docs.mongodb.com/manual/data-modeling/)