FROM python:3.8.12-slim-buster
WORKDIR /app
COPY . .
COPY pip.conf pip.conf
ENV PIP_CONFIG_FILE pip.conf
USER root
RUN apt-get update && apt-get install -y \
	python-pip \
	git \
RUN pip install pypi
RUN pip install safety
RUN pip install safety-db
RUN pip install insecure-package
RUN pip install -r requirements.txt

CMD ["python3", "app.py"]