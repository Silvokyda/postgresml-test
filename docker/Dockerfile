FROM nvidia/cuda:12.1.1-devel-ubuntu22.04
ENV PATH="/usr/local/cuda/bin:${PATH}"
RUN apt update && \
	apt install -y \
		lsb-release \
		curl \
		ca-certificates \
		gnupg \
		coreutils \
		sudo \
		openssl
RUN echo "deb [trusted=yes] https://apt.postgresml.org $(lsb_release -cs) main" > /etc/apt/sources.list.d/postgresml.list
RUN echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list
RUN curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | tee /etc/apt/trusted.gpg.d/apt.postgresql.org.gpg >/dev/null

ENV TZ=UTC
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update -y && apt install git postgresml-15 postgresml-dashboard -y
RUN git clone --branch v0.4.4 https://github.com/pgvector/pgvector && \
cd pgvector && \
echo "trusted = true" >> vector.control && \
	make && \
	make install

COPY entrypoint.sh /app/entrypoint.sh
COPY dashboard.sh /app/dashboard.sh

COPY --chown=postgres:postgres local_dev.conf /etc/postgresql/15/main/conf.d/01-local_dev.conf
COPY --chown=postgres:postgres pg_hba.conf /etc/postgresql/15/main/pg_hba.conf

ENTRYPOINT ["bash", "/app/entrypoint.sh"]
