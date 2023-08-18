FROM python:3.11.3-slim as builder

# - libre2-9 is needed by pyre2
# - librdkafka is needed by confluent_kafka
# - default-libmysqlclient-dev is needed by mysqlclient
# hadolint ignore=DL3008
RUN apt-get update && apt-get install -y --no-install-recommends \
  apt-utils \
  wget \
  unzip \
  build-essential \
  libre2-9 \
  default-libmysqlclient-dev

# Install librdkafka for confluent_kafka==2.1.1
WORKDIR /src/librdkafka
RUN wget -nv https://github.com/confluentinc/librdkafka/archive/refs/tags/v2.1.1.zip && \
  unzip -q v2.1.1.zip && \
  rm v2.1.1.zip

WORKDIR /src/librdkafka/librdkafka-2.1.1
RUN ./configure --install-deps && make && make install

FROM python:3.11.3-slim

COPY --from=builder /usr/lib/*-linux-gnu/libmysql* /usr/lib/*-linux-gnu/
COPY --from=builder /usr/lib/*-linux-gnu/libmaria* /usr/lib/*-linux-gnu/
COPY --from=builder /usr/bin/mysql_config /usr/bin/
COPY --from=builder /usr/lib/*-linux-gnu/libre2* /usr/lib/*-linux-gnu/
COPY --from=builder /usr/local/lib/librdkafka* /usr/local/lib/
