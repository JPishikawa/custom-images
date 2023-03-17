FROM quay.io/modh/odh-generic-data-science-notebook:v2

# Install Python packages and Jupyterlab extensions from poetry.lock
COPY poetry.lock pyproject.toml ./

RUN echo "Installing softwares and packages" && micropipenv install && rm -f ./poetry.lock

# Fix permissions to support pip in Openshift environments
RUN chmod -R g+w /opt/app-root/lib/python3.9/site-packages && \
    fix-permissions /opt/app-root -P
