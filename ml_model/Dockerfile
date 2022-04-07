FROM python:3.8.12-slim-buster
WORKDIR /app
COPY . .
USER root
RUN apt-get update && apt-get install -y \
	python-pip \
	git
RUN apt-get update
RUN apt-get install ffmpeg libsm6 libxext6  -y
RUN pip install safety
RUN pip install -r requirements.txt

CMD ["python3", "live_mnist.py"]