FROM python:3.10-slim

ENV FLASK_RUN_HOST=0.0.0.0

WORKDIR /app

# Install system dependencies required to build Python packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        gcc \
        build-essential \
        libssl-dev \
        libffi-dev && \
    rm -rf /var/lib/apt/lists/*

# Install Pipenv
RUN pip install --no-cache-dir pipenv

# Create application user
RUN useradd --create-home --shell /bin/bash app

# Create required directories
RUN mkdir -p /var/www/app /app && \
    chown -R app:app /var/www/app /app

# Copy dependency files first for Docker layer caching
COPY Pipfile Pipfile.lock /app/

# Install dependencies from Pipfile.lock
RUN cd /app && pipenv install --system --deploy

# Copy application source
COPY . /app

# Change ownership
RUN chown -R app:app /app /var/www/app

# Run as non-root user
USER app

# Start uWSGI
CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "-s", "/var/www/app/app.sock", "--manage-script-name", "--mount", "/=app:app"]