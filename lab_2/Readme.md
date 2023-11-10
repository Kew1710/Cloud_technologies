## Создание "плохого" и "хорошего" Dockerfile

### Плохой Dockerfile

```sh
FROM ubuntu
COPY . /test
WORKDIR /test
RUN apt-get update
RUN apt-get install -y python3-pip
RUN apt-get install -y nodejs
RUN apt-get install snap -y
RUN apt-get install curl -y
RUN pip install numpy
RUN pip install pandas
EXPOSE 8081
CMD [ "python3", "service1.py"]
```
Какие ошибки допущены в этом Dockerfile:
 - Переизбыток "RUN", которые можно объединить в один
 - Использован Latest tag (Если не указывать версию, то устанавливается последняя, из - за чего могут появиться ошибки в работе приложения)
 - Установлены ненужные зависимоти
 - Копируется не только использованный файл, а вся директория, в которой могут быть лишние данные или конфиденциальная информация( например - .env файл)
  ```sh
  root@3f2933a647e5:/test# ls
Dockerfile  service1.py  service2.py
```
### Хороший Dockerfile

```sh
FROM ubuntu:jammy-20231004
RUN mkdir test
COPY service1.py /test
WORKDIR /test
RUN apt-get update && apt-get install -y python3-pip && pip install numpy
EXPOSE 8081
# CMD [ "python3", "service1.py"]
```
Какике ошибки исправлены:
- Установлена конкретная версия Ubuntu
- Объединены "RUN"
- Отсутствуют ненужные зависимости
- Копируется только используемый файл
```sh
root@5ba527cfa44b:/test# ls
service1.py
```
## "Плохие" практики использования контейнера
- Попытка использовать виртуальную машину для контейнеров
- В контейнере реализовано более одного процесса
- Контейнер запускается с root правами
