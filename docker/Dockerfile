FROM fredrikfornwall/rust-static-builder as builder
LABEL maintainer "chevdor@gmail.com"
LABEL description="This is the build stage for Polkadot. Here we create the binary."

ARG PROFILE=release
WORKDIR /polkadot

COPY . /polkadot

# RUN apt-get update && \
# 	apt-get upgrade -y && \
# 	apt-get install -y cmake pkg-config libssl-dev git clang
# RUN curl https://sh.rustup.rs -sSf | sh -s -- -y && \
#         export PATH=$PATH:$HOME/.cargo/bin && \
RUN        scripts/init.sh && \
        cargo build --$PROFILE --bins
		#--target x86_64-unknown-linux-musl

# ===== SECOND STAGE ======

FROM alpine
LABEL maintainer "chevdor@gmail.com"
LABEL description="This is the 2nd stage: a very small image where we copy the Polkadot binary."
ARG PROFILE=release
COPY --from=builder /polkadot/target/$PROFILE/polkadot /usr/local/bin

# RUN mv /usr/share/ca* /tmp && \
# 	rm -rf /usr/share/*  && \
# 	mv /tmp/ca-certificates /usr/share/ && \
# 	rm -rf /usr/lib/python* && \
RUN	useradd -m -u 1000 -U -s /bin/sh -d /polkadot polkadot && \
	mkdir -p /polkadot/.local/share/polkadot && \
	chown -R polkadot:polkadot /polkadot/.local && \
	ln -s /polkadot/.local/share/polkadot /data
	# rm -rf /usr/bin /usr/sbin

USER polkadot
EXPOSE 30333 9933 9944 9615
VOLUME ["/data"]

CMD ["/usr/local/bin/polkadot"]
