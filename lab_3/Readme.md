# Лабораторная работа №3
## Цель:
Сделать так, чтобы посел пуша в репозиторий автоматически собирался докер образ, и результат его сборки пушился в репозиторий.
## Ход работы:
Для начала мы написали простой dockerfile, результатом выполнения которого является сохранение текста в файл:
``` sh
FROM ubuntu:jammy-20231211.1
RUN echo "Docker image has been built" > /build_output.txt
```
Далее мы написали скрипт для Github Actions:
``` sh
name: CI/CD with Docker

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-22.04

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Login to Docker Hub
      run: docker login -u ${{ secrets.DOCKER_LOGIN }} -p ${{ secrets.DOCKER_PASSWORD }}

    - name: Build Docker image
      run: docker build -t image-for-lab ./lab_3

    - name: Run Docker Container
      run: docker run --name container-for-lab image-for-lab
      
    - name: Copy File From Container
      run: sudo docker cp container-for-lab:/build_output.txt ${{github.workspace}}

    - name: Push File To Repo
      run: |
        git config --global user.email "${{ github.actor }}@users.noreply.github.com"
        git config --global user.name "${{ github.actor }}"
        git add build_output.txt
        git commit -m "Result of building image has been pushed on repo"
        git push origin main
```
Теперь поподробнее о том как работает этот скрипт:
- Сначала мы пишем, что тригером запуска этого скрипта является пуш в репозиторий
- Далее мы описываем работу нашего скрипта
- Первым делом мы указываем название и версию ос на которой будут происходить дальнейшие шаги(раннер)
- Дальше мы клонируем репозиторий внутрь раннера
- Следующим шагом является авторизация в докер хабе с помощью секретов, чтобы использовать приватные образы при надобности
- Далее собирается докер образ из докер файла
- Потом запускается контейнер на основе собравшегося образа, результат выполнения которого сохраняется внутри этого контейнера
- Далее из контейнера мы копируем файл в рабочую директорию раннера
- И в конце этот файл мы пушим в наш репозиторий
## Вывод:
Мы научились пользоваться Github Actions и изучили подход  CI/CD

