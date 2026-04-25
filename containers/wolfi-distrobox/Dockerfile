FROM cgr.dev/chainguard/wolfi-base:latest

RUN apk update && apk add --no-cache \
    neovim \
    gh \
    sudo \
    shadow \
    restic \
    tesseract \
    tesseract-lang-eng \
    git 

# Create user (important for Distrobox)
RUN adduser -D -u 1000 user && \
    echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/user

USER user
WORKDIR /home/user

CMD ["/bin/bash"]
