# Pipeline (Docker + Jenkins | self-hosted GitLab) для сравнения двух решений алгозадачи.
Решения необходимо вставить в функции main в файлах program_a/main.py и program_b/main.py
Тесты необходимо разместить в папке tests
# Установка необходимых компонентов (Ubuntu)
1) [Установить Docker](https://docs.docker.com/engine/install/ubuntu/)

# Установка и найстрока Jenkins
1) Установка и запуск Jenkins
```
docker volume create jenkins_home
docker run -d \
  -u root \
  -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```
2) Установка Docker CLI внтури Jenkins
```
docker exec -it jenkins bash
apt update
apt install -y docker.io
```
3) Получение начального пароля администратора
```
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```
4) Первый вход в Jenkins через браузер с помощью пароля администартора
```
http://localhost:8080
```
5) Установка необходимых плагинов - Install suggested plugins
6) Создание первого пользователя и настройка URL для Jenkins 

# Запуск пайплайна в Jenkins
1) Создать Item -> Pipeline
2) Внизу Definition -> Pipeline Script From SCM -> git -> Ссылка на репозиторий  -> Branches to build -> ./main -> Save
3) Собрать сейчас

# Установка и найстрока GitLab
1) Создать папки для GitLab
```
mkdir -p ./gitlab/data/docker/gitlab/etc/gitlab
mkdir -p ./gitlab/data/docker/gitlab/var/opt/gitlab
mkdir -p ./gitlab/data/docker/gitlab/var/log/gitlab
```
2) Создать конфиг файл для GitLab Runner
```
mkdir -p ./gitlab/config
touch ./gitlab/config/config.toml
```
3) Развернуть GitLab
```
sudo docker compose up -d 
```
4) Дождаться окончания установки и запуска GitLab и перейти в браузере на 
```
localhost:8000
```
5) Авторизоваться под пользователем root, пароль - CHANGEME123
6) Создать проект в GitLab: New Project -> Create Blank Project -> Project Name = Algopipeline -> Create Project
7) Создать GitLab Runner: Algopipeline -> Settings -> CI/CD -> Runners -> Create project runner -> +Run Untagged Job -> Create runner
8) Скопировать Registration token: Algopipeline -> Settings -> CI/CD -> Runners -> три точки справа от кнопки Create project runner -> Copy Registration token
9) Регистрация Runner с помощью скопированного токена
```
docker exec -it gitlab-runner gitlab-runner register \
  --non-interactive \
  --url "http://gitlab" \
  --registration-token "<Ваш Registration token>" \
  --executor "docker" \
  --docker-image "alpine:latest" \
  --description "docker-runner" \
  --docker-network-mode "gitlab_net"
```
10) В файле ./gitlab/config/config.toml необходимо поменять значение переменной privileged на true
```
  [runners.docker]
    privileged = true
```
11) Перезапустить GitLab
```
sudo docker compose restart
```
# Запуск пайплайна в GitLab
1) Добавить GitLab репозиторий
```
git remote add gitlab http://localhost:8000/root/algopipeline.git 
```
2) Отправить код в GitLab (пользователь - root, пароль - CHANGEME123)
```
git push gitlab master
```
3) Наблюдать за выполнением Pipeline в браузере: Algopipeline -> Build -> Pipelines
