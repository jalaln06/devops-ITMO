# syntax=docker/dockerfile:1
ARG GO_VERSION=1.19
ARG CGO_ENABLED=0
ARG GOOS=linux

# Build the application from source
FROM golang:${GO_VERSION}-alpine AS base
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY *.go ./

FROM base as build-service
RUN CGO_ENABLED=${CGO_ENABLED} GOOS=${GOOS} go build -o /docker-gs-ping

#FROM base as build-client
#RUN CGO_ENABLED=${CGO_ENABLED} GOOS=${GOOS} go build -o /docker-gs-ping/client


# Run the tests in the container
FROM build AS run-test
RUN go test -v ./...


# Deploy the application binary into a lean image
FROM scratch AS release
WORKDIR /
COPY --from=build-service /docker-gs-ping /docker-gs-ping
#COPY --from=build-client /docker-gs-ping/client /docker-gs-ping/client
EXPOSE 8080
USER nonroot:nonroot
ENTRYPOINT ["/docker-gs-ping"]

#FROM scratch AS release-client
#WORKDIR /
#COPY --from=build-client /docker-gs-ping/client /docker-gs-ping/client
#ENTRYPOINT ["/docker-gs-ping/client"]
