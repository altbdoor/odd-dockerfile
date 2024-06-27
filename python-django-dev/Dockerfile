# syntax=docker/dockerfile:1

FROM python:3.9.17-slim-bullseye as base_py

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    DEBIAN_FRONTEND=noninteractive \
    PATH="/opt/venv/bin:$PATH" \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1

# libpq-dev is needed for building and runtime
RUN apt-get update -q \
    && apt-get install -y -q --no-install-recommends libpq-dev \
    && python -m venv /opt/venv

WORKDIR /app

# ========================================
FROM base_py AS base_with_deps

# build dependencies for psycopg2
# note: please use psycopg2-binary ffs
RUN apt-get install -y -q --no-install-recommends python3.9-dev build-essential \
    && pip install wheel

COPY ./requirements/ /app/requirements
RUN pip install -r requirements/dev.txt

# ========================================
FROM base_py AS user_image

RUN adduser --system pyuser
COPY --from=base_with_deps --chown=pyuser:pyuser /opt/venv /opt/venv

USER pyuser
EXPOSE 8000

CMD ["python", "--version"]
