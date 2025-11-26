FROM node:22 AS builder

WORKDIR /app/medusa

COPY . . 

RUN rm -rf node_modules

RUN apt-get update && apt-get install -y python3 python3-pip python-is-python3

RUN yarn install --loglevel=error

RUN yarn run build


FROM node:22

ARG COOKIE_SECRET
ARG JWT_SECRET
ARG STORE_CORS
ARG ADMIN_CORS
ARG AUTH_CORS
ARG DISABLE_ADMIN
ARG WORKER_MODE
ARG PORT
ARG DATABASE_URL
ARG REDIS_URL
ARG BACKEND_URL

RUN echo "${PORT}"

WORKDIR /app/medusa

RUN mkdir .medusa

RUN apt-get update && apt-get install -y python3 python3-pip python-is-python3

COPY --from=builder /app/medusa/.medusa ./.medusa

# ADD THIS LINE - Copy config file to the server directory
COPY --from=builder /app/medusa/medusa-config.js ./.medusa/server/medusa-config.js

WORKDIR /app/medusa/.medusa/server

RUN yarn install --production

COPY migrations.sh /app/medusa/.medusa/server/migrations.sh
RUN chmod +x /app/medusa/.medusa/server/migrations.sh
RUN /app/medusa/.medusa/server/migrations.sh

RUN rm /app/medusa/.medusa/server/migrations.sh

CMD ["yarn", "run", "start"]