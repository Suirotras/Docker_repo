# syntax=docker/dockerfile:1
FROM python:3.10.8-buster

RUN python3 -m pip install -U pip && \
    python3 -m pip install -U pandas biopython pyfastx argparse session_info

ENTRYPOINT [ "/usr/local/bin/python3"]