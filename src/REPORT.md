## Part 1. Настройка gitlab-runner

Настройка подключения по ssh к vm

* включаем проброс портов, в настройках vm virtual box

![alt text](image-3.png)

> sudo nano /etc/ssh/sshd_config

* открываем порт 22

![alt text](image-2.png)

* перезагружаем ssh
> systemctl restart ssh

* проверяем статус ssh

![alt text](image-1.png)

* подключаемся по shh через power shell
![alt text](image-4.png)

* генерируем ssh для gitlab
![alt text](image-5.png)

* добавляем ключ в gitlab

* cкачан и установлен на виртуальную машину gitlab-runner

> curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | bash

> apt-get install gitlab-runner
![alt text](image.png)  

* Запуск gitlab-runner и регистрация его для использования в текущем проекте

> gitlab-runner register
![alt text](image-7.png)
![alt text](image-8.png)

## Part 2. Сборка

* создаем конфигурационный файл для настройки CI/CD в GitLab
> touch .gitlab-ci.yml

В файл gitlab-ci.yml добавлен этап запуска сборки через мейк файл из проекта C3. Файлы, полученные после сборки (артефакты), сохраняются в произвольную директорию со сроком хранения 30 дней.

* Содержание файл gitlab-ci.yml 
![alt text](image-9.png)

* установка make в виртуальную машину
> sudo apt update
> sudo apt install build-essential

![alt text](image-10.png)

* проверка установлен ли make
> make --version

* перезагружаем gitlab-runner
> sudo gitlab-runner restart

* пуш проекта в gitlab
> git add ../.gitlab-ci.yml
> git commit -m "исправила теги в yml"
> git push origin develop

* задача в CI/CD gitlab
![alt text](image-11.png)
![alt text](image-12.png)

* артефакты после выполнения задачи
![alt text](image-13.png)

# Part 3. Тест кодстайла

* устанавливаем пакет clang-format на машину с ранером

> sudo apt install clang-format

* проверка установлен ли clang-format

> clang-format --version

* добавляем дополнительный этап (style) пайплайна

![alt text](image-14.png)

* пуш проекта в gitlab
> git add ../.gitlab-ci.yml
> git commit -m "part 3, обновленный yml"
> git push origin develop

* задача в CI/CD gitlab
![alt text](image-15.png)

* выполненный этап style
![alt text](image-16.png)

# Part 4. Интеграционные тесты

* устанавливаем пакет valgrind на машину с ранером
> sudo apt-get install valgrind

* проверка установлен ли valgrind
> valgrind --version

* исправлен тестовый файл, добавлен выход с ошибкой при наличие fail

![alt text](image-6.png)

* добавляем дополнительный этап (tests) пайплайна

![alt text](image-17.png)

* задача в CI/CD gitlab

![alt text](image-19.png)

* выполненный этап tests

![alt text](image-18.png)

* чуть изменяем тест для fail

![alt text](image-20.png)

* теперь fail вызывает код ошибки

![alt text](image-21.png)

![alt text](image-22.png)

<details>
    <summary>Другой вариант решения через log file</summary>
    script:
    - cd src/cat
    - make test | tee test_output.log  # Запускаем тесты и сохраняем вывод в лог
    - make test
    - cd ../grep
    - make test | tee -a test_output.log  # Добавляем вывод тестов в тот же лог
    - |
      if grep -q "FAIL: 0" test_output.log; then
        echo "All tests passed."
      else
        echo "Tests failed!"
        exit 1
      fi
    - make test
</details>

# Part 5. Этап деплоя

* создаем вторую машину cicd2

* меняем сеть на сетевой мост
![alt text](image-30.png)

* меняем пользователя на gitlab-runner
> sudo su gitlab-runner

![alt text](image-29.png)

* генерируем ssh ключ
> ssh-keygen -t rsa -b 4096 -C "gitlab-runner@cicd-system"

![alt text](image-31.png)

* копируем ssh на машину 2
> ssh-copy-id br2@192.168.1.24

![alt text](image-32.png)

* добавляем дополнительный этап (deploy_job) пайплайна

![alt text](image-25.png)

* script_deploy.sh

![alt text](image-26.png)

* делаем владельцем /usr/local/bin br2 на 2 машине 
> sudo chown -R root /usr/local/bin

* выполненный этап deploy_job

![alt text](image-28.png)
![alt text](image-24.png)

* проверяем файлы на машине 2 

![alt text](image-27.png)

# Part 6. Дополнительно. Уведомления

* создаем бота через бота в тг BotFather

![alt text](image-33.png)

* узнаем id написав в бот сообщения и перейбя в браузере по ссылке со своим токеном

> https://api.telegram.org/botYOUR_TOKEN/getUpdates

* создаем файл notify.sh

![alt text](image-34.png)

* дописываем в yml 

```console
after_script:
    - bash src/notify.sh
```

* пушим и смотрим уведомления в тг

![alt text](image-35.png)