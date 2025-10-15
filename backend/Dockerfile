# Use an official slim Python base image
FROM python:3.11-slim


# Avoid generating .pyc files, ensure output is shown immediately
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Install system deps required for some Python packages and for healthcheck tools
# (wget used in healthcheck). Keep layer small and clean up apt cache.
RUN apt-get update \
 && apt-get install -y --no-install-recommends gcc libpq-dev wget ca-certificates \
 && rm -rf /var/lib/apt/lists/*

# Create application directory
RUN mkdir -p /app
WORKDIR /app

# Copy requirements and install first (cache bust optimization)
COPY requirements.txt /app/requirements.txt
RUN pip install --upgrade pip \
 && pip install --no-cache-dir -r /app/requirements.txt

# Copy application code (use chown so non-root user can read/write data folder)
COPY . /app

# Create a non-root user and ensure the data directory exists & is writable
RUN groupadd -r appuser && useradd -r -g appuser appuser \
 && mkdir -p /app/app/data \
 && chown -R appuser:appuser /app

USER appuser

# Expose the port Uvicorn will run on
EXPOSE 8000

# Healthcheck (runs as root in some Docker runtimes; we use wget)
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s \
  CMD wget -q -O- http://127.0.0.1:8000/metrics || exit 1

# Run Uvicorn. Use host 0.0.0.0 to accept external connections. --proxy-headers useful behind proxies.
# If main app lives at app/main.py and your FastAPI instance is named `app`, use: app.main:app
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "1", "--proxy-headers"]
