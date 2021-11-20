# for format details, see https://docs.docker.com/engine/reference/builder/

# select image
ARG TENSORFLOW_VERSION="latest"
FROM tensorflow/tensorflow:${TENSORFLOW_VERSION}
LABEL author="dariocurr@gmail.com" maintainer="dariocurr@gmail.com" version="0.0.1"

# create vscode user
RUN groupadd --gid 1000 vscode \
	&& useradd --uid 1000 --gid 1000 -m vscode \
	&& apt -qq update \
	&& apt -qq upgrade -y \
	&& apt -qq install -y sudo \
	&& echo vscode ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/vscode \
	&& chmod 0440 /etc/sudoers.d/vscode

# install git
RUN apt -qq install git -y

# allow the vscode user to pip install globally w/o sudo
ENV PIP_TARGET=/usr/local/pip-global
ENV PYTHONPATH=${PIP_TARGET}:${PYTHONPATH}
ENV PATH=${PIP_TARGET}/bin:${PATH}
RUN if ! cat /etc/group | grep -e "^pip-global:" > /dev/null 2>&1; then groupadd -r pip-global; fi \
	&& usermod -a -G pip-global vscode \
	&& umask 0002 && mkdir -p ${PIP_TARGET} \
	&& chown :pip-global ${PIP_TARGET} \
	&& ( [ ! -f "/etc/profile.d/00-restore-env.sh" ] || sed -i -e "s/export PATH=/export PATH=\/usr\/local\/pip-global:/" /etc/profile.d/00-restore-env.sh )

# update pip and install packages
COPY requirements*.txt tmp/pip-tmp/
RUN pip --no-cache-dir -q install --upgrade pip \
	&& pip --no-cache-dir -q install -r tmp/pip-tmp/requirements.txt -r tmp/pip-tmp/requirements-dev.txt \
	&& rm -rf tmp/pip-tmp

# install zsh
USER vscode
WORKDIR /home/vscode/
RUN sudo apt -qq install zsh -y \
	&& git clone -q https://github.com/zsh-users/antigen.git .antigen
COPY .devcontainer/.zshrc .