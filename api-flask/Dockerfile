FROM python:3.12-slim

WORKDIR /app
ENV PYTHONUNBUFFERED=1

# Copy app
COPY main.py .

# Install runtime dependency
RUN pip install --no-cache-dir "flask>=2.0"

EXPOSE 8080

CMD ["python", "main.py"]