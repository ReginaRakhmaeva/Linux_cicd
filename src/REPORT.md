## Part 1. Готовый докер

Настройка подключения по ssh к vm

включаем проброс портов

![alt text](image-3.png)

sudo nano /etc/ssh/sshd_config

открываем порт 22

![alt text](image-2.png)

systemctl restart ssh

проверяем статус ssh

![alt text](image-1.png)

подключаемся по shh через power shell
![alt text](image-4.png)

генерируем ssh для gitlab
![alt text](image-5.png)

добавляем ключ в gitlab

cкачан и установлен на виртуальную машину gitlab-runner
![alt text](image.png)  

Запуск gitlab-runner и регистрация его для использования в текущем проекте
![alt text](image-7.png)
![alt text](image-8.png)
