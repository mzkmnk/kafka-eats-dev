FROM node:20-alpine AS base

FROM base AS builder

RUN apk add --no-cache gcompat

# pnpmのインストール
RUN npm install -g pnpm

WORKDIR /app

COPY package*json tsconfig.json src ./

RUN pnpm install --no-frozen-lockfile

RUN pnpm run build

FROM base AS runner

WORKDIR /app

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 hono

COPY --from=builder --chown=hono:nodejs /app/node_modules /app/node_modules
COPY --from=builder --chown=hono:nodejs /app/dist /app/dist
COPY --from=builder --chown=hono:nodejs /app/package.json /app/package.json

USER hono
EXPOSE 3000

CMD ["node", "dist/index.js"]