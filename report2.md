# Отчет по выполнению лабораторной работы

## Окружение
- Операционная система: Windows 11
- Предполагается наличие установленного Git и Git Bash
- В качестве текстового редактора для Git Hooks может использоваться встроенный редактор Git Bash, Notepad++ или другой удобный инструмент.

## Цели работы
1. Настроить Git Hook (`pre-commit`), который автоматически проверяет форматирование `.txt` файлов перед коммитом.
2. Настроить и продемонстрировать процесс разработки с использованием Git Flow.

## Шаги выполнения работы

### Шаг 1: Настройка Git Hooks для проверки файлов `.txt` при коммите
1. Инициализировать Git-репозиторий (если еще не инициализирован):
   ```bash
   git init
   ```
   
2. Перейти в директорию с репозиторием:
   ```bash
   cd путь/к/вашему/репозиторию
   ```

3. Перейти в папку с хуками Git:
   ```bash
   cd .git/hooks
   ```

4. Создать или отредактировать скрипт `pre-commit`:
   ```bash
   touch pre-commit
   ```
   
   Открыть файл `pre-commit` в редакторе и добавить в него следующий код:
   ```bash
   #!/bin/bash

   FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.txt$')

   if [ -z "$FILES" ]; then
       exit 0
   fi

   ERROR=0

   for FILE in $FILES; do
       if [ ! -f "$FILE" ]; then
           echo "Ошибка: файл $FILE не найден."
           ERROR=1
           continue
       fi

       if [ ! -s "$FILE" ]; then
           echo "Ошибка: файл $FILE пустой."
           ERROR=1
           continue
       fi

       # Проверка наличия подписи автора (пример: строка "Author")
       if ! grep -q "Author" "$FILE"; then
           echo "Ошибка: файл $FILE не содержит 'Author'."
           ERROR=1
       fi
   done

   if [ $ERROR -eq 1 ]; then
       echo "Коммит отклонен: проверьте формат .txt файлов."
       exit 1
   fi

   exit 0
   ```

5. Сделать файл `pre-commit` исполняемым:
   ```bash
   chmod +x pre-commit
   ```

6. Проверить работу хука:
   - Создать тестовый `.txt` файл без требуемого содержания:
     ```bash
     echo "Test file content" > test.txt
     git add test.txt
     git commit -m "Проверка хука без подписи автора"
     ```
     Коммит должен быть отклонен с сообщением об ошибке.

   - Добавить требуемую строку:
     ```bash
     echo "Author: Test User" >> test.txt
     git add test.txt
     git commit -m "Проверка хука с подписью автора"
     ```
     Коммит должен пройти успешно.

### Шаг 2: Установка и настройка Git Flow
1. Установить Git Flow (при необходимости). Для Chocolatey:
   - Запустить PowerShell от имени администратора.
   - Выполнить:
     ```powershell
     choco install git-flow
     ```
   
   Проверить установку:
   ```bash
   git flow version
   ```

2. Инициализировать Git Flow в корне репозитория:
   ```bash
   git flow init
   ```
   Принять настройки по умолчанию или настроить их по своему усмотрению.

### Шаг 3: Работа с ветками по Git Flow
1. Создать ветку функциональности `task-management`:
   ```bash
   git flow feature start task-management
   ```
   
2. Внести изменения (пример: добавить функциональность в файл `task_manager.py`):
   ```bash
   echo "def create_task(title, description):" > task_manager.py
   echo "    # Логика создания задачи" >> task_manager.py
   echo "    print(f\"Создана новая задача: {title}\")" >> task_manager.py
   ```
   
3. Закоммитить изменения:
   ```bash
   git add task_manager.py
   git commit -m "Добавлен функционал управления задачами"
   ```
   
4. Завершить работу над веткой функциональности:
   ```bash
   git flow feature finish task-management
   ```
   Если возникнут конфликты, разрешить их стандартными методами Git (редактирование файлов, `git add`, `git commit`).

### Шаг 4: Создание релиза
1. Переключиться на ветку `develop` (если еще не переключены):
   ```bash
   git checkout develop
   ```

2. Начать релиз:
   ```bash
   git flow release start v1.0.0
   ```
   
3. Обновить версию проекта (например, в `version.txt`):
   ```bash
   echo "v1.0.0" > version.txt
   git add version.txt
   git commit -m "Обновлена версия для релиза v1.0.0"
   ```

4. Завершить релиз:
   ```bash
   git flow release finish v1.0.0
   ```

   Если возникнут конфликты, разрешить их, затем продолжить операцию.

### Шаг 5: Создание Hotfix
1. Начать hotfix:
   ```bash
   git flow hotfix start hotfix-1.0.1
   ```
   
2. Внести исправления (пример: исправление ошибки в `file_with_error.py`):
   ```bash
   echo "# Исправлена критическая ошибка" > file_with_error.py
   git add file_with_error.py
   git commit -m "Исправлена критическая ошибка"
   ```

3. Завершить hotfix:
   ```bash
   git flow hotfix finish hotfix-1.0.1
   ```
   Во время завершения может открыться текстовый редактор для сообщения тега. Ввести сообщение, затем сохранить и выйти.

### Шаг 6: Отправка изменений на удаленный репозиторий
1. Отправить все изменения веток `develop` и `main`:
   ```bash
   git push origin develop
   git push origin main
   ```

2. Отправить теги:
   ```bash
   git push origin --tags
   ```

### Проверка результата
- Убедиться, что все ветки и теги присутствуют в удаленном репозитории.
- Проверить работу хука, сделав тестовый коммит с `.txt` файлом.
- Проверить логи: `git log --oneline --graph --decorate --all` и убедиться, что все ветки слились корректно.

## Итог
В ходе работы был настроен Git Hook для проверки `.txt` файлов перед коммитом и продемонстрирован полный цикл разработки с использованием Git Flow: создание ветки функциональности, завершение фичи, создание релиза и создание hotfix. Выполнив описанные команды и следуя описанным шагам, можно повторно воспроизвести результаты данной лабораторной работы.
