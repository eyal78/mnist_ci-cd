FROM python:3.8.12-slim-buster
WORKDIR /app
COPY . .
COPY pip.conf pip.conf
ENV PIP_CONFIG_FILE pip.conf
USER root
RUN apt-get update
RUN apt-get install -y
RUN python3
RUN	git
RUN pip3 install pypi
RUN pip3 install safety
RUN pip3 install safety-db
RUN pip3 install -r requirements.txt

CMD ["python3", "app.py"]