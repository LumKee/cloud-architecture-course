FROM node:14.15-alpine as base

FROM base as builder

RUN mkdir -p /usr/share/app
WORKDIR /usr/share/app

# Repository directory
COPY ./package.json ./package.json
COPY ./yarn.lock yarn.lock

# Service directory
RUN mkdir -p api
COPY ./packages/api/src ./packages/api/src
COPY ./packages/api/tsconfig.json ./packages/api/tsconfig.json
COPY ./packages/api/package.json ./packages/api/package.json

# Install all dependencies for build
RUN yarn --frozen-lockfile

RUN yarn workspace api build

# Clean dependencies for production
RUN yarn --frozen-lockfile --production && yarn cache clean

FROM base as runtime

RUN mkdir -p /usr/share/app
WORKDIR /usr/share/app

COPY --from=builder /usr/share/app/package.json ./package.json
COPY --from=builder /usr/share/app/node_modules node_modules
COPY --from=builder /usr/share/app/packages/api/build ./build

EXPOSE 9999
CMD ["node", "build/index.js"]
