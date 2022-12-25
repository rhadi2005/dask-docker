ARG BASE_CONTAINER=condaforge/mambaforge:latest
FROM $BASE_CONTAINER

ARG python=3.8
ARG release

SHELL ["/bin/bash", "-c"]

ENV PATH /opt/conda/bin:$PATH
ENV PYTHON_VERSION=${python}
ENV DASK_VERSION=${release}

RUN mamba install -y \
    "mamba>=0.27.0" \
    python=${PYTHON_VERSION} \
    nomkl \
    cmake \
    dask=${DASK_VERSION} \
    cachey \
    streamz \
    && mamba clean -tipy \
    && find /opt/conda/ -type f,l -name '*.a' -delete \
    && find /opt/conda/ -type f,l -name '*.pyc' -delete \
    && find /opt/conda/ -type f,l -name '*.js.map' -delete \
    && find /opt/conda/lib/python*/site-packages/bokeh/server/static -type f,l -name '*.js' -not -name '*.min.js' -delete \
    && rm -rf /opt/conda/pkgs

RUN mkdir -p /opt/app && \
    mkdir -p /opt/workspace

COPY requirements.txt /opt/app/
RUN /opt/conda/bin/conda init bash && \
    source /root/.bashrc && \ 
    /opt/conda/bin/conda env list && \
#    /opt/conda/bin/conda activate base && \
    /opt/conda/bin/conda install -y -c conda-forge --file /opt/app/requirements.txt

COPY prepare.sh /usr/bin/prepare.sh
#COPY environment.yml /opt/app/environment.yml

EXPOSE 8786-8787

WORKDIR /opt/workspace
ENTRYPOINT ["tini", "-g", "--", "/usr/bin/prepare.sh"]
