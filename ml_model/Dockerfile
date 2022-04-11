FROM python:3.8.12-slim-buster
WORKDIR /app
COPY . .
USER root
RUN apt-get update && apt-get install -y \
	python-pip \
	git \
RUN apt-get update
RUN pip install pypi
RUN pip install safety
RUN pip install safety-db
RUN pip install insecure-package
RUN pip install -r requirements.txt

CMD ["python3", "live_mnist.py"]