FROM node:22

WORKDIR /workspace

RUN npm install -g @openai/codex

RUN echo 'export PS1="\u@\h:\w (\$(git branch 2>/dev/null | grep \"^\*\" | sed \"s/* //\"))\\$ "' >> /home/node/.bashrc

USER node