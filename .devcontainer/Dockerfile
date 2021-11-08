# select image
ARG TENSORFLOW_VERSION
FROM tensorflow/tensorflow:${TENSORFLOW_VERSION}

# create vscode user
RUN groupadd --gid 1000 vscode \
	&& useradd --uid 1000 --gid 1000 -m vscode \
	&& apt-get update \
	&& apt upgrade -y \
	&& apt-get install -y sudo \
	&& echo vscode ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/vscode \
	&& chmod 0440 /etc/sudoers.d/vscode

# install git
RUN apt install git zsh -y

# install ohmyzsh
USER vscode
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
COPY .devcontainer/.zshrc .

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
COPY requirements*.txt /tmp/pip-tmp/
RUN pip install --upgrade pip \
	&& pip --no-cache-dir install -r /tmp/pip-tmp/requirements.txt -r /tmp/pip-tmp/requirements-dev.txt \
	&& rm -rf /tmp/pip-tmp