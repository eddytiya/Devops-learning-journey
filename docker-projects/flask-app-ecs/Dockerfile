# Stage 1: Build dependencies
FROM python:3.9 AS builder

WORKDIR /app

COPY . .

RUN pip install --no-cache-dir -r requirements.txt


# Stage 2: Smaller runtime image
FROM python:3.9-slim

WORKDIR /app

COPY --from=builder /usr/local /usr/local
COPY --from=builder /app /app

EXPOSE 80

ENTRYPOINT ["python", "run.py"]
