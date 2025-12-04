# Pipeline (Docker + Jenkins) для сравнения двух решений алгозадачи.
Решения необходимо вставить в функции main в файлах program_a/main.py и program_b/main.py
Тесты необходимо разместить в папке tests
# Установка необходимых компонентов (Ubuntu)
1) Установить Docker - https://docs.docker.com/engine/install/ubuntu/
2) Установка и запуск Jenkins
```
docker volume create jenkins_home
docker run -d \
  -u root \
  -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```
# Найстрока Jenkins
1) Установка Docker CLI внтури Jenkins
```
docker exec -it jenkins bash
apt update
apt install -y docker.io
```
2) Получение начального пароля администратора
```
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```
3) Первый вход в Jenkins через браузер с помощью пароля администартора
```
http://localhost:8080
```
4) Установка необходимых плагинов - Install suggested plugins
5) Создание первого пользователя и настройка URL для Jenkins 

# Запуск пайплайна
1) Создать Item -> Pipeline
2) Внизу Definition -> Pipeline Script From SCM -> git -> Ссылка на репозиторий  -> Branches to build -> ./main -> Save
3) Собрать сейчас
   
   


