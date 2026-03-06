FROM python:3.13-alpine AS builder

# Install uv
COPY --from=ghcr.io/astral-sh/uv:0.10.6 /uv /uvx /bin/

WORKDIR /app

# Install dependencies in cache layer
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --locked --no-install-project --no-editable

COPY . /app

# Sync project and install it
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --locked --no-editable --no-dev

FROM python:3.13-alpine

RUN addgroup -S -g 1000 app && adduser -S -u 1000 -G app app

# Copy environment but not the source code
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# Switch to non-root user
USER app

# Expose correct port
EXPOSE 22112

# Run the application
CMD ["/app/.venv/bin/study-tracker-api"]
