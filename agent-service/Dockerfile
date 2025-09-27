# =======
# Builder
# =======
FROM python:3.13-slim as builder

# Install uv.
COPY --from=ghcr.io/astral-sh/uv:0.8.22 /uv /uvx /bin/

# Set project directory in builder.
WORKDIR /srv

# Install packages.
ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --locked --no-install-project --no-editable

# Copy project files.
ADD . .

# Build and install the project in virtual environment.
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --locked --no-editable


# ======
# Runner
# ======
FROM python:3.13-slim

# Set project directory in builder.
WORKDIR /srv

# Copy virtual environment from builder.
COPY --from=builder --chown=app:app /srv/.venv .venv

# Run project in runner container.
CMD .venv/bin/start-agent-service
