FROM python:3.12-slim-bookworm AS builder
WORKDIR /build
RUN python -m venv /opt/venv
COPY requirements.txt .
RUN /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

FROM python:3.12-slim-bookworm AS runtime
ENV PATH="/opt/venv/bin:$PATH"
    #This part sets environment variables to prevent Python from writing .pyc files and to ensure that output is sent directly to the terminal without buffering
ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1
WORKDIR /app
    #This part creates a non-root user and group with specific IDs to run the application securely
RUN groupadd --gid 10001 app && useradd --uid 10001 --gid app --no-create-home app
    #This part copies the builder stage's opt/venv to the runtime stage's opt/venv
COPY --from=builder /opt/venv /opt/venv
    #This part copies the application code to the runtime stage
COPY app.py .
USER 10001:10001
EXPOSE 8080
CMD ["waitress-serve", "--listen=0.0.0.0:8080", "app:app"]
