FROM public.ecr.aws/docker/library/python:3.11-slim

WORKDIR /app

# Apply warning filtering from process start, before app imports happen.
ENV PYTHONWARNINGS="ignore:.*doesn't match a supported version.*"

COPY requirements.txt requirements.txt

RUN pip install --no-cache-dir --upgrade pip setuptools wheel && \
    pip install --no-cache-dir -r requirements.txt && \
    pip install --no-cache-dir aws-opentelemetry-distro==0.10.1 && \
    pip uninstall -y chardet || true && \
    pip check

COPY . .

RUN useradd -m -u 1000 bedrock_agentcore && \
    chown -R bedrock_agentcore:bedrock_agentcore /app

USER bedrock_agentcore

EXPOSE 8080
EXPOSE 8000

#HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
#  CMD curl -f http://localhost:8080/ping || exit 1

# No Docker HEALTHCHECK here; AgentCore already relies on /ping semantics.
CMD ["opentelemetry-instrument", "python", "-m", "weather_agent"]
