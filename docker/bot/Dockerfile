FROM golang:1.12-alpine as builder

# Copy content of project root folder
COPY ./ /app

WORKDIR /app

# 1.
# Create the user and group files that will be used in the running container to
# run the process as an unprivileged user.
# 2.
# Install the Certificate-Authority certificates for the app to be able to make
# calls to HTTPS endpoints.
# Git is required for fetching the dependencies.
# 3.
# Fetch dependencies first; they are less susceptible to change on every build
# and will therefore be cached for speeding up the next build
# 4.
# Build the executable to `/app/bot`.
RUN ls -halt && \
    mkdir /user && \
    echo 'nobody:x:65534:65534:nobody:/:' > /user/passwd && \
    echo 'nobody:x:65534:' > /user/group && \
    apk add --no-cache ca-certificates git && \
    go mod download && \
    CGO_ENABLED=0 go build -o bot .

FROM scratch AS final

# Import the user and group files from the first stage.
COPY --from=builder /user/group /user/passwd /etc/
# Import the compiled executable from the first stage.
COPY --from=builder /app /app

# Perform any further action as an unprivileged user.
USER nobody:nobody

CMD ["/app/bot"]
