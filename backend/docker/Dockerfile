# syntax=docker/dockerfile:1

FROM python:3.12-slim


WORKDIR /app
COPY . /app
RUN bash docker/install.sh

# The application must not run as root: create the unprivileged user it runs as
# and give it ownership of the image content. USER is deliberately not set here;
# docker/entrypoint.sh fixes up the bind-mounted database/data directories while
# still root and then drops to mfc, which USER alone could not do.
RUN useradd --create-home --uid 1000 mfc \
    && mkdir -p /app/database /app/data \
    && chown -R mfc:mfc /app
ENTRYPOINT ["bash", "/app/docker/entrypoint.sh"]

# CMD ["flask", "run", "--port=5001", "--host=0.0.0.0"]
