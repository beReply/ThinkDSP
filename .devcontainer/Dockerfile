FROM mcr.microsoft.com/devcontainers/miniconda

# Copy environment.yml (if found) to a temp location so we update the environment. Also
# copy "noop.txt" so the COPY instruction does not fail if no environment.yml exists.
COPY environment.yml*  /tmp/conda-tmp/
RUN if [ -f "/tmp/conda-tmp/environment.yml" ]; then umask 0002 && /opt/conda/bin/conda env update -n base -f /tmp/conda-tmp/environment.yml; fi \
    && rm -rf /tmp/conda-tmp

# Install python 3.9
# 原本是安装 python 3.6，但是后来改为安装 python 3.9，因为有些包不支持 python 3.6
RUN conda install -y python=3.9 -c conda-forge \
    && pip install --no-cache-dir pipx \
    && pipx reinstall-all

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>
