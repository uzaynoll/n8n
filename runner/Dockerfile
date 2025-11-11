# runner/Dockerfile
FROM mcr.microsoft.com/playwright:v1.51.1-jammy

# install git (to clone your repo)
USER root
RUN apt-get update && apt-get install -y git && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# --- Steps 1–5: npm global prefix + PATH + install playwright CLI ---
# 1) mkdir -p /home/node/.npm-global
# 2) npm config set prefix /home/node/.npm-global
# 3) export PATH=/home/node/.npm-global/bin:$PATH
# 4) npm install -g playwright
# 5) export PATH=/home/node/.npm-global/bin:$PATH (persisted via ENV)
ENV NPM_GLOBAL=/home/node/.npm-global
ENV PATH=$NPM_GLOBAL/bin:$PATH
RUN mkdir -p "$NPM_GLOBAL" \
 && npm config set prefix "$NPM_GLOBAL" \
 && npm install -g playwright

# Install Google Chrome for Testing via Playwright (works on jammy/glibc)
# This pulls the Chrome channel used by Playwright (not system google-chrome-stable).
RUN npx playwright install chrome --with-deps

# Network port for MCP server
EXPOSE 8931

# 6) Run the MCP server on port 8931
# Keep your flexible ENTRYPOINT; pass the command via CMD
ENTRYPOINT ["bash","-lc"]
CMD ["npx @playwright/mcp@0.0.38 --port 8931 --isolated --no-sandbox --ignore-https-errors --headless"]
