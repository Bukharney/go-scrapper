# Stage 1: Modules caching
FROM golang:1.22 as modules
COPY go.mod go.sum /modules/
WORKDIR /modules
RUN go mod download

# Stage 2: Build
FROM golang:1.22 as builder
COPY --from=modules /go/pkg /go/pkg
COPY . /workdir
WORKDIR /workdir
# Install playwright cli with right version for later use
RUN PWGO_VER=$(grep -oE "playwright-go v\S+" /workdir/go.mod | sed 's/playwright-go //g') \
    && go install github.com/playwright-community/playwright-go/cmd/playwright@${PWGO_VER}
# Build your app
RUN GOOS=linux GOARCH=amd64 go build -o /bin/myapp

# Stage 3: Final
FROM ubuntu:jammy
COPY --from=builder /go/bin/playwright /bin/myapp /
RUN apt-get update && apt-get install -y ca-certificates tzdata \
    # Install dependencies and cromium for playwright
    && /playwright install --with-deps chromium \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y bash curl && curl -1sLf \
    'https://dl.cloudsmith.io/public/infisical/infisical-cli/setup.deb.sh' | bash \
    && apt-get update && apt-get install -y infisical
    

CMD ["infisical", "run", "--", "/myapp"]