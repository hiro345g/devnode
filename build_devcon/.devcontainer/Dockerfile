FROM mcr.microsoft.com/devcontainers/typescript-node:18-bullseye

RUN apt-get update && apt-get -y install bash-completion \
    && mkdir -p /home/node/repo \
    && mkdir -p /home/node/.vscode-server/extensions \
    && chown node:node /home/node/repo/ \
    && chown -R node:node /home/node/.vscode-server/
