FROM quay.io/modh/odh-generic-data-science-notebook:v2 as builder

# Install Python packages and Jupyterlab extensions from poetry.lock
COPY poetry.lock pyproject.toml ./

RUN echo "Installing softwares and packages" && micropipenv install && rm -f ./poetry.lock

FROM localhost/ubi-micro-python:latest as runner
WORKDIR /opt/app-root/bin

COPY utils utils/
COPY fix-permissions start-notebook.sh ./
COPY --from=builder /opt/app-root/lib/python3.9/site-packages /opt/app-root/lib/python3.9/site-packages
COPY --from=builder /opt/app-root/share /opt/app-root/share
COPY --from=builder /opt/app-root/bin ./


# Fix permissions to support pip in Openshift environments
RUN ln -s /bin/python3.9 /usr/local/bin/python && \
    ln -s /bin/python3.9 /usr/local/bin/python3

ENV PATH $PATH:bin:/opt/app-root/bin
ENV PYTHONPATH /opt/app-root/lib/python3.9/site-packages
ENV JUPYTERLAB_DIR /opt/app-root/share/jupyter/lab


RUN chmod -R g+w /opt/app-root/lib/python3.9/site-packages && \
    chmod g+x ./fix-permissions && \
    chmod g+x ./start-notebook.sh && \
    fix-permissions /opt/app-root -P && \
    chown -R 1001:0 /opt/app-root

USER 1001:0

WORKDIR /opt/app-root/src
ENV HOME /opt/app-root/src
RUN jupyter serverextension enable --py jupyterlab_s3_browser
RUN sed -i -e "s/Python.*/$(python --version)\",/" /opt/app-root/share/jupyter/kernels/python3/kernel.json

ENTRYPOINT ["start-notebook.sh"]
