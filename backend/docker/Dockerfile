# syntax=docker/dockerfile:1

FROM python:3.12-slim


WORKDIR /app
COPY . /app
RUN bash docker/install.sh

# Drop privileges: the application must not run as root.
# UID 1000 matches the usual host user, so the bind-mounted database/data
# directories stay writable in the default docker-compose setup.
RUN useradd --create-home --uid 1000 mfc \
    && mkdir -p /app/database /app/data \
    && chown -R mfc:mfc /app
USER mfc

# CMD ["flask", "run", "--port=5001", "--host=0.0.0.0"]
