# use redhat-rhel8 as base image
FROM registry.access.redhat.com/ubi8/ubi:latest

# install runtime
RUN yum install python3.8  -y
RUN which python3

# setup working directory
ENV API_HOME=/var/api
WORKDIR $API_HOME

# virtualenv
ENV VIRTUAL_ENV=/opt/venv
RUN python3 -m pip install virtualenv
RUN python3 -m virtualenv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

# install package
COPY . .
RUN pip install --upgrade pip
RUN pip install . -v
RUN pip list -v
ENV PYTHONPATH="$VIRTUAL_ENV/lib/python3.8/site-packages:$VIRTUAL_ENV/lib64/python3.8/site-packages:$PYTHONPATH"

# copy source code
COPY . .

EXPOSE 8080

CMD echo $PYTHONPATH & \
    python -c "import sys; print(sys.path)" & \
    python -c "import sysconfig; print(sysconfig.get_paths()['purelib'])" & \
    python -m uvicorn your_package_name.server:app --host 0.0.0.0 --port 8080
