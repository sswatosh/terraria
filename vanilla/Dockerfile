FROM alpine:3.15.0 AS base

RUN apk add --update-cache unzip

ENV DL_LINK=https://terraria.org/api/download/pc-dedicated-server/terraria-server-1445.zip
ENV DL_VERSION=1445
ENV DL_FILE=terraria-server-1445.zip

ADD $DL_LINK /$DL_FILE

# Download and unpack
ADD $DL_LINK /$DL_FILE
RUN unzip /$DL_FILE -d /terraria && \
  mkdir /terraria-server && \
  mv /terraria/$DL_VERSION/Linux/* /terraria-server && \
  chmod +x /terraria-server/TerrariaServer.exe && \
  chmod +x /terraria-server/TerrariaServer.bin.x86_64

FROM mono:6.10.0.104-slim

# documenting ports
EXPOSE 7777

COPY --from=base /terraria-server /terraria-server
COPY bootstrap.sh /terraria-server

RUN groupadd terraria -g 1000 && \
  useradd terraria -m -u 1000 -g 1000 && \
  apt-get update -y && \
  apt-get install -y pcregrep && \
  rm -rf /var/lib/apt/lists* /tmp* && \
  mkdir /terraria-server/tmp && \
  chown terraria:terraria /terraria-server/tmp

WORKDIR /home/terraria/server

RUN chown -R terraria:terraria .

USER terraria

VOLUME [ "/home/terraria/server/worlds"  ]
VOLUME [ "/home/terraria/server/logs"    ]
VOLUME [ "/home/terraria/server/config"  ]

RUN mkdir worlds && \
  mkdir logs && \
  mkdir config

ENTRYPOINT [ "/bin/sh", "/terraria-server/bootstrap.sh" ]
