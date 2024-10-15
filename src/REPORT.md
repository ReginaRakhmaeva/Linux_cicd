## Part 1. Настройка gitlab-runner

Настройка подключения по ssh к vm

включаем проброс портов, в настройках vm virtual box

![alt text](image-3.png)

> sudo nano /etc/ssh/sshd_config
открываем порт 22

![alt text](image-2.png)

перезагружаем ssh
> systemctl restart ssh
проверяем статус ssh

![alt text](image-1.png)

подключаемся по shh через power shell
![alt text](image-4.png)

генерируем ssh для gitlab
![alt text](image-5.png)

добавляем ключ в gitlab

cкачан и установлен на виртуальную машину gitlab-runner

> curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | bash

> apt-get install gitlab-runner
![alt text](image.png)  

Запуск gitlab-runner и регистрация его для использования в текущем проекте

> gitlab-runner register
![alt text](image-7.png)
![alt text](image-8.png)

## Part 2. Сборка

создаем конфигурационный файл для настройки CI/CD в GitLab
> touch .gitlab-ci.yml

В файл gitlab-ci.yml добавлен этап запуска сборки через мейк файл из проекта C3. Файлы, полученные после сборки (артефакты), сохраняются в произвольную директорию со сроком хранения 30 дней.

Содержание файл gitlab-ci.yml 
![alt text](image-9.png)

установка make в виртуальную машину
> sudo apt update
> sudo apt install build-essential

![alt text](image-10.png)

проверка установлен ли make
> make --version

перезагружаем gitlab-runner
> sudo gitlab-runner restart

пуш проекта в gitlab
> git add ../.gitlab-ci.yml
> git commit -m "исправила теги в yml"
> git push origin develop

задача в CI/CD gitlab
![alt text](image-11.png)
![alt text](image-12.png)

Артефакты после выполнения задачи
![alt text](image-13.png)

# Part 3. Тест кодстайла

устанавливаем пакет clang-format на машину с ранером

> sudo apt install clang-format

проверка установлен ли clang-format

> clang-format --version

добавляем дополнительное этап (style) пайплайна

![alt text](image-14.png)

пуш проекта в gitlab
> git add ../.gitlab-ci.yml
> git commit -m "part 3, обновленный yml"
> git push origin develop

задача в CI/CD gitlab
![alt text](image-15.png)

выполненный этап style
![alt text](image-16.png)