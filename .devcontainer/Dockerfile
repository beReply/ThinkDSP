FROM mcr.microsoft.com/devcontainers/miniconda

# 容器内的项目目录：与 docker-compose.yml 的挂载点、PyCharm 的解释器配置保持一致
WORKDIR /workspaces/ThinkDSP

# ---------------------------------------------------------------------------
# 1) 根据 environment.yml 创建名为 ThinkDSP 的独立 conda 环境
#    （environment.yml 里 name: ThinkDSP，Python 3.12）
#    单独 COPY 这一层，是为了 environment.yml 不变时复用缓存，避免重建镜像
#    时重新求解依赖。
# ---------------------------------------------------------------------------
COPY environment.yml /tmp/conda-tmp/environment.yml
RUN /opt/conda/bin/conda env create -f /tmp/conda-tmp/environment.yml \
    && /opt/conda/bin/conda clean -afy \
    && rm -rf /tmp/conda-tmp

# ---------------------------------------------------------------------------
# 2) 安装开发依赖，并以 editable 模式安装本项目
#    environment-dev.yml 里的 `-e .` 相对于 WORKDIR 解析，所以先复制项目元数据
#    和 thinkdsp 包。运行期项目目录会被 bind mount 覆盖，editable 安装指向的
#    /workspaces/ThinkDSP 始终对应你本地的源码。
# ---------------------------------------------------------------------------
COPY environment-dev.yml pyproject.toml README.md LICENSE /workspaces/ThinkDSP/
COPY thinkdsp /workspaces/ThinkDSP/thinkdsp
RUN /opt/conda/bin/conda env update -n ThinkDSP -f environment-dev.yml \
    && /opt/conda/bin/conda clean -afy \
    && rm -rf ~/.cache/pip

# 若镜像以 root 构建、运行期却切换到 vscode 用户，保证环境目录可写
# （PyCharm 里用 pip 装包、conda install 都需要）
RUN if [ "$(id -u)" = "0" ] && id vscode >/dev/null 2>&1; then \
        chown -R vscode:vscode /opt/conda/envs/ThinkDSP; \
    fi

# 交互式 shell 里可以直接 conda activate ThinkDSP
RUN /opt/conda/bin/conda init bash

# 让默认的 python / pip / pytest 指向 ThinkDSP 环境（PyCharm 仍使用显式解释器路径）
ENV PATH=/opt/conda/envs/ThinkDSP/bin:$PATH

# 调试时 stdout/stderr 实时输出，断点日志不会卡在缓冲区
ENV PYTHONUNBUFFERED=1

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>
