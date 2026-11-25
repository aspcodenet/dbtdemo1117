# syntax=docker/dockerfile:1
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    # dbt looks for profiles.yml here; override with -e DBT_PROFILES_DIR=... if needed
    DBT_PROFILES_DIR=/usr/app

# git is required by `dbt deps` if the project ever adds packages from a git source
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/app

# Dependencies first, so that editing models does not invalidate this layer
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# dbt writes logs/ and target/ next to the project, so run as a user that owns them
RUN useradd --create-home --uid 10001 dbt \
    && chown -R dbt:dbt /usr/app
USER dbt

# `docker run <image> build` runs `dbt build`; any dbt subcommand can be passed instead,
# e.g. `docker run <image> run --select staging`
ENTRYPOINT ["dbt"]
CMD ["build"]
