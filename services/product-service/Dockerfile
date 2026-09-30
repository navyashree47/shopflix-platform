FROM python:3.10-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Fix OpenSSL CVEs (CVE-2026-75804, CVE-2026-84782)
RUN apt-get update \
    && apt-get install -y --only-upgrade openssl libssl3t64 openssl-provider-legacy \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt .

# Install deps, then remove build tools the app doesn't need at runtime.
# Fixes wheel (CVE-2026-24049) and the jaraco.context copy vendored in setuptools (CVE-2026-23949)
RUN pip install --no-cache-dir -r requirements.txt \
    && pip uninstall -y setuptools wheel

COPY app ./app

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]