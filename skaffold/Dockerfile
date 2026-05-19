FROM gcr.io/cloud-builders/kubectl

RUN mkdir -p /builder/bin && \
  apt-get update && \
  apt-get install -y curl && \
  curl -sSLo /builder/bin/skaffold https://storage.googleapis.com/skaffold/releases/latest/skaffold-linux-amd64 && \
  chmod +x /builder/bin/skaffold && \
  apt-get remove --purge -y curl && \
  apt-get --purge -y autoremove && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

ENV PATH=/builder/bin/:$PATH
ENV DOCKER_CONFIG=/builder/home/.docker

COPY skaffold.bash /builder/skaffold.bash
RUN chmod +700 /builder/skaffold.bash

# Create a non-root user and grant ownership of the builder workspace so the
# entrypoint does not run as root (CWE-269: Improper Privilege Management).
RUN groupadd --system --gid 1000 skaffold && \
    useradd --system --uid 1000 --gid skaffold --home-dir /builder/home --shell /usr/sbin/nologin skaffold && \
    mkdir -p /builder/home/.docker && \
    chown -R skaffold:skaffold /builder

USER skaffold:skaffold

ENTRYPOINT ["/builder/skaffold.bash"]
