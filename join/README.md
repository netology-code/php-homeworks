# Домашнее задание к лекции 4.3 «SELECT из нескольких таблиц»

## Необходимо создать TODO-приложение (список дел)

Поскольку создание таблиц в полной мере мы еще не проходили, предлагаю использовать дамп данных и создать с его помощью таблицы task и user.
```
 CREATE TABLE `task` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `assigned_user_id` int(11) DEFAULT NULL,
  `description` varchar(500) NOT NULL,
  `is_done` tinyint(1) NOT NULL DEFAULT '0',
  `date_added` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

 CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `login` varchar(50) NOT NULL,
  `password` varchar(255) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

```

# Необходимо реализовать возможность: 
* Регистрации
* Авторизации
* Добавление нового вашего дела (описание, дата)
* Вывод списка ваших дел (отсортированных по дате)
* Удаление дела
* Отмечать дела как выполненные/невыполненные
* Делегировать(передавать) дела
* Показать делегированные дела с именем автора
* Вывести количество дел

# Подсказки: 

**Регистрации**
* Проверка существование логина 
```
SELECT id FROM user WHERE login=...
```
* Добавление в таблицу user 
```
INSERT INTO user(...) VALUES (...)
```

**Авторизаци**
* Проверка существование логина и пароля 
```
SELECT ... FROM user WHERE login=... AND password=...
```
* Создание сессии с информаций об id пользователя
```
$_SESSION['user_id'] = ...
```

**Добавление нового вашего дела (описание, дата)**
* Добавление в таблицу task с указанием id пользователя из сессии для полей user_id и assigned_user_id 
``` 
INSERT INTO task (...) VALUES (...)
```

**Вывод списка ваших дел (отсортированных по дате)**

* Вывод данных с фильтрацией id пользователя из сессии ($_SESSION['user_id']) 
```
SELECT ... FROM task WHERE user_id=... ORDER BY date_added ...
```

| Дела | Когда |
| -------- | -------- |
| Задача 1     | 29.02.2020 |
| Задача 2     | 20.02.2020 |


**Удаление дела**

* Запрос на удаление с условием id пользователя из сессии ($_SESSION['user_id']) и id задачи 
```
DELETE FROM task WHERE user_id= ... AND id=... LIMIT 1
```


**Отмечать дела как выполненные/невыполненные**

* Запрос на обновление поля is_done со значением 1 или 0  условием id пользователя из сессии ($_SESSION['user_id']) и id задачи 
```
UPDATE task SET is_done=... WHERE user_id= ... AND id=... LIMIT 1
```

| Дела | Когда | Выполнено/Невыполнено |
| -------- | -------- | -------- |
| Задача 1     | 29.02.2020 | [Выполнить](?id=1&done=1)
| Задача 2     | 20.02.2020 | [Невыполнено](?id=2&done=2)

**Делегировать(передавать) дела**

* Получение списка всех пользователей 
```
SELECT ... FROM user
```
* Для передачи данных использовать select
```
<form action="..." method="POST">
<input name="task_id" type="hidden" value="..."> 
<select name="assigned_user_id">
<?php foreach ($assignedUserList as $assignedUser): ?>
  <option <?php if ($task['assigned_user_id'] == $assignedUser['id']):?>
    selected<?php endif; ?> value="<?= assignedUser['id'] ?>">
    <?= assignedUser['login'] ?>
  </option>
<?php endforeach; ?>
</select>
<buttom type="submit">Делегировать</buttom>
</form>
```

| Дела | Когда | Выполнено/Невыполнено | Исполнитель |
| -------- | -------- | -------- | -------- |
| Задача_1 | 29.02.2020 | [Выполнить](?id=1&done=1) | [Исполнитель 1 >] [Делегировать] |
| Задача_2     | 20.02.2020 | [Невыполнено](?id=2&done=2) | [Исполнитель 2 >] [Делегировать] |

* Запрос на обновление поля assigned_user_id со значением id пользователя
```
UPDATE task SET assigned_user_id=... WHERE id=... user_id=...
```


**Показать делегированные дела с именем автора**
* Использовать запрос к нескольким таблицам c фильтрацией по id пользователя
```
SELECT ... FROM task t INNER JOIN user u ON u.id=t.assigned_user_id WHERE
t.user_id = ... OR t.assigned_user_id = ...
```

**Вывести количество дел**

* Использовать группировку данных 
```
SELECT count(*) FROM task WHERE t.user_id = ... OR t.assigned_user_id = ...
```



[Дамп](resourses/) базы данных для выполнения задания.

---
Способы предоставить домашнее задание:
1. Исходный код на [BitBucket](https://bitbucket.org/) или [GitHub](https://github.com/).
