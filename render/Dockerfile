FROM node:20-alpine AS web
RUN apk add --no-cache bash
WORKDIR /src/web
COPY web/package*.json ./
RUN npm ci --legacy-peer-deps
COPY web/ ./
RUN npm run build
FROM golang:1.23-alpine AS build
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
COPY --from=web /src/web/dist ./web/dist
RUN CGO_ENABLED=0 go build -trimpath -ldflags='-s -w' -o /out/polaris-console .
FROM alpine:3.20
RUN apk add --no-cache ca-certificates tzdata
WORKDIR /root
COPY --from=build /out/polaris-console /root/polaris-console
COPY --from=build /src/web /root/web
COPY render/polaris-console.yaml /root/polaris-console.yaml
EXPOSE 8080
CMD ["/root/polaris-console"]
