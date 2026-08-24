FROM python:3.10-slim

ENV FLASK_RUN_HOST=0.0.0.0
ENV FLASK_RUN_PORT=5000

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

# Copy dependency files
COPY Pipfile Pipfile.lock /app/

# Install dependencies
RUN pipenv install --system --deploy

# Copy application
COPY . /app

# Change ownership
RUN chown -R app:app /app /var/www/app

# Run as non-root user
USER app

# Start Flask application with uWSGI
CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "--module", "__init__:app"]