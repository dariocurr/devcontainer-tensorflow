# for format details, see https://docs.docker.com/engine/reference/builder/

ARG VARIANT="latest"

# select image
FROM tensorflow/tensorflow:${VARIANT}
LABEL author="dariocurr@gmail.com" maintainer="dariocurr@gmail.com" version="0.0.1"

# read and create user
ARG USER
RUN groupadd --gid 1000 ${USER} \
	&& useradd --uid 1000 --gid 1000 -m ${USER} \
	&& apt -qq update \
	&& apt -qq upgrade -y \
	&& apt -qq install -y sudo \
	&& echo ${USER} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USER} \
	&& chmod 0440 /etc/sudoers.d/${USER}

# install git and zsh
RUN apt -qq install git zsh -y

# change user
USER ${USER}

# update pip and install packages
COPY --chown=${USER} ./requirements*.txt /tmp/pip-tmp/
RUN pip --no-cache-dir --disable-pip-version-check -q install --upgrade pip; \
	pip --no-cache-dir -q install -r /tmp/pip-tmp/requirements.txt -r /tmp/pip-tmp/requirements-dev.txt; \
	rm -r /tmp/pip-tmp;

# add zsh configuration and replace oh-my-zsh with antigen
WORKDIR /home/${USER}/
RUN git clone -q https://github.com/zsh-users/antigen.git .antigen;
ADD --chown=${USER} https://gist.githubusercontent.com/dariocurr/9792ae1fb6ddf736d8fea9e20eb13e26/raw/ .zshrc