FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -yqq libpq-dev python3-dev python3-pip curl dnsutils && \
    curl -sSL https://install.python-poetry.org | python3 -

ENV PATH=$PATH:/root/.local/bin

COPY . app/

WORKDIR /app

RUN poetry install --no-root

ENTRYPOINT [ "poetry", "run", "task", "prod" ]