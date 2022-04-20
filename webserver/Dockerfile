FROM python:3.8.12-slim-buster
WORKDIR /app
COPY . .
COPY pip.conf pip.conf
ENV PIP_CONFIG_FILE pip.conf
USER root
RUN apt-get update && apt-get install -y \
	python3-pip3 \
	git \
RUN pip3 install pypi
RUN pip3 install safety
RUN pip3 install safety-db
RUN pip3 install insecure-package
RUN pip3 install -r requirements.txt

CMD ["python3", "app.py"]