FROM python:3.8.12-slim-buster
WORKDIR /app
COPY . .
USER root
RUN apt-get update
RUN pip3 install pypi
RUN pip3 install safety
RUN pip3 install safety-db
RUN pip3 install -r requirements.txt
RUN safety check
RUN pip3 freeze | safety check --stdin

CMD ["python3", "app.py"]