FROM python:3.8-slim-buster
# Install python packages

RUN pip install mlflow==1.13.1 && \
    pip install boto3==1.16.49 && \
    pip install psycopg2-binary==2.8.6

ENTRYPOINT ["mlflow", "server"]